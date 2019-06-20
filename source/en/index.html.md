---
search: true

toc_footers:
 - <a href='../'>Türkçe dökümantasyon için tıklayınız </a>
---
# <a href='#paytrek' style='color: white; text-decoration: none; color:#7ccfaf' > Paytrek</a>


Paytrek creates the ability to process transactions in more than
100 countries as well as ensuring the
highest-level cardholder information safety with its PCI DSS Level 1
certification. Paytrek introduces a payment system that is easy-to-use
and free of pricey legal installation requirements.

# Payment Flows

Following payment flows are suggested ones for easy-to-use integrations.

In order to be able to use Paytrek you must be a valid merchant.
If you don’t have a username and password to use in the API calls,
please sign up at [https://sandbox.paytrek.com/sandbox/signup](https://sandbox.paytrek.com/sandbox/signup)
Once your integration is fully completed Paytrek Integration
Team will provide the live credentials.

Paytrek provides you following ways to integrate:

+ Paytrek Hosted Payment Form
+ Direct Charge
+ Tokenization Flow
+ Pre-Authorization Flow

<aside class="notice">
  You must check <a href="#retrieve-sale"><b> Sale </b></a> status when you complete charging with this endpoints.
</aside>

## Paytrek Hosted Payment Flow

1. The customer submits an order on merchant web site.
2. Merchant requests a token from Paytrek via the [Sale](#sale) Resource.
3. Paytrek returns the merchant a `sale_token`.
4. Merchant redirects the customer to checkout form on Paytrek with the token[1].
5. Customer enters card data on Paytrek Hosted Form.
6. Paytrek processes the payment.
7. Paytrek receives a response from the related processor.
8. Paytrek redirects the customer to your web site with the `sale_token`[2].
9. Merchant checks sale status with <a href="#retrieve-sale"><b>Retrieve Sale</b></a> endpoint.


<sub> * [1] Sandbox hosted form is located at
    [https://sandbox.paytrek.com](https://sandbox.paytrek.com) and Production hosted form
    is located at [https://secure.paytrek.com](https://secure.paytrek.com) and [https://secure.paytrek.com.tr](https://secure.paytrek.com.tr) for Turkey.
    You should redirect the user to these urls with
    the token parameter added to the url.
    (ie. https://sandbox.paytrek.com/?token=TOKEN_VALUE)</sub>

<sub> * [2] You must check the status of the sale via Sale
    Resource once the customer lands to your web site.
    If the sale status is one of **Paid**, **Ready to Send**,
    **Authorized** and **PreAuthorized** it means the sale has been
    successfully charged.</sub>

## Direct Charge

1. The customer checks out on your web site.
2. Customer enters card data on your web site.
3. You charge on Paytrek via the [Direct Charge](#direct-charge-2) Resource.
4. Paytrek processes the payment.
5. Paytrek receives a response from the related processor.
6. Paytrek returns you a response with the payment result.

## Tokenization Flow

1. The customer checks out on your web site.
2. Customer enters card data on your form and and you make a call to `Create Card Token` with PaytrekJS.
3. You save the card token and create a sale via [Sale](#sale) Resource.
4. You make a call to [Charge with Card Token](#charge-with-token) Resource with card token and sale token.
5. Paytrek processes the payment.
6. Paytrek receives a response from the related processor.
7. Paytrek returns you a response with the payment result.

## Pre-Authorization

1. The customer checks out on your web site.
2. Customer enters card data on your form and you make a call to [Direct Charge](#direct-charge) Resource with `pre_auth: true` option.
3. You make a call to [Capture](#capture) endpoint with ```sale_token```.
4. Paytrek processes the payment.
5. Paytrek receives a response from the related processor.
6. Paytrek returns you a response with the payment result.


# 3D Transactions

You or your customers may choose a charge to be processed
through 3D secure systems provided by MasterCard or Visa.
In this case, Paytrek handles 3D transactions both on Hosted Form
and Tokenization Flow.

When you choose a charge to be processed through 3D secure system, your API request
must include ```secure_option``` parameter with `true` variable.

<aside class="notice">
  You must check <a href="#retrieve-sale"><b> Sale </b></a> status when you complete charging with this endpoints.
</aside>

## 3D Transactions on Paytrek Hosted Payment Form

1. The customer checks out on your web site.
2. You request a token from Paytrek via the [Sale](#sale) Resource.
3. Paytrek returns you a `sale_token`.
4. Redirect your customer to hosted form on Paytrek with the `sale_token`. [1]
5. Customer enters card data.
6. Paytrek starts a 3D transaction, redirects the customer to 3D secure system.
7. Customer enters the PIN on the 3D secure system.
8. 3D Secure system redirects the customer back to Paytrek.
9. Paytrek processes the payment if 3D secure result is a success or halts the process if 3D secure result is failure.
10. Paytrek redirects the customer to your web site with the `sale_token`. [2]

<sub> * [1] Sandbox hosted form is located at
    [https://sandbox.paytrek.com](https://sandbox.paytrek.com) and Production hosted form
    is located at [https://secure.paytrek.com](https://secure.paytrek.com).
    You should redirect the user to these urls with
    the token parameter added to the url.
    (ie. [https://sandbox.paytrek.com/?token=TOKEN_VALUE](https://sandbox.paytrek.com/?token=TOKEN_VALUE)))</sub>

<sub> * [2] You must check the status of the sale via Sale
    Resource once the customer lands to your web site.
    If the sale status is one of **Paid**, **Ready to Send**,
    **Authorized** and **PreAuthorized** it means the sale has been
    successfully charged.</sub>

## 3D Transactions on Direct Charge Flow

1. The customer checks out on your web site.
2. Customer enters card data on your web site.
3. You charge on Paytrek via the [Direct Charge](#direct-charge) Resource.
4. Paytrek returns you a response with a field `forward_url`.
6. You redirect the customer to the given `forward_url`.
7. Customer is redirected to Paytrek and Paytrek redirects the customer to 3D secure system.
8. Customer enters the PIN on the 3D secure system.
9. 3D Secure system redirects the customer back to Paytrek.
10. Paytrek processes the payment if 3D secure result is a success or halts the process if 3D secure result is failure.
11. Paytrek redirects the customer to your web site. [1]


<sub> * [3] Paytrek redirects the customer to the given
    `return_url` field when creating a new sale via Sale Resource.</sub>


## 3D Transactions on Tokenization Flow

1. The customer checks out on your web site.
2. Customer enters card data on your form and and you make a call to `Create Card Token` with PaytrekJS.
3. You save the `card_token` and create a sale via [Sale Resource](#sale).
4. You send a request to [Charge with Card Token Resource](#charge-with-token)  with the `card_token` you obtained and the sale you have created.
5. Paytrek returns you a response with a field `forward_url`.
6. You redirect the customer to the given `forward_url`.
7. Customer is redirected to Paytrek and Paytrek redirects the customer to 3D secure system.
8. Customer enters the PIN on the 3D secure system.
9. 3D Secure system redirects the customer back to Paytrek.
10. Paytrek processes the payment if 3D secure result is a success or halts the process if 3D secure result is failure.
11. Paytrek redirects the customer to your web site. [1]

<sub> * [1] Paytrek redirects the customer to the given
    return_url field when creating a new sale via Sale Resource.</sub>

# Paytrek API

Paytrek gateway API is designed with industry standard best practices,
based on RESTful architecture.


## Authentication

All requests must be authenticated using [Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication).

If you don’t have a username(api_key) and password(secret) to use in the API calls,
please sign up at [https://sandbox.paytrek.com/sandbox/signup](https://sandbox.paytrek.com/sandbox/signup).

Your username(api_key) and password(secret) are located on [Account Info](https://sandbox.paytrek.com/dashboard/account_info/) page.

## Headers

All requests must include ```Content-Type: application/json```

## Endpoints


| Resource      | Endpoint      | Allowed HTTP Methods |
| ------------- |:-------------| :-----:|
| [`Sale`](#sale)      | ```/api/v2/sale/``` | ```POST``` ```GET```|
| [`Charge`](#charge)      | ```/api/v2/charge/``` | ```POST``` |
| [`Direct Charge`](#direct-charge)| ```/api/v2/direct_charge/``` | ```POST``` |
| [`Vault`](#vault) | ```/api/v2/vault/``` | ```POST``` ```GET``` |
| [`Charge With Token`](#charge-with-token) | ```/api/v2/charge_with_token/``` | ```POST``` |
| [`Capture`](#capture) | ```/api/v2/capture/``` | ```POST``` |
| [`Cancel`](#cancel) | ```/api/v2/cancel/``` | ```POST``` |
| [`Refund`](#refund) | ```/api/v2/refund/``` | ```POST``` |
| [`Installments`](#installments) | ```/api/v2/installments/``` | ```GET``` |


## Paytrek Environment URLs

|||
|:-|:-|
|Sandbox (Test) | [https://sandbox.paytrek.com](https://sandbox.paytrek.com)|
|Live Environment | [https://secure.paytrek.com.tr](https://secure.paytrek.com.tr)|


# PaytrekJS Library

PaytrekJS is a small library that will help you integrate Paytrek’s
tokenization service with your client side application.
In this section we are going to inspect this library.

## Integrating PaytrekJS

PaytrekJS does not interfere with your form submissions or event handlers.
It only requires you to call it once everything you’d like to perform
on your form’s onsubmit handler is done.

## Include PaytrekJS
```html
<script type="text/javascript" src="https://secure.paytrek.com.tr/static/v2/js/paytrek.js"></script>
```

> For Paytrek Sandbox, please include the following:

```html
<script type="text/javascript" src="https://sandbox.paytrek.com/static/v2/js/paytrek.js"></script>
```

In order to be able to use PaytrekJS you need to include it in your web page.
You can either include it at the footer or the header.

## Configuring PaytrekJS

PaytrekJS has some configuration options that you must set.
The options are explained as follows:

### PaytrekJS Options

```javascript
Paytrek.Options = {
    "publishableKey"          : "myPaytrekPublishableKey",
    "host"                    : "https://secure.paytrek.com",
    "orderId"                 : "1420199350",
    "currency"                : "TRY",
    "amount"                  : "1299.00",
    "removeCardElements"      : false,
    "resetCardElementValues"  : false,
    "cardTokenField"       : "card_token"
}

var form = document.getElementById("payment_form");
form.addEventListener("submit", function(event) {
    // handle your form submission.
    // everything seems good so far let's get a card token
    var paytrekForm = new Paytrek.Form(event.target);
    paytrekForm.createToken(function(response) {
        if (response.hasOwnProperty("errors")) {
            // there's a validation error or there's a server side error
            // from Paytrek that you must handle.
            return false;
        }
        // we have the token, let's use it.
        console.log(response.card_token);
    });
});
```

| Parameter| Description |
|:-|:-|
|publishableKey|string The publishable key that you’ve obtained from Paytrek Dashboard.|
|host|string Paytrek host that you are going to make the request. You need to use the secure.paytrek.com.tr host otherwise you have to use the secure.paytrek.com host.|
|orderId|string The order id for the sale that you will use when creating a sale. Order ids must be unique for each sale.|
|currency|string The 3 letter currency symbol for the sale. (ie: TRY, USD, EUR)|
|amount|string The amount of the sale. (ie: 23.99)|
|dccEnabled|true or false Whether you wish to enable listing dynamic currency options.|
|removeCardElements|true or false. Whether Paytrek.Form should remove all credit card related elements from your form after obtaining a payment token.|
|resetCardElementValues|true or false. Whether Paytrek.Form should reset all credit card related elements’ value to an empty string after obtaining a card token.|
|cardTokenField|string. Id of the hidden input field of which value will be set to the payment token after obtaining it.|
|useVaultField|string Id of the checkbox field of which value will be checked if the customer wanted their card data to be stored.|

The following is a code sample to show you how you can embed PaytrekJS in
your form submissions.

PaytrekJS consists of two components that we will cover:

## Paytrek.Form

This class file is a JavaScript object mapping to your checkout form. It will map your credit card number, cvc,
expiration date and card holder name fields to its own properties.

### Constructor

```javascript
// creating a Paytrek.Form object with an id
var paytrekForm = new Paytrek.Form("myFormId");
```

```javascript
// creating a Paytrek.Form object with a form element
var paytrekForm = new Paytrek.Form(document.getElementById("myFormId"));
var paytrekForm = new Paytrek.Form(document.forms[0]);
```

You can instantiate Paytrek.Form class by providing your form’s id attribute or the form element itself,
an optional configuration object and an object related to sale information.

If the constructor cannot find the form, it will throw an Error.


### Sale Data Object

Sale Data is an object that holds the minimum information
regarding a sale that you will create after obtaining a card token.
These are the data that you know beforehand creating a card token or
sale such as the sale’s order id, sale’s amount and currency.

All the data that will be sent through sale data must be converted to string.

### Creating Card Token

```javascript
paytrekForm.createToken(function(response){
    // response is an object returned by the xhr call.
    console.log(response.card_token);
});
```

Once you have a Paytrek.Form instance ready,
all you have to do is to call the `createToken()` method on your
instance in order to obtain a card token.

`createToken()` method takes only one argument:
A callback function as an argument of which the first
argument is the response returned from the Paytrek API.


### Card Token Response

```javascript
paytrekForm.createToken(function(response){
    if (response.card_token === undefined) {
        // handle failure case
    } else {
        // handle success case
    }
});
```

The HTTP response that Paytrek Tokenization API returns will always
be in JSON format. It will have either `card_token` or `errors` keys.

### Form Elements
> Example

```html
<form id="checkout">
   <input id="payment_token" name="payment_token" value="" />
   <div>
     <label for="number">Credit Card Number:</label>
     <input id="number" data-paytrek-field="number" />
   </div>
   <div>
     <label for="chname">Card Holder Name:</label>
     <input id="chname" data-paytrek-field="card-holder-name" />
   </div>
   <div>
     <label for="expmonth">Expiration Month:</label>
     <select id="expmonth" data-paytrek-field="expiration-month">
       <option>1..12</option>
     </select>
   </div>
   <div>
     <label for="expyear">Expiration Year:</label>
     <select id="expyear" data-paytrek-field="expiration-year">
       <option>2014..2020</option>
     </select>
   </div>
</form>
```

Once you create a new Paytrek.Form object, it will search for all the
nodes under your form element that have an attribute
called “data-paytrek-field”. There is an example of a
form with fields having data-paytrek-field attribute in left panel.

## Paytrek.CreditCard

```javascript
var creditCard = new Paytrek.CreditCard(
    number,
    cvc,
    expMonth,
    expYear,
    cardHolderName
);
```

This is a JavaScript class that takes care of the credit
card data and its validation before making an actual API request.

### Constructor

Paytrek.CreditCard’s constructor takes 5 arguments in the following order:
credit card number, cvc, expiration month, expiration year, card holder name.

### Validations

```javascript
if (creditCard.isValid()) {
    // credit card is valid
} else {
   // credit card is invalid
   console.log(creditCard.errors);
}
```

You can validate the credit card instance you have.
Paytrek.CreditCard class supports validating
credit card number, cvc, expiration date and the card holder name.

The following are the list of methods that Paytrek.CreditCard provides.

| Method Name| explanation  |
|:-|:-|
|isCvcValid()| Validates the cvc.|
|isCardNumberValid()| Validates the credit card number.|
|isdExpirationDateValid()| Validates the expiration date.|
|isCardHolderNameValid()| Validates the card holder name.|

You don’t have to call all of the methods above
in order to validate a credit card object. You can just call the
isValid() method.


## Payment Options

PaytrekJS provides two more nice features which are card type fees
and dynamic currency options per sale.
Please seek advice from your Paytrek Account
Representative whether your current subscription type supports these features.

### Card Type Fees

As Paytrek lets you set up fees per card type of your customers, PaytrekJS
also lets you see and update the costs on the fly on your payment form.

### Dynamic Currencies
> Example;

```javascript
paytrekForm.getPaymentOptions(function(response) {
    console.log(response);
    /*
    {"options":
      {"TRY": {
        "tx_fee": "0.96",
        "grand_total": "101.96",
        "total": "101.00",
        "tx_fee_rate": "0.95"}
      }
    }*/
});
```

Paytrek lets your customers to make payments in their own currencies.
A nice, enabling feature of PaytrekJS is that it lets you do the same
for your customers in your own payment pages.
If you set dccEnabled property of Paytrek.Options to true,
while creating a card token PaytrekJS will also return a
list of available currencies. You can then display this list of
available currencies in your card form and satisfy your customers.

## Paytrek.Checkout

Paytrek.Checkout widget allows you to integrate
embedded checkout form on your web site incredibly easy.
This widget will redirect the customer to the given
`return_url`. In this `return_url`, you must query the status of
the sale via [Sale Resource](#sale).
If the sale status is one of **Paid**, **Ready to Send**, **Authorized** and
**PreAuthorized** it means the sale has been successfully charged.

### Constructor

```javascript
function Checkout(saleData, options) {
    // Paytrek.Checkout implementation
}
```

Paytrek.Checkout constructor takes two arguments, named
saleData and options. Both saleData and options arguments
are JavaScript Hashes. Keys for saleData argument are the fields for Sale Resource.

### Constructor Options

> Modal Form

```javascript
// your order form
var form = document.forms['order-form'];
form.addEventListener('click', function(event){
    // your form handling logic
    // ...
    // everything is fine, display the checkout widget
    new Paytrek.Checkout({
        amount: "10.20",
        currency: "TRY",
        customer_first_name: "John",
        customer_last_name: "Doe",
        customer_email: "johndoe@gmail.com",
        billing_state: "CA",
        billing_city: "San Francisco",
        billing_country: "US",
    }).displayForm();
});
```

> Embeded Form

```javascript
var saleData = {
  amount: "10.20",
  currency: "TRY",
  customer_first_name: "John",
  customer_last_name: "Doe",
  customer_email: "johndoe@gmail.com",
  billing_state: "CA",
  billing_city: "San Francisco",
  billing_country: "US",
};

var options = {
  openModal: false,
  widgetParent: document.getElementById('widget-parent'),
  errorCb: function(errorMessage) {
      console.log(errorMessage);
  },
  beforeCb: function(event) {
      // form validation is handled here
      // if you want to stop paytrek widget to charge
      // you should do event.preventDefault()
  },
  afterCb: function(event) {
      // post-charge callback
  }
};

new Paytrek.Checkout(saleData, options).displayForm();
```

Paytrek.Checkout accepts options as the second positional argument.

| Options | Description |
|:-|:-|
|openModal|Sets if you would like to display the form embedded in your page or open as a modal box. Defaults to true.|
|widgetParent|The parent DOM Element where you want to display the embedded form if you don’t wish to display the modal box.|
|beforeCb|The callback function that you want to run before charging takes place.|
|afterCb|The callback function that you want to run after charging takes place.|
|errorCb|The callback function that you want to run when charging fails.|

# Test Cards

Here is a list of test credit cards that you can use.

| Card Number        | Bank           | Expiration  | Cvc| 3D Pass
| ------------- |:-------------| :-----:|:---:|:---:|
| 4508034508034509     | İşbank | 12/20 |000| |
| 4506347043358536      | YKB      |  08/19 |000| |
| 4508034508034509 | ING      |   12/18 |000| |
| 4355084355084358 | Akbank | 12/18 |000| a |
| 5456165456165454 | Finansbank | 12/18 |000| a |

# Error Codes

Paytrek handles possible error mappings between bank services. It returns you to user
friendly error messages within `error_code` and `error_message`. Following table includes
error codes and descriptions.

|Code| Merchant Message  |  Customer Message  |
|:-:|:-|:-|
|30001|Invalid Business Member|An unexpected error has occurred, please contact the seller.|
|10004|Transaction has been denied / General Rejection|Transaction has been denied, please contact your bank|
|10006|ID Control / Honor With Id|Transaction has been denied, please contact your bank.|
|30003|Invalid Amount|An unexpected error has occurred, please contact the seller.|
|10009|Invalid Account / Card Number|Your transaction has been terminated, please check your card information.|
|10020|Insufficient Limit|Transaction has been denied, please check your card limit.|
|10023|Card has Expired|Transaction has been denied, please check your card information.|
|10026|The transaction has not been cleared to the cardholder|Transaction has been denied, please contact your bank.|
|10029|Security Breach|Transaction has been denied, please contact your bank.|
|30009|Incompatible data received|An unexpected error has occurred, please contact the seller.|
|30011|Encryption Error|An unexpected error has occurred, please contact the seller.|
|10033|Wrong / Invalid Cvv|Transaction has been denied, please check your card information.|
|30013|Authentication Error|An unexpected error has occurred, please contact the seller.|
|10038|Rejection - Invalid Card|The transaction has been denied, please proceed with another card.|
|30015|Double Transaction Transfer|-|
|30016|Daily Count Error/ Cancellation has been rejected|-|
|20015|System Error|An unexpected error has occurred, please contact the seller.|
|20017|Connection Error|An unexpected error has occurred, please contact the seller.|
|30033|Incorrect Extra Point Parameter|An unexpected error has occurred, please contact the seller.|
|30037|Invalid Installment|An unexpected error has occurred, please contact the seller.|
|30041|Invalid Pos|An unexpected error has occurred, please contact the seller.|
|30050|End of the Day Transactions must be Completed|An unexpected error has occurred, please contact the seller.|
|30053|Invalid Currency|An unexpected error has occurred, please contact the seller.|
|30090|Invalid Repeating Payment Duration|-|
|10046|The Request can be Fraud, Ip of cardholder did not clear the check|Transaction has been denied, please contact your bank.|
|30119|Invalid Order Number.|We cannot complete your transaction at the moment, please contact the the seller.|
|30120|Order Number is in Use|We cannot complete your transaction at the moment, please contact the the seller.|
|10049|Maximum Retry Count Exceeded|Maximum retry count exceeded, Please try again later.|
|99999|Card Declined|Card Declined|
|10050|FR-Rejected|An unexpected error has occurred, please contact the seller.|
|40000|Unable to Authenticate|We cannot proceed with your transaction, unable to verify cardholder.|
|40004|Cardholder not Participating|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|40005|Directory Server not Responding|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|40006|Authentication Failure|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|40007|3D Secure System Error|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|10001|Please call your bank for authorization|Transaction has been denied, please contact your bank.|
|10003|Block The Card|Transaction has been denied, please contact your bank.|
|30002|Invalid Transaction|An unexpected error has occurred, please contact the seller.|
|10010|Unidentified Issuer Bank|Your transaction has been terminated, please check your card information.|
|30005|Cancellation could not be completed / The original could not be found|-|
|10016|Too Many Pin Entry Attempts, Block the Card|Transaction has been denied, please contact your bank.|
|10032|Invalid Date|Transaction has been denied, please check your card information.|
|30118|The Request is not included in the Group's Allowed Day/Hour Setting|We cannot complete your transaction at the moment, please contact the the seller.|
|30128|Vpos Lock Control Failure|We cannot complete your transaction at the moment, please contact the the seller.|
|UNKNOWN_ERROR|Unknown Error|An unexpected error has occurred, please try again later|
|9000|Preauth Transaction Cannot Void|Preauth Transaction Cannot Void|
|9001|3D Fields Do Not Match|3D Fields Do Not Match|
|40002|Cardholder or Issuer Bank not Enrolled in 3D Secure System|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|

# Paytrek Collection Wallet

Paytrek Wallet is the payout platform that used to make payments for your payees such as earnings, commissions or loyalities etc.
The platform comprise of two main components for completing payments
and payout flow. They are [Wallet API](https://paytrekwallet.docs.apiary.io/#introduction/paytrek-wallet-api) and [Payee Dashboard](https://paytrekwallet.docs.apiary.io/#introduction/payee-dashboard).

# <a href='#reference' style='color: white; text-decoration: none; color:#7ccfaf' > Reference </a>

# Sale

Sale Resource provides you the endpoint to create a sale object that including sale related informations.
It allows to define a proper transaction before charging the card.
If the sale that you are going to charge is Pre-Authorized (`pre_auth` must be ```true```),
the sale will be captured.

To retrieve a created sale object, ```sale_token``` must be defined in url and send by `GET` HTTP method.
It returns related sale details with transaction informations and status.

(eg. `/api/v2/sale/f25356bab177416ba7b518cc1bd8a596/`)



## Create Sale

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "amount": 24.0,
  "order_id": "1467034250",
  "secure_option": false,
  "pre_auth": false,
  "billing_address": "123 Market St. San Francisco",
  "billing_city": "San Francisco",
  "billing_country": "US",
  "billing_state": "CA",
  "currency": "TRY",
  "customer_email": "johndoe@gmail.com",
  "customer_first_name": "John",
  "customer_ip_address": "212.57.9.204",
  "customer_last_name": "Doe",
  "installment": 1,
  "items": [{
    "unit_price": 12.0,
    "quantity": 2,
    "name": "product_name",
    "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg"
  }],
  "sale_data": {"merchant_name": "Ted"}
}
```

> Response Code

```string
201
```
> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "request_id": null,
    "sys_time": 1521014319,
    "language_code": "en-us",
    "transactions": [],
    "currency": "TRY",
    "order_id": "1467034250",
    "amount": "24.00",
    "installment": 1,
    "sale_token": "c626f49193114e24ad0980752b749318",
    "expires_at": "2018-03-14T08:58:39.519205Z",
    "secure_option": true,
    "half_secure": null,
    "pre_auth": false,
    "return_url": null,
    "customer_first_name": "John",
    "customer_last_name": "Doe",
    "customer_email": "johndoe@gmail.com",
    "customer_ip_address": "212.57.9.204",
    "billing_city": "San Francisco",
    "billing_country": "US",
    "billing_zipcode": null,
    "billing_state": "CA",
    "billing_phone": null,
    "sale_data": {
        "merchant_name": "Ted"
    },
    "items": [
        {
            "name": "product_name",
            "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg",
            "quantity": 2,
            "unit_price": "12.00"
        }
    ],
    "created_at": "2018-03-14T07:58:39.519707Z",
    "callback_url": null,
    "refunded_amount": "0.00",
    "status": "Created",
    "financial_status": null,
    "description": null,
    "is_locked": false,
    "hosted_payment": true,
    "billing_address": "123 Market St. San Francisco",
    "billing_company": null
}
```

| Request Type | Endpoint |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/sale/ |

There is an example request of sale creation request in left panel.

| Parameter | Type | Description |
|:-|:-|:-|
|currency | string, required | 3-letter ISO code for currency.|
|order_id | string, required | The order id of the sale.|
|amount |  number, required | The amount to charge the visitor.|
|customer_first_name | string, required | Customer's first name.|
|customer_last_name | string, required | Customer's last name. |
|customer_email | string, required | Customer's email address.|
|customer_ip_address | string, required | Customer's ip address.  |
|billing_address | string, required | Customer's billing address.|
|billing_city | string, required | Customer's billing city.|
|billing_state | string | Customer's billing state, use postal abbreviations for US and CA.|
|billing_country | string, required | Customer's billing country, 2-letter ISO code. |
|billing_zipcode | string, optional | Customer's billing zipcode.|
|billing_phone | string, optional | Customer's billing phone number.|
|items | object, required | The item related informations which is selling.|
|return_url | string | Related link that user is redirected when payment completed.|
|hosted_payment | boolean | To use the hosted payment page.|
|hosted_payment_url | string | Returns to hosted payment page url.|
|installment | number, required | The installment number of the sale.|
|secure_option | boolean | Whether the sale is secure.(Default: false)|
|half_secure | boolean | Whether the sale can be charged if 3d enrollment check returns mdstatus 2, 3, 4.|
|pre_auth | boolean | Indicates whether this sale should be pre-authorized.(Default: false)|
|sale_data | object | Extra fields for sale.|

## Retrieve Sale

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```string
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "request_id": null,
    "sys_time": 1521014392,
    "language_code": "en-us",
    "transactions": [],
    "currency": "TRY",
    "order_id": "1467034250",
    "amount": "24.00",
    "installment": 1,
    "sale_token": "c626f49193114e24ad0980752b749318",
    "expires_at": "2018-03-14T08:58:39.519205Z",
    "secure_option": true,
    "half_secure": null,
    "pre_auth": false,
    "return_url": null,
    "customer_first_name": "John",
    "customer_last_name": "Doe",
    "customer_email": "johndoe@gmail.com",
    "customer_ip_address": "212.57.9.204",
    "billing_city": "San Francisco",
    "billing_country": "US",
    "billing_zipcode": null,
    "billing_state": "CA",
    "billing_phone": null,
    "sale_data": {
        "merchant_name": "Ted"
    },
    "items": [
        {
            "name": "product_name",
            "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg",
            "quantity": 2,
            "unit_price": "12.00"
        }
    ],
    "created_at": "2018-03-14T07:58:39.519707Z",
    "callback_url": null,
    "refunded_amount": "0.00",
    "status": "Created",
    "financial_status": null,
    "description": null,
    "is_locked": false,
    "hosted_payment": true,
    "billing_address": "123 Market St. San Francisco",
    "billing_company": null
}
```

| Request Type | Endpoint |
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/sale/**sale_token**/ |


# Charge

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "number": "4508034508034509",
  "expiration": "12/2020",
  "cvc":"000",
  "card_holder_name":"John Doe",
  "sale_token":"c626f49193114e24ad0980752b749318"
}
```

> Response Code

```string
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "exp_month": "12",
    "number": "450803******4509",
    "sale_token": "c626f49193114e24ad0980752b749318",
    "exp_year": "2020",
    "card_holder_name": "J********",
    "succeeded": true,
    "error_message": "",
    "issuer_bank_id": "isbank",
    "secure_charge": false,
    "cvc": "***",
    "return_url": "?token=c626f49193114e24ad0980752b749318"
}
```

Charge Resource provides you the
endpoint to charge a credit card.
You must create a [Sale](#sale) object before charging your customer.
If the sale that you are going to charge is Pre-Authorized (pre_auth must be true),
the sale will be captured.

| Request Type | Endpoint |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/charge/ |

While charging a card, you can also store it with `save_card` option.
If `save_card` is settled `true` in request then it will be vaulted and
returns `card_token` in response. It allows to charge the card in
the next payment within [Charge with token](#charge-with-token).



| Parameter | Type | Description |
|:-|:-|:-|
|sale_token  | string, required | The sale token created though sale endpoint.|
|number  | number, required | The credit card number. |
|expiration  | string, required | The expiration date of the credit card, in MM/YY format.|
|cvc  | number, required | The security number of the credit card.|
|card_holder_name | The name on card.|
|save_card  | boolean(Default: true) | The card storing option.|

<aside class="notice">
You must check <a href="#retrieve-sale"><b> Sale </b></a> status when you complete charging with this endpoints.
</aside>

# Direct Charge

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "amount": 24.0,
  "order_id": "1467034250",
  "secure_option": false,
  "pre_auth": false,
  "number": "4508034508034509",
  "expiration": "12/2020",
  "cvc":"000",
  "card_holder_name":"John Doe",
  "billing_address": "123 Market St. San Francisco",
  "billing_city": "San Francisco",
  "billing_country": "US",
  "billing_state": "CA",
  "currency": "TRY",
  "customer_email": "johndoe@gmail.com",
  "customer_first_name": "John",
  "customer_ip_address": "212.57.9.204",
  "customer_last_name": "Doe",
  "installment": 1,
  "items": [{
    "unit_price": 12.0,
    "quantity": 2,
    "name": "product_name",
    "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg"
    }],
  "sale_data": {"merchant_name": "Ted"}
}
```

> Response Code

```string
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "secure_option": true,
    "number": "450803******4509",
    "half_secure": null,
    "currency": "TRY",
    "customer_email": "johndoe@gmail.com",
    "language_code": "en-us",
    "billing_country": "US",
    "customer_ip_address": "212.57.9.204",
    "card_holder_name": "J********",
    "customer_last_name": "Doe",
    "billing_zipcode": null,
    "financial_status": "Ready to Send",
    "billing_phone": null,
    "issuer_bank_id": "isbank",
    "sale_data": {
        "merchant_name": "Ted"
    },
    "sys_time": 1521016061,
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "description": null,
    "transactions": [
        {
            "type": "Sale",
            "succeeded": true,
            "error_message": "",
            "ref_id": "",
            "amount": "24.00",
            "currency": "TRY",
            "is_secure": false,
            "secure_status": null,
            "created_at": "2018-03-14T08:27:40.467500Z",
            "bin_number": "450803",
            "card_number_last4": "4509",
            "card_type": "credit",
            "exchange_rate": null,
            "card_holder_name": "J********",
            "card_fee": "0.00",
            "card_fee_rate": "0.00",
            "fraud_risk": {
                "id": 46196,
                "request_id": null,
                "sys_time": 1521016061,
                "language_code": "en-us",
                "created_at": "2018-03-14T08:27:40.411031Z",
                "succeeded": true,
                "risk_score": "16.00",
                "decision": "ACCEPT",
                "reviewer_decision": null,
                "reviewer_comments": null,
                "bin_country": "TR",
                "bin_name": "visa",
                "ip_region": null,
                "ip_country": null,
                "ip_city": null,
                "ref_id": null,
                "error": null,
                "info_codes": null,
                "sale": 90810,
                "transaction": null
            },
            "paytrek_ref_id": "1mlHu6ljNGxC5CZY0nVs",
            "paytrek_error": null
        }
    ],
    "order_id": "1467034250",
    "succeeded": true,
    "expires_at": "2018-03-14T09:27:40.301574Z",
    "hosted_payment": false,
    "exp_month": "12",
    "sale_token": "92489820e4914f23b2db8039205cb64b",
    "exp_year": "2020",
    "secure_charge": false,
    "billing_city": "San Francisco",
    "installment": 1,
    "refunded_amount": "0.00",
    "is_locked": false,
    "items": [
        {
            "name": "product_name",
            "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg",
            "quantity": 2,
            "unit_price": "12.00"
        }
    ],
    "created_at": "2018-03-14T08:27:40.302118Z",
    "error_message": "",
    "amount": "24.00",
    "status": "Paid",
    "return_url": "?token=92489820e4914f23b2db8039205cb64b",
    "billing_company": null,
    "request_id": null,
    "customer_first_name": "John",
    "callback_url": null,
    "cvc": "***",
    "billing_state": "CA"
}
```

Direct charge endpoint provides to charge a credit card instantly
without creating any sale object. If the sale that you are going to charge is Pre-Authorized
(`pre_auth` must be ```true```), the sale will be captured.

While charging a card, you can also store it with `save_card` option.
If `save_card` is settled `true` in request then it will be vaulted and
returns `card_token` in response. It allows to charge the card in
the next payment within [Charge with token](#charge-with-token).


| Parameter | Type | Description |
|:-|:-|:-|
|currency|string, required|3-letter ISO code for currency.|
|order_id|string, required|The order id of the sale.|
|amount |number, required|The amount to charge the visitor.|
|number|number, required|The credit card number.|
|expiration|string, required|The expiration date of the credit card, in MM/YY format.|
|cvc|number, required|The security number of the credit card.|
|card_holder_name|string, required|The name on card.|
|installment|number, required|The installment number of the sale.|
|secure_option|boolean|Whether the sale is secure.(Default: false)|
|half_secure|boolean|Whether the sale can be charged if 3d enrollment check returns mdstatus 2, 3, 4.|
|return_url|string, required|Related link that user is redirected when payment completed.|
|pre_auth|boolean|Indicates whether this sale should be pre-authorized.(Default: false)|
|items|object, required|The item related informations which is selling.|
|customer_first_name|string, required|Customer's first name.|
|customer_last_name|string, required|Customer's last name.|
|customer_email|string, required|Customer's email address.|
|customer_ip_address|string, required|Customer's ip address.  |
|billing_address|string, required|Customer's billing address.|
|billing_city|string, required|Customer's billing city.|
|billing_state|string|Customer's billing state, use postal abbreviations for US and CA.|
|billing_country |string, required|Customer's billing country, 2-letter ISO code.|
|billing_zipcode |string, required|Customer's billing zipcode.|
|billing_phone |string, required|Customer's billing phone number.|
|sale_data |object|Extra fields for sale.|

<aside class="notice">
You must check <a href="#retrieve-sale"><b> Sale </b></a> status when you complete charging with this endpoints.
</aside>

# Vault

Paytrek allows you to store your customer’s PAN data on Paytrek’s secure vault. By using this endpoint,
you will only need to retrieve the credit card data from your customers only once and then you will be able to use the same data for charging any time you wish.

Here are the steps you must take:

+ Validate the credit card data that customers enter on your form.
+ Send a POST request to Vault endpoint with the credit card data.
+ Paytrek will return you a card token.
+ Charge the customer with that token through `charge_with_token` endpoint.

## Vaulting Card

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
    "card_holder_name": "Joe Doe",
    "number": "4508034508034509",
    "expiration": "12/2020",
    "cvc": "000",
    "card_label": "Ted"
}
```

> Response Code

```
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "request_id": null,
    "sys_time": 1521015364,
    "language_code": "tr",
    "card_token": "2b54e2f7-58ad-40e3-9a71-f3bc9333c406",
    "created_at": "2018-03-14T08:16:04.001969Z",
    "bin_number": "450803",
    "last4": "4509",
    "card_brand": "visa",
    "card_type": "credit",
    "card_user_id": "b561169d7a2d467f885a7aedf7b10cf3",
    "card_label": "Ted",
    "card_issuer": "isbank",
    "card_country": "TR"
}
```

| Request Type | Endpoint |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/vault/ |

| Parameter | Type | Description |
|:-|:-|:-|
|number | number, required | The credit card number.|
|expiration | string, required| The expiration date of the credit card, in MM/YY format.|
|cvc | number, required| The security number of the credit card.|
|card_holder_name | string, required| The name on card.|
|card_label | string | The optional card label name.|

## Retrieve Vaulted Card


> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
        {
            "request_id": null,
            "sys_time": 1521015364,
            "language_code": "tr",
            "card_token": "2b54e2f7-58ad-40e3-9a71-f3bc9333c406",
            "created_at": "2018-03-14T08:16:04.001969Z",
            "bin_number": "450803",
            "last4": "4509",
            "card_brand": "visa",
            "card_type": "credit",
            "card_user_id": "b561169d7a2d467f885a7aedf7b10cf3",
            "card_label": "Ted",
            "card_issuer": "isbank",
            "card_country": "TR"
        }
```

| Request Type | Endpoint |
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/vault/**card_token**/ |

| Parameter | Type | Description |
|:-|:-|:-|
| card_token | string, required | Card token after vault operation. |

## Listing Vaulted Cards

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
    {
        "count": 2,
        "next": null,
        "previous": null,
        "results": [
        {
            "request_id": null,
            "sys_time": 1540562575,
            "language_code": "en-us",
            "card_token": "aac3a45b-8ced-4e4c-8ebb-c88ceff272f6",
            "created_at": "2018-10-26T13:59:45.207410Z",
            "bin_number": "450803",
            "last4": "4509",
            "card_brand": "visa",
            "card_type": "credit",
            "card_user_id": "10001",
            "card_label": "",
            "card_issuer": "isbank",
            "card_country": "TR"
        },
        {
            "request_id": null,
            "sys_time": 1540562575,
            "language_code": "en-us",
            "card_token": "0f46ba98-d9d2-4fd3-93e4-6d23b4b48839",
            "created_at": "2018-10-26T13:59:46.445397Z",
            "bin_number": "450803",
            "last4": "4509",
            "card_brand": "visa",
            "card_type": "credit",
            "card_user_id": "10001",
            "card_label": "",
            "card_issuer": "isbank",
            "card_country": "TR"
        }
    ]
}
```

| Request Type | Endpoint |
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/vault/ |

| Parameter | Type | Description |
|:-|:-|:-|
| card_user_id | optional | Card user id (you can set when you create vault)|
| card_label | optional | Card label|


## Delete Vaulted Card

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
204
```
> Response Headers

```json
"Content-Type": "application/json"
```

| Request Type | Endpoint |
|:-:|:-:|
| DELETE | https://sandbox.paytrek.com/api/v2/vault/**card_token**/ |

| Parameter | Type | Description |
|:-|:-|:-|
|card_token| string | Card token after vault operation.|


# Charge With Token


> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token":"5f20ca43d47244509949eb32c80e522a",
  "card_token":"914dcf0b-236b-44e9-9b31-3925af88cdc5"
}
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "exp_month": "12",
    "number": "450803******4509",
    "payment_product": null,
    "sale_token": "5f20ca43d47244509949eb32c80e522a",
    "exp_year": "2020",
    "sale_risk": null,
    "card_holder_name": "J********",
    "dcc_currency": null,
    "succeeded": true,
    "error_message": null,
    "issuer_bank_id": "isbank",
    "secure_charge": false,
    "cvc": "***",
    "return_url": "?token=5f20ca43d47244509949eb32c80e522a"
}
```

This enpoint provides to charge the customer
with created card token. You will notice there is no amount or quantity
parameters for this endpoint. You must create a sale by using Sale endpoint
and card token by using Vault endpoint before charging your customer.

| Request Type | Endpoint |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/charge_with_token/ |

| Parameter | Type | Description |
|:-|:-|:-|
|sale_token | string, required | The sale token created though sale endpoint.|
|card_token | string, required | The card token created though vault endpoint.|

# Capture

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token": "b39887704acb480e81ab8611ae256719",
  "comments": "testing"
}
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "sale_token": "a16a810b503c4a91bb5c3d883617530a",
    "amount": "24.72",
    "sale_amount": "24.72",
    "sale_id": 97510,
    "succeeded": true
}
```

Capture endpoint provides to capture a payment.
If you want to capture a payment that you've already pre-authorized, all you need to do is sending a POST request to the capture resource’s endpoint with the pre-authorized sale’s token.
Please note that you cannot capture a payment that is not pre-authorized. If you would like to authorize a payment, you must create the sale with pre_auth field set to true.
If your sale was in review status, the sale’s fraud decision will be updated as accepted after you capture it through the API.

Flow of pre-authorized payment :

+ Send a request to `direct_charge` endpoint within required data and `pre_auth: true`.
+ After that use `sale_token` to capture the payment.



| Request Type | Endpoint |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/capture/ |

|Parameter|Type|Description|
|:-:|:-|:-|
|sale_token | string, required | The sale token created though sale endpoint.|
|comments | string, optional | Comments for accepting the fraud review decision.|

<aside class="notice">
You must check <a href="#retrieve-sale"><b> Sale </b></a> status when you complete charging with this endpoints.
</aside>

# Cancel

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token": "b39887704acb480e81ab8611ae256719",
  "comments": "testing"
}

```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "sale_token": "a16a810b503c4a91bb5c3d883617530a",
    "amount": 24,
    "sale_amount": 24.72,
    "succeeded": true
}
```

Cancel endpoint provides to cancel and refund a payment.
When a request is sent to the Cancel endpoint, it will issue a full refund.
If you’d like to issue a partial refund use ```Refund``` endpoint.
In both of cases, the response will contain an amount information.
If your sale was in review status, the sale’s fraud decision will be updated as rejected after you cancel it through the API.



| Request Type | Endpoint |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/cancel/ |

|Parameter|Type|Description|
|:-:|:-|:-|
|sale_token | string, required | The sale token created though sale endpoint.|
|comments | string, optional | Comments for accepting the fraud review decision. |

# Refund

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token": "b39887704acb480e81ab8611ae256719",
  "amount": 9.70,
  "comments": "testing"
}

```

> Response Code

```
200
```

> Response Headers

```json
{
    "total_refunded_amount": 9.7,
    "succeeded": true,
    "sale_token": "b39887704acb480e81ab8611ae256719",
    "sale_amount": 50,
    "sale_id": 89890,
    "amount": 9.7
}
```

> Response Body

```json
{
    "sale_token": "a16a810b503c4a91bb5c3d883617530a",
    "amount": 24,
    "sale_amount": 24.72,
    "succeeded": true
}
```

Refund endpoint provides refund a payment.
When a request is sent to the Refund Resource, Paytrek will first try to cancel the payment.
If there’s any error with the cancel request, it will issue a full refund.
If you’d like to issue a partial refund, you’ll need to send the amount parameter.
In both of cases, the response will contain an amount information.
If your sale was in review status,
the sale’s fraud decision will be updated as rejected
after you cancel it through the API.

| Request Type | Endpoint |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/refund/ |

|Parameter|Type|Description|
|:-:|:-|:-|
|sale_token | string, required | The sale token that charged sale object. |
|amount | number, optional | Amount to refund.|
|comments | string, optional | Comments for accepting the fraud review decision.|

# Subscription Plan

Paytrek allows you to create subscription plans for recurring payments.
Subscription plan endpoint requires almost the same request parameters
with [`Direct Charge`](#direct-charge) except `recurring` one.

It's a json block that includes following parameters for creating a new plan.
```json
"recurring": {
    "name": "Test Store Recurring Payments",
    "recurring_period": "Monthly",
    "start_date": "2018-08-30",
    "recurring_frequency": 4
}
```
If `start_date` isn't settled then **first payment** will be charged today.
In addition, it's mandatory to set `save_card: true` while creating subscription plan.

#### Recurring Periods

 - Daily
 - Weekly
 - Monthly
 - Yearly



## Create Subscription Plan


> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
    "secure_option": "false",
    "card_holder_name": "John Doe",
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "sale_data": {},
    "recurring" : {
        "name": "Test Store Recurring Payments",
        "recurring_period": "Monthly",
        "start_date": "2018-06-30",
        "recurring_frequency": 4
    },
    "save_card": true,
    "number": "4508034508034509",
    "cvc": "000",
    "customer_last_name": "Doe",
    "billing_state": "CA",
    "return_url": "https://www.google.com.tr/",
    "currency": "TRY",
    "amount": "100.00",
    "customer_first_name": "John",
    "billing_country": "US",
    "billing_zipcode": "34410",
    "billing_city": "San Francisco",
    "customer_ip_address": "212.57.9.204",
    "customer_email": "accept@paytrek.com",
    "installment": 1,
    "fraud_check_enabled": false,
    "expiration": "12/2020",
    "items": [],
    "order_id": "20180424141314",
    "half_secure": false
}

```

> Response Code

```
200
```

> Response Headers

```json
{
    "total_refunded_amount": 9.7,
    "succeeded": true,
    "sale_token": "b39887704acb480e81ab8611ae256719",
    "sale_amount": 50,
    "sale_id": 89890,
    "amount": 9.7
}
```

> Response Body

```json
{
    "secure_option": true,
    "half_secure": false,
    "currency": "TRY",
    "customer_email": "accept@paytrek.com",
    "language_code": "tr-tr",
    "card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
    "billing_country": "US",
    "customer_ip_address": "212.57.9.204",
    "description": null,
    "customer_last_name": "Doe",
    "billing_zipcode": "34410",
    "financial_status": null,
    "billing_phone": null,
    "sale_data": {},
    "card_brand": "visa",
    "sys_time": 1529389543,
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "card_country": "TR",
    "transactions": [],
    "order_id": "20180424141314",
    "expires_at": "2018-06-19T07:25:42.720412Z",
    "card_issuer": "isbank",
    "hosted_payment": true,
    "sale_token": "8639b66510424eca9b104f5ad0be634e",
    "subscription_plan": {
        "cancelled_at": null,
        "vaults": [
            {
                "card_type": "credit",
                "last4": "4509",
                "token": "ab5b2706-75c6-4929-8269-9f410fe54178",
                "card_brand": "visa"
            }
        ],
        "name": "Test Store Recurring Payments",
        "plan_token": "d0351fe3-4c05-4611-8c38-c07ff33679e0",
        "is_active": true,
        "fixed_currency_amount": null,
        "subscriptions": [
            {
                "status": "Active",
                "payment_date": "2018-10-18T21:00:00Z",
                "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75"
            },
            {
                "status": "Active",
                "payment_date": "2018-09-18T21:00:00Z",
                "subscription_token": "a1ad588a-9291-4d14-a4ee-ec94d07389ab"
            },
            {
                "status": "Active",
                "payment_date": "2018-08-18T21:00:00Z",
                "subscription_token": "9b5ee804-5399-423a-a0b7-794f1145ea4a"
            },
            {
                "status": "Active",
                "payment_date": "2018-07-18T21:00:00Z",
                "subscription_token": "73a3eea0-06f8-4113-9412-7c32f976e2e4"
            }
        ],
        "active_card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
        "created_at": "2018-06-19T06:25:43.520000Z",
        "description": null
    },
    "billing_city": "San Francisco",
    "installment": 1,
    "last4": "4509",
    "is_locked": false,
    "items": [],
    "created_at": "2018-06-19T06:25:43.421093Z",
    "refunded_amount": "0.00",
    "card_type": "credit",
    "amount": "100.00",
    "card_label": "",
    "status": "Created",
    "return_url": "https://www.google.com.tr/",
    "billing_company": null,
    "request_id": null,
    "customer_first_name": "John",
    "card_user_id": "69daf8cb6fe741a885cad8219bdb9dac",
    "callback_url": null,
    "billing_state": "CA",
    "bin_number": "450803"
}
```
|Parameter|Type|Description|
|:-|:-|:-|
|currency |string, required|3-letter ISO code for currency.|
|order_id |string, required|The order id of the sale.|
|amount  |number, required|The amount to charge the visitor.|
|number |number, required|The credit card number.|
|expiration |string, required|The expiration date of the credit card, in MM/YY format.|
|cvc |number, required|The security number of the credit card.|
|card_holder_name | The name on card.|
|recurring |object, required|Subscription plan proporties[1].|
|installment |number, required|The installment number of the sale.|
|secure_option |boolean|Whether the sale is secure(Default: False).|
|half_secure |boolean|Whether the sale can be charged if 3d enrollment check returns mdstatus 2, 3, 4.|
|return_url |string, required|Related link that user is redirected when payment completed.|
|pre_auth |boolean|Indicates whether this sale should be pre-authorized(Default: False).|
|items |object, required|The item related informations which is selling.|
|customer_first_name |string, required|Customer's first name.|
|customer_last_name |string, required|Customer's last name.|
|customer_email |string, required|Customer's email address.|
|customer_ip_address |string, required|Customer's ip address.  |
|billing_address |string, required|Customer's billing address.|
|billing_city |string, required|Customer's billing city.|
|billing_state |string|Customer's billing state, use postal abbreviations for US and CA.|
|billing_country |string, required|Customer's billing country, 2-letter ISO code.|
|billing_zipcode |string, required|Customer's billing zipcode.|
|billing_phone |string, required|Customer's billing phone number.|
|sale_data |object|Extra fields for sale.|

[1] Parameters of Recurring parameter(JSON);

|Parameter|Type|Description|
|:-|:-|:-|
|name|string, required|The name of subscriotion plan.|
|start_date|string|To define when subscription plan will begin.|
|recurring_period|string, required|The recurring period.|
|recurring_frequency|number, required|The recurring frequency to define how many periods.|

## Retrieve Subscription Plan

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "secure_option": true,
    "half_secure": false,
    "currency": "TRY",
    "customer_email": "accept@paytrek.com",
    "language_code": "tr-tr",
    "card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
    "billing_country": "US",
    "customer_ip_address": "212.57.9.204",
    "description": null,
    "customer_last_name": "Doe",
    "billing_zipcode": "34410",
    "financial_status": null,
    "billing_phone": null,
    "sale_data": {},
    "card_brand": "visa",
    "sys_time": 1529389543,
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "card_country": "TR",
    "transactions": [],
    "order_id": "20180424141314",
    "expires_at": "2018-06-19T07:25:42.720412Z",
    "card_issuer": "isbank",
    "hosted_payment": true,
    "sale_token": "8639b66510424eca9b104f5ad0be634e",
    "subscription_plan": {
        "cancelled_at": null,
        "vaults": [
            {
                "card_type": "credit",
                "last4": "4509",
                "token": "ab5b2706-75c6-4929-8269-9f410fe54178",
                "card_brand": "visa"
            }
        ],
        "name": "Test Store Recurring Payments",
        "plan_token": "d0351fe3-4c05-4611-8c38-c07ff33679e0",
        "is_active": true,
        "fixed_currency_amount": null,
        "subscriptions": [
            {
                "status": "Active",
                "payment_date": "2018-10-18T21:00:00Z",
                "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75"
            },
            {
                "status": "Active",
                "payment_date": "2018-09-18T21:00:00Z",
                "subscription_token": "a1ad588a-9291-4d14-a4ee-ec94d07389ab"
            },
            {
                "status": "Active",
                "payment_date": "2018-08-18T21:00:00Z",
                "subscription_token": "9b5ee804-5399-423a-a0b7-794f1145ea4a"
            },
            {
                "status": "Active",
                "payment_date": "2018-07-18T21:00:00Z",
                "subscription_token": "73a3eea0-06f8-4113-9412-7c32f976e2e4"
            }
        ],
        "active_card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
        "created_at": "2018-06-19T06:25:43.520000Z",
        "description": null
    },
    "billing_city": "San Francisco",
    "installment": 1,
    "last4": "4509",
    "is_locked": false,
    "items": [],
    "created_at": "2018-06-19T06:25:43.421093Z",
    "refunded_amount": "0.00",
    "card_type": "credit",
    "amount": "100.00",
    "card_label": "",
    "status": "Created",
    "return_url": "https://www.google.com.tr/",
    "billing_company": null,
    "request_id": null,
    "customer_first_name": "John",
    "card_user_id": "69daf8cb6fe741a885cad8219bdb9dac",
    "callback_url": null,
    "billing_state": "CA",
    "bin_number": "450803"
}
```

| Request Type | Endpoint |
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/subscription_plan/ |

| Parameter | Type | Description |
|:-|:-|:-|
|plan_token|Subscription plan token.|


### Update Subscription Plan
> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
    "subscriptions": [{
            "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75",
            "status": "Cancelled",
    }],
    "name": "New Test Store Recurring Payments"
}
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "cancelled_at": null,
    "vaults": [
        {
            "card_type": "credit",
            "last4": "4509",
            "token": "ab5b2706-75c6-4929-8269-9f410fe54178",
            "card_brand": "visa"
        }
    ],
    "name": "New Test Store Recurring Payments",
    "plan_token": "d0351fe3-4c05-4611-8c38-c07ff33679e0",
    "is_active": true,
    "fixed_currency_amount": null,
    "subscriptions": [
        {
            "status": "Cancelled",
            "payment_date": "2018-10-18T21:00:00Z",
            "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75"
        },
        {
            "status": "Active",
            "payment_date": "2018-09-18T21:00:00Z",
            "subscription_token": "a1ad588a-9291-4d14-a4ee-ec94d07389ab"
        },
        {
            "status": "Active",
            "payment_date": "2018-08-18T21:00:00Z",
            "subscription_token": "9b5ee804-5399-423a-a0b7-794f1145ea4a"
        },
        {
            "status": "Active",
            "payment_date": "2018-07-18T21:00:00Z",
            "subscription_token": "73a3eea0-06f8-4113-9412-7c32f976e2e4"
        }
    ],
    "active_card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
    "created_at": "2018-06-19T06:25:43.520000Z",
    "description": null
}
```

| Request Type | Endpoint |
|:-:|:-:|
| PUT | https://sandbox.paytrek.com/api/v2/subscription_plan/ |

|Parameter|Type|Description|
|:-|:-|:-|
|plan_token|Subscription plan token.|

# Installments

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
     "sys_time": 1520321002,
     "language_code": "en-us",
     "bin_number": "510118",
     "amount": "123.00",
     "banks": [
         {
             "card_brand": "mastercard",
             "card_type": "credit",
             "card_issuer": "denizbank",
             "card_country": "TR",
             "loyalty": "bonus",
             "is_commercial": false,
             "installments": [
                 {
                     "number": 1,
                     "commission": "9.92",
                     "instalment_amounts": "132.92",
                     "total_amount": "132.92",
                     "total_amount_wo_commission": "113.08"
                 },
                 {
                     "number": 2,
                     "commission": "9.92",
                     "instalment_amounts": "66.46",
                     "total_amount": "132.92",
                     "total_amount_wo_commission": "113.08"
                 },
                 {
                     "number": 3,
                     "commission": "9.92",
                     "instalment_amounts": "44.31",
                     "total_amount": "132.92",
                     "total_amount_wo_commission": "113.08"
                 },
                 {
                     "number": 4,
                     "commission": "5.46",
                     "instalment_amounts": "32.12",
                     "total_amount": "128.46",
                     "total_amount_wo_commission": "117.54"
                 },
                 {
                     "number": 5,
                     "commission": "5.46",
                     "instalment_amounts": "25.69",
                     "total_amount": "128.46",
                     "total_amount_wo_commission": "117.54"
                 },
                 {
                     "number": 6,
                     "commission": "5.46",
                     "instalment_amounts": "21.41",
                     "total_amount": "128.46",
                     "total_amount_wo_commission": "117.54"
                 }
             ]
         }
     ]
 }
```

Installments endpoint provides to list installment alternatives and
cards through by given ```bin_number``` and ```amount``` as parameters in url.




| Request Type | Endpoint|
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/installments/?bin_number=bin_number&amount=amount |

|Parameter|Type|Description|
|:-|:-|:-|
|bin_number | optional | First 6 digits of card.|
|amount | - | Amount for installment plans.|
