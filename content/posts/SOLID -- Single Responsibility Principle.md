---
title: SOLID -- Single Responsibility Principle
date: 2026-01-19
draft: false
tags:
  - blog
  - design_patterns
  - solid
---

Ever since I started looking for work, something has become extremely clear -- while I am familiar with the practical aspects of software design and development, a lot of the theoretical patterns are beyond fuzzy.

To address this, I have gone back to studying some of the basic design patterns and principles, starting with SOLID, which came to light in the early 2000s.

## Single Responsibility Principle

The first of these five software design principles is the "Single Responsibility Principle". This principle stats that *an entity should have one and only one reason to change, meaning that a class should have only one job.*

Let's start with this Python example:
```py

class Employee:
	def __init__(self):
		pass

	def create(self, username, password, email):
		# Assumed: database table already exists
		conn = sqlite3.connect(db_path)
		try:
			ensure_table(conn)
			with conn:
				conn.execute(
					"INSERT INTO users (username, password, email) ",
					"VALUES (?, ?, ?)",
					(username, password, email),
				)
				self.send_email(email, "Welcome to XYZ Inc")
		except sqlite3.IntegrityError as e:
			raise ValueError("username or email already exists") from e
		finally:
			conn.close()

	def send_email(self, email, message):
		# Build message
		msg = EmailMessage()
		msg["From"] = "info@xyz.com"
		msg["To"] = email
		msg["Subject"] = "Welcome to XYZ Inc"
		msg.set_content(message)
		
		try:
		    # Connect to the SMTP server (unencrypted)
		    with smtplib.SMTP(SMTP_HOST, SMTP_PORT, timeout=10) as smtp:
		        smtp.ehlo()             # identify ourselves to the SMTP server
		
		        # Send the message
		        smtp.send_message(msg)
		
		    print("Email sent successfully")
		
		except Exception as e:
		    print("Failed to send email:", type(e).__name__, e)

```

This is a basic example gives us what we need to properly understand the principle.

At first glance, everything looks fine -- an Employee is created and an email is sent upon creation to welcome them to the company. But under the *Single Responsibility Principle*, there is an issue: an Employee class should not be responsible for sending an email.

As per the definition of the principle, the Employee class should be responsible only for handling employee operations and, more importantly, should only be changed for reasons related to employee operations. While welcoming the new employee to the company is considered part of these normal operations, sending an email is not.

## A better way

A better implementation would move the email-sending logic to a dedicated entity that would be specifically built to handle that logic. This means that any changes to the email-sending logic would be done in the entity (the class) responsible for that sending the email message. The Employee class shouldn't be changed since none of the employee operations have changed.

The implementation could look like this:
```py

class EmailService:
	def __init__(self):
		pass

	def send_email(self, email, message):
		# Build message
		msg = EmailMessage()
		msg["From"] = "info@xyz.com"
		msg["To"] = email
		msg["Subject"] = "Welcome to XYZ Inc"
		msg.set_content(message)

		try:
		    # Connect to the SMTP server (unencrypted)
		    with smtplib.SMTP(SMTP_HOST, SMTP_PORT, timeout=10) as smtp:
		        smtp.ehlo()             # identify ourselves to the SMTP server
		
		        # Send the message
		        smtp.send_message(msg)
		
		    print("Email sent successfully")
		
		except Exception as e:
		    print("Failed to send email:", type(e).__name__, e)



class Employee:
	def __init__(self):
		pass

	def create(self, username, password, email):
		# Assumed: database table already exists
		conn = sqlite3.connect(db_path)
		try:
			ensure_table(conn)
			with conn:
				conn.execute(
					"INSERT INTO users (username, password, email) ",
					"VALUES (?, ?, ?)",
					(username, password, email),
				)
				self.send_email(email, "Welcome to XYZ Inc")
		except sqlite3.IntegrityError as e:
			raise ValueError("username or email already exists") from e
		finally:
			conn.close()


```

The result is a set of classes that are much more concise and cleaner. Tests become easier to write. Developers looking to fix or update employee-related code will not have to deal with (and risk breaking) the email-related code and vice-versa.

Stay tuned for more articles as I refresh my memory and go through the rest of SOLID and other design patterns and principles.

