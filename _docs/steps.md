---
title: Step-by-Step Integration
tags: 
 - Stripe
 - Embrace API
description: How to implement Stripe and the Embrace API, step-by-step
---

# Step-by-Step Integration

## Step 1: Install Stripe's SDK
##### Option 1: Install locally using React Stripe.js
**Installation**

Install React Stripe.js and the Stripe.js loader using npm:
```console
npm install --save @stripe/react-stripe-js @stripe/stripe-js
```
**Elements Provider**

The `Elements` provider allows you to use [Element components](https://docs.stripe.com/sdks/stripejs-react#element-components) and access the [Stripe object](https://docs.stripe.com/js/initializing) in any nested component. Render an `Elements` provider at the root of your React app so that it is available everywhere you need it. 

To use the `Elements` provider, call [loadStripe](https://github.com/stripe/stripe-js/blob/master/README.md#loadstripe) from `@stripe/stripe-js` with your publishable key. The `loadStripe` function asynchronously loads the Stripe.js script and initializes a Stripe object. Pass the returned `Promise` to `Elements`.

{% highlight js linenos %}
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
{% endhighlight %}

For additional information on how to use `Elements` visit [Stripe Docs](https://docs.stripe.com/sdks/stripejs-react#elements-provider)

##### Option 2: Use a script tag
**Installation**

Add the Stripe.js module as a script to the `<head>` element of your HTML:
{% highlight html linenos %}
<head>
  <script src="https://js.stripe.com/v3"></script>
</head>
{% endhighlight %}

**Stripe.js Constructor**

Set the API publishable key to allow Stripe to tokenize customer information and collect payment details:
{% highlight js linenos %}
var stripe = Stripe('your_testkey_abc123');
{% endhighlight %}

## Step 2: Get Publishable Key
#### Use Checkout Endpoint to Get Publishable Key
To initalize the Stripe element, you will need the publishable key. This key is returned from the `checkout` endpoint of Embrace's API. `checkout` should only be called after the quote has been selected by the customer. You will pass the `quoteId` returned from `fullquote` as a parameter in the `checkout` endpoint.

**Endpoint:** `POST /checkout`
- **URL:** `https://api.embracepetinsurance.com/api/v2/quotes/{quoteId}/checkout`
- **Method:** POST
- **Headers:** Include your API key in the header

**Request Body:**

<table class="table table-light">
  <thead>
    <tr class="text-uppercase text-muted">
      <th scope="col"></th>
      <th scope="col">Field</th>
      <th scope="col"></th>
      <th scope="col">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-bs-toggle="collapse" data-bs-target="#analyticsCheckoutCollapse" aria-expanded="true" aria-controls="analyticsCheckoutCollapse" style="cursor: pointer;" class="fs-5 toggle-icon">+</td>
      <td class="fw-bolder" scope="col">analytics</td>
      <td></td>
      <td>Expand to see details</td>
    </tr>
    <tr class="collapse" id="analyticsCheckoutCollapse">
      <td></td>
      <td colspan="3">
        <table class="table table-sm mb-0">
          <thead>
            <tr class="text-uppercase text-muted">
              <th scope="col">Field</th>
              <th scope="col">Type</th>
              <th scope="col">Description</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td class="fw-bolder" scope="row">medium</td>
              <td>string</td>
              <td>Embrace provides this value.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">term</td>
              <td>string</td>
              <td>Optional field to use at your discretion.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">content</td>
              <td>string</td>
              <td>Optional field to use at your discretion.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">campaign</td>
              <td>string</td>
              <td>Optional field for campaign name, slogan, promo code, etc...</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">source</td>
              <td>string</td>
              <td>Embrace provides this value.</td>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
    <tr>
      <td data-bs-toggle="collapse" data-bs-target="#expVariationCollapse" aria-expanded="true" aria-controls="expVariationCollapse" style="cursor: pointer;" class="fs-5 toggle-icon">+</td>
      <td class="fw-bolder" scope="col">experimentVariation</td>
      <td></td>
      <td>Expand to see details</td>
    </tr>
    <tr class="collapse" id="expVariationCollapse">
      <td></td>
      <td colspan="3">
        <table class="table table-sm mb-0">
          <thead>
            <tr class="text-uppercase text-muted">
              <th scope="col">Field</th>
              <th scope="col">Type</th>
              <th scope="col">Description</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td class="fw-bolder" scope="row">variationId</td>
              <td>string</td>
              <td>Nullable field.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">variationSystemName</td>
              <td>string</td>
              <td>Nullable field.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">variationAdded</td>
              <td>DateTime</td>
              <td>Date and time when variation was added.</td>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
    <tr>
      <td data-bs-toggle="collapse" data-bs-target="#abTestCollapse" aria-expanded="true" aria-controls="abTestCollapse" style="cursor: pointer;" class="fs-5 toggle-icon">+</td>
      <td class="fw-bolder" scope="col">abTest</td>
      <td></td>
      <td>Expand to see details</td>
    </tr>
    <tr class="collapse" id="abTestCollapse">
      <td></td>
      <td colspan="3">
        <table class="table table-sm mb-0">
          <thead>
            <tr class="text-uppercase text-muted">
              <th scope="col">Field</th>
              <th scope="col">Type</th>
              <th scope="col">Description</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td class="fw-bolder" scope="row">experimentVariationName</td>
              <td>string</td>
              <td>Variation name (on, off, not_targeted, etc...)</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">experimentDescription</td>
              <td>string</td>
              <td>Experiment Name (ab-11_reduced_coverage_test for example)</td>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
  </tbody>
</table>

**Example Request:**
{% highlight json %}
{
  "analytics": {
    "medium": "string",
    "term": "string",
    "content": "string",
    "campaign": "summer-sale",
    "source": "string"
  },
  "experimentVariation": {
    "variationId": "string",
    "variationSystemName": "string",
    "variationAdded": "2024-10-31T18:47:57.952Z"
  },
  "abTest": {
    "experimentVariationName": "string",
    "experimentDescription": "string"
  }
}
{% endhighlight %}

**Example Response:**
{% highlight json %}
{
  "analytics": {
    "medium": "string",
    "term": "string",
    "content": "string",
    "campaign": "summer-sale",
    "source": "string"
  },
  "quoteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "quoteState": "string",
  "sessionType": "string",
  "publishableKey": "123_abc_publishablekey"
}
{% endhighlight %}


## Step 3: Add Stripe Elements to Your Checkout Page
Embed Stripe Elements in your frontend code to collect payment details securely:
{% highlight js linenos %}
// Initialize Stripe
const stripe = Stripe('123_abc_publishablekey');
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
{% endhighlight %}

## Step 4: Use Embrace Partner API to Complete Purchase
Once payment is confirmed, you’ll need to call our API to finalize the policy purchase. The endpoint you’ll use is `purchase`

**Endpoint:** `POST /purchaseV2`
- **URL:** `https://api.embracepetinsurance.com/api/v2/purchaseV2`
- **Method:** POST
- **Headers:** Include your API key in the header
  
**Request Body:**

<table class="table table-light">
  <thead>
    <tr class="text-uppercase text-muted">
      <th scope="col"></th>
      <th scope="col">Field</th>
      <th scope="col">Type</th>
      <th scope="col">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-bs-toggle="collapse" data-bs-target="#analyticsCollapse" aria-expanded="true" aria-controls="analyticsCollapse" style="cursor: pointer;" class="fs-5 toggle-icon">+</td>
      <td class="fw-bolder" scope="col">analytics</td>
      <td></td>
      <td>Expand to see details</td>
    </tr>
    <tr class="collapse" id="analyticsCollapse">
      <td></td>
      <td colspan="3">
        <table class="table table-sm mb-0">
          <thead>
            <tr class="text-uppercase text-muted">
              <th scope="col">Field</th>
              <th scope="col">Type</th>
              <th scope="col">Description</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td class="fw-bolder" scope="row">medium</td>
              <td>string</td>
              <td>Embrace provides this value.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">term</td>
              <td>string</td>
              <td>Optional field to use at your discretion.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">content</td>
              <td>string</td>
              <td>Optional field to use at your discretion.</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">campaign</td>
              <td>string</td>
              <td>Optional field for campaign name, slogan, promo code, etc...</td>
            </tr>
            <tr>
              <td class="fw-bolder" scope="row">source</td>
              <td>string</td>
              <td>Embrace provides this value.</td>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
    <tr>
      <td data-bs-toggle="collapse" data-bs-target="#paymentCollapse" aria-expanded="false" aria-controls="paymentCollapse" style="cursor: pointer;" class="fs-5 toggle-icon">+</td>
      <td class="fw-bolder" scope="row">paymentInformation</td>
      <td></td>
      <td>Expand to see details</td>
    </tr>
    <tr class="collapse" id="paymentCollapse">
      <td colspan="4">
        <table class="table table-light mb-0">
          <thead>
            <tr class="text-uppercase text-muted">
              <th></th>
              <th scope="col">Field</th>
              <th scope="col">Type</th>
              <th scope="col">Description</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td data-bs-toggle="collapse" data-bs-target="#billingCollapse" aria-expanded="false" aria-controls="billingCollapse" style="cursor: pointer;" class="fs-5 toggle-icon">+</td>
              <td class="fw-bolder" scope="row">billingAddress</td>
              <td></td>
              <td></td>
            </tr>
            <tr class="collapse" id="billingCollapse">
              <td></td>
              <td colspan="3">
                <table class="table mb-0">
                  <thead>
                    <tr class="text-uppercase text-muted">
                      <th scope="col">Field</th>
                      <th scope="col">Type</th>
                      <th scope="col">Description</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr>
                      <td class="fw-bolder" scope="row">line1</td>
                      <td>string</td>
                      <td>First line for street address.</td>
                    </tr>
                    <tr>
                      <td class="fw-bolder" scope="row">line2</td>
                      <td>string</td>
                      <td>Option second line for street address.</td>
                    </tr>
                    <tr>
                      <td class="fw-bolder" scope="row">city</td>
                      <td>string</td>
                      <td>Full city name.</td>
                    </tr>
                    <tr>
                      <td class="fw-bolder" scope="row">state</td>
                      <td>string</td>
                      <td>Two letter abbreviation of the State.</td>
                    </tr>
                    <tr>
                      <td class="fw-bolder" scope="row">zipCode</td>
                      <td>string</td>
                      <td>5 numeric charachters. Must be within the U.S.</td>
                    </tr>
                  </tbody>
                </table>
              </td>
            </tr>
            <tr>
              <td></td>
              <td class="fw-bolder" scope="row">paymentMethod</td>
              <td>string</td>
              <td>Ach or Credit.</td>
            </tr>
            <tr>
              <td></td>
              <td class="fw-bolder" scope="row">paymentFrequency</td>
              <td>string</td>
              <td>Monthly or Yearly.</td>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
    <tr>
      <td></td>
      <td class="fw-bolder" scope="row">firstName</td>
      <td>string</td>
      <td>The customer's first name.</td>
    </tr>
    <tr>
      <td></td>
      <td class="fw-bolder" scope="row">lastName</td>
      <td>string</td>
      <td>The customer's last name.</td>
    </tr>
    <tr>
      <td></td>
      <td class="fw-bolder" scope="row">phoneNumber</td>
      <td>string</td>
      <td>The customer's phone number.</td>
    </tr>
    <tr>
      <td></td>
      <td class="fw-bolder" scope="row">agreeToTermsOfServiceAndPrivacyPolicy</td>
      <td>boolean</td>
      <td>Terms of Service and Privacy Policy must be expressly accepted by the purchaser.</td>
    </tr>
    <tr>
      <td></td>
      <td class="fw-bolder" scope="row">optInTextMessage</td>
      <td>boolean</td>
      <td>Optional field for customer to accept text messages.</td>
    </tr>
    <tr>
      <td></td>
      <td class="fw-bolder" scope="row">allPetsHadVetVisit</td>
      <td>boolean</td>
      <td>Pet has had required vet visit within the past 12 months, or will have one within 14 days of the policy beginning.</td>
    </tr>
    <tr>
      <td></td>
      <td class="fw-bolder" scope="row">paymentMethodToken</td>
      <td>boolean</td>
      <td>Token which is returned from the Stripe element after checkout.</td>
    </tr>
  </tbody>
</table>

**Example Request:**
{% highlight json %}
{
  "analytics": {
    "medium": "string",
    "term": "string",
    "content": "string",
    "campaign": "summer-sale",
    "source": "string"
  },
  "mailingAddress": {
    "line1": "123 Main St.",
    "line2": "string",
    "city": "Cleveland",
    "state": "OH",
    "zipCode": "44120"
  },
  "paymentInformation": {
    "billingAddress": {
      "line1": "123 Main St.",
      "line2": "string",
      "city": "Cleveland",
      "state": "OH",
      "zipCode": "44120"
    },
    "paymentMethod": "Ach",
    "paymentFrequency": "Monthly"
  },
  "firstName": "Carlo",
  "lastName": "Ginzburg",
  "phoneNumber": "5555555555",
  "agreeToTermsOfServiceAndPrivacyPolicy": true,
  "optInTextMessage": true,
  "allPetsHadVetVisit": true,
  "paymentMethodToken": "string"
}
{% endhighlight %}

**Example Response:**
{% highlight json %}
{
  "purchaseSucceeded": true,
  "policyNumber": "INS-987654321"
}
{% endhighlight %}

