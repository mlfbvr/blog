---
title: Software Development is dead... Long live software development!
date: 2026-02-01
draft: false
tags:
  - blog
---
Software development is dead. We've all heard it. But is it true? My opinion is that it depends on how you define software development.

If for you software development means sitting down with Vim or Visual Studio Code and writing APIs from scratch with ExpressJS, then yes, it's dead. AI can do that. Better? Unlikely, but just as good as you. Faster? Way faster than you.

Today, you tell an AI tool that you need an API to query and manage customer profiles. It will know already which routes you will need. It will know how to secure the API and how to get the most performance out of it. It will know the best structure for the API code. But that's where things will start falling apart for the AI and that's where your experience comes in.

The AI has no way of knowing that for you, a customer is:
- A UUID that's mapped to a phone number to identify the customer across all systems.
- An entry, per UUID, in a MongoDB instance that contains the customer's most up-to-date activity.
- A LDAP entry with profile information like serial numbers or IMEIs.
- Device information in a MySQL database with an IMEI-based index.
- Transactional data in Elastic for different services (mobile, home internet, etc).
- Billing-related data provided by an API that's part of a 3rd party platform.

That's where your experience and "Actual Intelligence" comes in. You know that you need to:
- Extract the UUID for the phone number
- Use that UUID to query the LDAP and MongoDB instances to build the profile.
- Query MySQL with the customer's IMEI to get the device information.
- Use the phone number to get the transactional data.
- Correlate each type of activity to the billing-related data to estimated usage costs for the current billing period.

It's unfair to assume that the AI will know how to build this logic. This is your domain and you can get it right. AI will help you by taking care of the grunt stuff, like writing the CRUD routes for the API.

Let's be honest... Most of us haven't **needed** to write these in a very long time. That stuff hasn't changed in ages. We've been using previous APIs as templates, or even API templates where this is all done for us. The AI just does it faster.

AI-assisted coding tools let us focus on the important business logic that adds value to the API. You can even go further. Tests and documentation are the safeties we as developers rely on, but for customers or the customer operations team? They have no value. AI can do this so instead we can work on training and integrating a model that's going to go through the transactional data and determine what issue made our customer call in the first place.
