---
title: Getting Started
tags: 
 - Stripe
 - Embrace API
description: Getting started with Stripe and Embrace
---

# Getting Started

**Embrace has transitioned to Stripe as our primary payment processor to enhance security, reliability, and flexibility in our payment processes.** This upgrade enables more secure, streamlined checkouts for our customers, while also offering advanced fraud detection and a seamless experience across various devices. For new policy enrollments, payments will now be collected through Stripe using Stripe Elements, an optimized tool designed for a smooth and consistent user experience.

This documentation provides a step-by-step guide to integrating Stripe Elements with the Embrace API, ensuring compliance with our security standards and simplifying payment handling.

#### Quick overview of the policy purchasing flow with our external API and Stripe:
1. **Request Quotes** - `/quotes/fullquote` - This step assumes customer and pet details have already been collected.
   1. Request
      1. Customer contact details
      2. Pet details
      3. Optional analytics
   2. Response
      1. Quote ID
      2. Premium summary
      3. Full pet quote, including coverage details
2. **Request a Checkout** - `/quotes/{quoteId}/checkout` - Triggered after the customer reviews the quote and is ready to proceed to checkout.
   1. Request
      1. Quote ID (included in endpoint URL)
      2. Optional analytics
   2. Response
      1. Stripe Publishable Key
      2. Stripe Client Secret
3. **Initialize Stripe Elements**
   1. Initialize Stripe
      1. Use the Publishable Key from the Checkout response to initialize Stripe.
      2. Use the Client Secret from the Checkout response to set up Stripe Elements.
   2. Collect Payment Method Token from Stripe
      1. After the customer submits payment information, Stripe will attempt to add the payment method and return a Payment Method Token upon success.
4. **Finalize Purchase** - `/quotes/fullquote/{quoteId}/purchase` - After the customer submits payment via Stripe.
   1. Request
      1. Quote ID (included in endpoint URL)
      2. Payment Method Token (returned from Stripe after submission)
   2. Response
      1. Purchase succeeded result
      2. Policy number
   