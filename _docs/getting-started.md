---
title: Getting Started
tags: 
 - Stripe
 - Embrace API
description: Getting started with Stripe and Embrace
---

# Getting Started

## Prerequisites
- A [Stripe account](https://stripe.com/) with access to the Stripe API.
- API credentials for Embrace API.
- Server-side capability to handle requests to both Stripe and the Embrace API.
  
### Required Technologies
- Frontend: Javascript for Stripe elements.
- Backend: Any server-side language that can interact with both Stripe and the Embrace API.

## Step-by-Step Integration

### Step 1: Install Stripe's SDK
##### Option 1: Install locally using React Stripe.js
**Installation**

Install React Stripe..js and the Stripe.js loader using npm:
```console
npm install --save @stripe/react-stripe-js @stripe/stripe-js
```
**Elements Provider**

The `Elements` provider allows you to use [Element components](https://docs.stripe.com/sdks/stripejs-react#element-components) and access the [Stripe object](https://docs.stripe.com/js/initializing) in any nested component. Render an `Elements` provider at the root of your React app so that it is available everywhere you need it. 

To use the `Elements` provider, call [loadStripe](https://github.com/stripe/stripe-js/blob/master/README.md#loadstripe) from `@stripe/stripe-js` with your publishable key. The `loadStripe` function asynchronously loads the Stripe.js script and initializes a Stripe object. Pass the returned `Promise` to `Elements`.

```js
import {Elements} from '@stripe/react-stripe-js';
import {loadStripe} from '@stripe/stripe-js';

// Make sure to call `loadStripe` outside of a component’s render to avoid
// recreating the `Stripe` object on every render.
const stripePromise = loadStripe('your_test_key');

export default function App() {
  const options = {
    // passing the client secret obtained from the server
    clientSecret: '{{CLIENT_SECRET}}',
  };

  return (
    <Elements stripe={stripePromise} options={options}>
      <CheckoutForm />
    </Elements>
  );
};
```
For additional information on how to use `Elements` visit [Stripe Docs](https://docs.stripe.com/sdks/stripejs-react#elements-provider)

##### Option 2: Use a script tag
**Installation**

Add the Stripe.js module as a script to the `<head>` element of your HTML:
```html 
<head>
  <script src="https://js.stripe.com/v3"></script>
</head>
```
**Stripe.js Constructor**

Set the API publishable key to allow Stripe to tokenize customer information and collect payment details:
```js
var stripe = Stripe('your_testkey_abc123');
```

### Step 2: Add Stripe Elements to Your Checkout Page
Embed Stripe Elements in your frontend code to collect payment details securely:
```js
// Initialize Stripe
const stripe = Stripe('your-publishable-key');
const elements = stripe.elements();

// Create a card input field
const card = elements.create('card');
card.mount('#card-element');

// Handle form submission
const form = document.getElementById('payment-form');
form.addEventListener('submit', async (event) => {
  event.preventDefault();
  
  const { token, error } = await stripe.createToken(card);
  
  if (error) {
    // Display error to the user
    console.error(error.message);
  } else {
    // Pass token to your backend
    handleToken(token);
  }
});
```
### Step 3: Handle the Payment Token on Your Backend
Once the token is generated from Stripe Elements, it will be passed to your backend. Use this token to create a payment or a payment intent using Stripe's API.
```js
// Sample backend code for processing payment (Node.js example)

const stripe = require('stripe')('your-secret-key');

// Handle POST request from frontend
app.post('/charge', async (req, res) => {
  const { token, amount, insurancePolicyId } = req.body;
  
  try {
    // Create a charge or a payment intent
    const charge = await stripe.charges.create({
      amount: amount,
      currency: 'usd',
      source: token, // The token from Stripe Elements
      description: `Payment for insurance policy ${insurancePolicyId}`
    });

    // On success, call the Insurance API
    const insuranceResponse = await purchasePolicy(insurancePolicyId, charge.id);
    
    res.status(200).json({ success: true, insurance: insuranceResponse });
  } catch (error) {
    res.status(500).json({ success: false, error: error.message });
  }
});
```
### Step 4: Use Embrace Partner API to Complete Purchase
Once payment is confirmed, you’ll need to call our API to finalize the policy purchase. The endpoint you’ll use is `/purchase`

**Endpoint:** `POST /purchase`
- **URL:** `https://api.embracepetinsurance.com/api/v2/purchase`
- **Method:** POST
- **Headers:** Include your API key in the header
  
**Request Body:**

| Field                 | Type    | Description                                        |
|:----------------------|:--------|:---------------------------------------------------|
| `insurancePolicyId`   | string  | The ID of the insurance policy being purchased.    |
| `paymentConfirmation` | string  | The payment confirmation ID returned from Stripe.  |
| `amount`              | integer | The amount charged to the customer (in cents).     |

**Example Request:**
```json
{
  "insurancePolicyId": "12345",
  "paymentConfirmation": "ch_1Ftpzp2eZvKYlo2CDHcmgYfi",
  "amount": 5000
}
```

**Example Response:**
```json
{
  "policyNumber": "INS-987654321",
  "status": "active",
  "effectiveDate": "2024-10-23T00:00:00Z",
  "coverage": {
    "type": "auto",
    "premium": 5000,
    "term": "12 months"
  }
}
```
## Error Handling 

### Common Errors:
- **Invalid Token:** If Stripe returns an invalid token, your backend should handle it and provide feedback to the user. Ensure proper validation of the token before proceeding with the charge.
- **Payment Failure:** In case of payment failure from Stripe, do not proceed with the policy purchase. Provide a clear error message to the user.

[Stripe docs](https://docs.stripe.com/error-handling) contain additional information on handling errors. 

## Testing
Use Stripe's [test cards](https://docs.stripe.com/testing) to simulate different payment scenarios. Ensure that your backend properly handles both success and failure cases, and that our Insurance API is correctly called when the payment is successful.

![Stripe test card example](../assets/img/stripe-test-card.jpg)
