---
title: Error Handling
tags: 
 - Troubleshooting
 - Error Handling
description: How to handle errors from Stripe
---
# Error Handling

### Common Errors:
- **Invalid Token:** If Stripe returns an invalid token, your backend should handle it and provide feedback to the user. Ensure proper validation of the token before proceeding with the charge.
- **Payment Failure:** In case of payment failure from Stripe, do not proceed with the policy purchase. Provide a clear error message to the user.

[Stripe docs](https://docs.stripe.com/error-handling) contain additional information on handling errors. 
