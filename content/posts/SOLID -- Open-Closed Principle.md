---
title: SOLID -- Open-Closed Principle
date: 2026-06-17
draft: true
tags:
  - blog
  - solid
  - design_patterns
  - typescript
---

"How can something be open and closed at the same time?"

That's the question everyone asks when they first read the Open-Closed Principle:

> "Software entities should be open for extension, but closed for modification."

It sounds contradictory. Maybe even a bit Zen. But it's actually a straightforward idea once you strip away the jargon: **you should be able to add new behaviour without changing existing, working code.**

---

## The Bad Way: A Switch Statement That Grows Forever

Let's say we're building a payment system. Here's the most natural first pass:

```typescript
type PaymentType = "credit_card" | "paypal";

class PaymentProcessor {
  process(paymentType: PaymentType, amount: number): void {
    if (paymentType === "credit_card") {
      // Authorize via credit card gateway
      console.log(`Processing credit card payment of $${amount}`);
    } else if (paymentType === "paypal") {
      // Redirect to PayPal API
      console.log(`Processing PayPal payment of $${amount}`);
    } else {
      throw new Error(`Unknown payment type: ${paymentType}`);
    }
  }
}
```

At first glance, this looks fine. It works. Tests pass. Ship it.

Then the product team asks for Apple Pay. You go back and add another `else if`. Then Google Pay. Then Klarna. Each time, you open this file and add a branch.

This is the **opposite** of OCP. The class has to be modified every single time the system grows.

---

## Why This Hurts

A few months in, that innocent `if/else` chain becomes a liability:

- **Collateral damage** — fixing a bug in the PayPal branch risks breaking the credit card branch. They're in the same function.
- **Testing bloat** — every new payment type forces you to re-run the entire test suite for this class, not just the new path.
- **Merge conflicts** — two teams adding payment methods at the same time step on each other's toes in the same file.

I've been on the receiving end of this pattern more times than I'd like to admit. It starts small — "just one more `else if`, it's fine" — and before you know it, that file is a 400-line monster nobody wants to touch.

---

## The Fix: Program to an Interface

The better approach is to define an abstraction that each payment method implements independently:

```typescript
interface IPaymentMethod {
  process(amount: number): void;
}
```

Now each payment type lives in its own class:

```typescript
class CreditCard implements IPaymentMethod {
  process(amount: number): void {
    console.log(`Processing credit card payment of $${amount}`);
  }
}

class PayPal implements IPaymentMethod {
  process(amount: number): void {
    console.log(`Processing PayPal payment of $${amount}`);
  }
}

class ApplePay implements IPaymentMethod {
  process(amount: number): void {
    console.log(`Processing Apple Pay payment of $${amount}`);
  }
}
```

And the processor becomes dangerously simple:

```typescript
class PaymentProcessor {
  process(payment: IPaymentMethod, amount: number): void {
    payment.process(amount);
  }
}
```

Using it is just as clean:

```typescript
const processor = new PaymentProcessor();
processor.process(new CreditCard(), 49.99);
processor.process(new ApplePay(), 9.99);
```

---

## What Changed?

The key difference: **extending the system no longer requires modifying existing code.**

Adding Klarna means creating a new file — `class Klarna implements IPaymentMethod` — and nothing else changes. The `PaymentProcessor` stays closed for modification. No risk of regressions in the credit card path. No merge conflicts in a shared switch statement.

---

> **The mental shortcut I use:**
>
> **Bad OCP:** "Every new feature requires editing a giant `if`, `switch`, or existing class."
>
> **Good OCP:** "Every new feature can be added by creating a new implementation that plugs into the existing design."

---

## A Quick Note on Balance

OCP sounds like the answer to everything, and it is — when you know change is coming. The trick is knowing when to reach for it.

If you'll only ever have one or two payment methods, the `if/else` solution is perfectly fine. Premature abstraction has a cost too (more files, more indirection, harder to trace the flow). OCP trades simplicity-for-today against flexibility-for-tomorrow. Pick the right side of that trade based on what you know about your domain.

For me, payment processing is almost always a "I know more methods will show up" domain. Your mileage may vary.

Next up in the SOLID series: the Liskov Substitution Principle — how to make sure those subclasses don't ruin your carefully designed abstraction.
