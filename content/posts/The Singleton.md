---
title: The Singleton
date: 2025-12-15
draft: true
tags:
  - design_patterns
  - database
  - typestript
  - javascript
---
# Handling MySQL Connections in TypeScript: The Good, the Bad, and the “Too Many Connections” Error

When building a TypeScript application that talks to MySQL, how you manage database connections matters more than it might initially appear. A seemingly harmless pattern can quietly work its way into production and, under load, bring your application down with the dreaded:

> **Error: Too many connections**

In this post, we will look at a **bad but common approach** to handling MySQL connections, why it causes problems, and then contrast it with a **simple, effective approach** that leverages ES modules and the Singleton pattern—without classes.

---

## The Bad Way: Creating a New Connection Per Request

A very common mistake is to create a new MySQL connection every time the database is accessed, without checking whether a connection already exists.

At first glance, this approach looks straightforward and works fine during development or low traffic. The issue only appears once your application sees real usage.

### Example: A New Connection Every Time

```ts
// db.ts
import mysql from "mysql2/promise";

export async function getConnection() {
  return mysql.createConnection({
    host: "localhost",
    user: "app_user",
    password: "secret",
    database: "app_db",
  });
}
```

```ts
// userRepository.ts
import { getConnection } from "./db";

export async function getUsers() {
  const connection = await getConnection();
  const [rows] = await connection.query("SELECT * FROM users");
  return rows;
}
```

### Why This Is a Problem

Each call to `getConnection()` opens a **brand new TCP connection** to MySQL. In a real application:

- Every HTTP request may open one or more database connections
- Connections may not be closed promptly—or at all
- Under concurrent load, MySQL’s connection limit is quickly exhausted

MySQL has a **finite number of allowed connections**, and once they are used up, new queries will fail until existing connections are released.

This approach scales poorly and fails in exactly the situations where you need reliability the most.

---

## The Good Way: A Singleton Connection via ES Modules

A better approach is to **open the connection once and reuse it**.

In Node.js, ES modules are cached after their first import. This gives us a natural and elegant way to implement a Singleton—without classes, frameworks, or complexity.

### Example: A Singleton MySQL Connection

```ts
// db.ts
import mysql from "mysql2/promise";

let connection: mysql.Connection | null = null;

export async function getConnection() {
  if (!connection) {
    connection = await mysql.createConnection({
      host: "localhost",
      user: "app_user",
      password: "secret",
      database: "app_db",
    });
  }

  return connection;
}
```

```ts
// userRepository.ts
import { getConnection } from "./db";

export async function getUsers() {
  const connection = await getConnection();
  const [rows] = await connection.query("SELECT * FROM users");
  return rows;
}
```

### Why This Works

- The `db.ts` module is loaded once and cached by Node.js
- The `connection` variable persists for the lifetime of the process
- Every call to `getConnection()` returns the same MySQL connection
- You avoid opening unnecessary connections and exhausting MySQL resources

This pattern dramatically reduces connection churn and is suitable for many small-to-medium applications.

---

## A Quick Note on Connection Pools

In higher-throughput systems, you will typically want a **connection pool** rather than a single connection. The same Singleton principle applies: create the pool once and reuse it everywhere.

The key takeaway remains the same:

> **Never open a new MySQL connection for every request without a clear lifecycle strategy.**

---

## Final Thoughts

Connection management is one of those details that feels minor—until it isn’t. The “bad” approach often slips into codebases because it is easy and works early on. The “good” approach takes only a few extra lines of code and saves you from production outages later.

By leveraging ES module caching and a simple Singleton pattern, you can keep your TypeScript applications efficient, stable, and friendly to your MySQL server.

If you are seeing “too many connections” errors, this is one of the first places worth revisiting.