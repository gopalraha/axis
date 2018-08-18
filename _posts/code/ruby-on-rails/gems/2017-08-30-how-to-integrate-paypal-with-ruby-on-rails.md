---
layout: post
author: yashumittal
title: How To Integrate PayPal With Ruby on Rails
date: 2017-08-30 12:10:00 +0530
categories: code
tags: ruby-on-rails ruby rails integration
description: If you are looking for solutions to implement payment gateways in your Rails app, you've found the right place. Here's a quick guide for PayPal integration.
image: https://i.imgur.com/a8qugwb.png
---

PayPal is a online payments system that supports online money transfers. With the help of this guide you will be able to integrate intercom no time.

We have two different approaches available when implementing PayPal gateway:

## Active Merchant

Active Merchant is a gem extracted from Shopify.

**Pros:**

* Support for multiple payment gateways
* Maintained by Shopify

**Cons:**

* Non-customizable
* Doesn’t support REST API

I'd recommend Active Merchant only when your project is a shop and requires only simple payments.

Instructions for Active Merchant can be found [here](https://jldbasa.github.io/blog/2014/01/25/rails-4-paypal-express-checkout-integration/).

## Official PayPal SDK

PayPal Ruby SDK uses REST API and is recommended and maintained by PayPal.

**Pros:**

* Flexible
* Big capabilities
* Provided by PayPal

**Cons:**

* None

Use official PayPal Ruby SDK every time you need customized solution and single payment method (PayPal).

### Step 1

Install and configure gem following [gem README instructions](https://github.com/paypal/PayPal-Ruby-SDK#installation).

### Step 2

Familiarize yourself with [simple usage guide](https://devtools-paypal.com/guide/pay_paypal/ruby).

### Step 3

How to use this SDK in Rails:

At first, create controller which handles main operations required to process payments:

[code]

It has four main actions for every step:

* **index** handles initial operations (this could be form with amount or simple PayPal button)
* **notify** handles payment object creation (it should have proper `return_url`) and request to paypal for response with link to accept payment process
* **done**, this action is immediately invoked after redirect from paypal acceptance page. In this method we can finalize the whole transaction and additionally save record to our database
* **cancel** takes care of clients who reconsider their payment and cancel acceptance on paypal page

### Step 4

Good idea is to create a Service which handles some of SDK operations:

[code]