# FX4Biz-REST API #

The FX4Biz-REST API provides a simplified, easy-to-use interface to the FX4BIZ accounts & operations via a RESTful API. This page explains how to use the API to execute FX trades, send cross-boarder payments and monitoring accounts with FX4Biz.

We recommend FX4Biz-REST for financial institutions just getting started with FX4Biz, since it provides high-level abstractions and convenient simplifications in the data format. 

## Available API Routes ##

#### Accounts ####

* [Get Wallets - `GET /v1/wallets`](#get-wallets-list)
* [Get Account Balances - `GET /v1/accounts/{:address}/balances`](#get-account-balances)
-> To be determined

#### Payments ####

* [Submit Payment - `POST /v1/accounts/{:source_address}/payments`](#submit-payment)
* [Confirm Payment - `GET /v1/accounts/{:address}/payments/{:id}`](#confirm-payment)
* [Get Payment History - `GET /v1/accounts/{:address}/payments`](#get-payment-history)
-> To be determined

#### Orders ####

* [Place Order - `POST /v1/accounts/{:address}/orders`](#place-order)
* [Cancel Order - `DELETE /v1/accounts/{:address}/orders/{:sequence}`](#cancel-order)
* [Get Account Orders - `GET /v1/accounts/{:address}/orders`](#get-account-orders)
* [Get Order Book - `GET /v1/accounts/{:address}/order_book/{:base}/{:counter}`](#get-order-book)
* [Get Order Transaction - `GET /v1/accounts{:address}/orders/{:hash}`](#get-order-transaction)

#### Trustlines ####

* [Get Trustlines - `GET /v1/accounts/{:address}/trustlines`](#get-trustlines)
* [Grant Trustline - `POST /v1/accounts/{:address}/trustlines`](#grant-trustline)

#### Notifications ####

* [Check Notifications - `GET /v1/accounts/{:address}/notifications/{:id}`](#check-notifications)

#### Status ####

* [Check Connection - `GET /v1/server/connected`](#check-connection)
* [Get Server Status - `GET /v1/server`](#get-server-status)

#### Utilities ####

* [Retrieve Ripple Transaction - `GET /v1/transactions/{:id}`](#retrieve-ripple-transaction)
* [Retrieve Transaction Fee - `GET /v1/transaction-fee`](#retrieve-transaction-fee)
* [Generate UUID - `GET /v1/uuid`](#create-client-resource-id)


## API Overview ##

### FX4Biz Concepts ###

FX4Biz is a payment institution for FX transactions and cross-boarder payment. You can use FX4Biz to hold accounts, in any currency, at no cost, make real time FX trades & send money anywhere in the world.

In the FX4Biz world, 
-> Authentification process

A FX4Biz payment can be sent using FX4Biz' wallet account, directly from one account to another, at no cost.  Payments can also be sent in external bank accounts, prealably referenced in the platform.

Payments are made between two accounts, by specifying the #id destination accounts of the funds. FX4Biz will automatically debit the wallet account in the currency at the date specified in the payments instructions. If no date is specified we will debit the wallet account in the payment currency at the closest operation date available. A payment also involves an amount to be transferred.

FX trades are made between two wallet accounts. FX4Biz will automatically debit the source wallet account and credit the destination wallet account at the date specified in the FX trade instructions. If no date is specified, we will execute the operation at the closest tradable date available. A FX trades also involves an amount, which includes both the numeric amount and the currency in order to define is this amount is to be buy or sell, for example: '100000.00+GBP'.

Although the FX4Biz-REST API provides a high-level interface to FX4Biz, there are also API methods for monitoring your wallet account balance & transfer informations of received or sent payments on those accounts. 

### Sending Payments ###

Sending a payment involves two steps:

1. Generate the payment object with the [Create Payment method](#CreatePayment). 
When you submit a payment to be scheduled, you assign a unique id to that payment. Thi is a string which uniquely identifies the payment, and ensures that you do not accidentally submit the same payment twice. You can also use the id of the payment to retrieve a payment once he has been submitted.
-> Is it true?
*Caution:* The payment created will be automatically rolled to the next closest working days if not confirmed in the scheduled date of operation.

2. Confirm the payment to the API for processing, using the [Confirm Payment method](#ConfirmPayment). 
When you confirm a payment for processing, make sure you have sufficient funds in your wallet account balance. The funds transfer will be automatically locked-in if the wallet account balance is not sufficient. Make sure you always have enough funds on your ewallet.
*Caution:* Payment submission is an asynchronous process, so payments can fail even after they have been submitted and confirmed successfully. 
-> is it true?

### Placing Trades ###

Placing a trade



# Formatting Conventions #

The `FX4Biz-rest` API conforms to the following general behavior for [RESTful API](http://en.wikipedia.org/wiki/Representational_state_transfer):

* You make HTTP (or HTTPS) requests to the API endpoint, indicating the desired resources within the URL itself. (The public server, for the sake of security, only accepts HTTPS requests.)
* The HTTP method identifies what you are trying to do.  Generally, HTTP `GET` requests are used to retrieve information, while HTTP `POST` requests are used to make changes or submit information.
* If more complicated information needs to be sent, it will be included as JSON-formatted data within the body of the HTTP POST request.
  * This means that you must set `Content-Type: application/json` in the headers when sending POST requests with a body.
* Upon successful completion, the server returns an [HTTP status code](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) of 200 OK, and a `Content-Type` value of `application/json`.  The body of the response will be a JSON-formatted object containing the information returned by the endpoint.

As an additional convention, all responses from Ripple-REST contain a `"success"` field with a boolean value indicating whether or not the success

## Errors ##

When errors occur, the server returns an HTTP status code in the 400-599 range, depending on the type of error. The body of the response contains more detailed information on the cause of the problem.

In general, the HTTP status code is indicative of where the problem occurred:

* Codes in the 200-299 range indicate success. 
    * Unless otherwise specified, methods are expected to return `200 OK` on success.
* Codes in the 400-499 range indicate that the request was invalid or incorrect somehow. For example:
    * `400 Bad Request` occurs if the JSON body is malformed. This includes syntax errors as well as when invalid or mutually-exclusive options are selected.
    * `404 Not Found` occurs if the path specified does not exist, or does not support that method (for example, trying to POST to a URL that only serves GET requests)
* Codes in the 500-599 range indicate that the server experienced a problem. This could be due to a network outage or a bug in the software somewhere. For example:
    * `500 Internal Server Error` occurs when the server does not catch an error. This is always a bug. If you can reproduce the error, file it at [our bug tracker in your FX4Biz platform](https://fx4bizplatform.com/login).
    * `502 Bad Gateway` occurs if FX4Biz-REST could not contact its `FX4Biz` server at all.
    * `504 Gateway Timeout` occurs if the `FX4Biz` server took too long to respond to the FX4Biz-REST server.

When possible, the server provides a JSON response body with more information about the error. These responses contain the following fields:

| Field | Type | Description |
|-------|------|-------------|
| success | Boolean | `false` indicates that an error occurred. |
| error_type | String | A short code identifying a general category for the error that occurred. |
| error | String | A human-readable summary of the error that occurred. |
| message | String | (May be omitted) A longer human-readable explanation for the error. |

Example error:

```js
{
    "success": false,
    "error_type": "invalidId",
    "error": "Invalid parameter: IdAccount",
    "message": "Your payment must have a counterparty"
}
```

## Quoted Numbers ##

In any case where a large number should be specified, FX4Biz-REST uses a string instead of the native JSON number type. This avoids problems with JSON libraries which might automatically convert numbers into native types with differing range and precision.

You should parse these numbers into a numeric data type with adequate precision. If it is not clear how much precision you need, we recommend using an arbitrary-precision data type.


### Amounts in JSON ###

When an amount of currency is specified as part of a JSON body, it is encoded as an object with three fields:

| Field | Type | Description |
|-------|------|-------------|
| value | String (Quoted decimal) | The quantity of the currency |
| currency | String | Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying which currency. Alternatively, a 160-bit hex value. (Some advanced features, like [demurrage](https://ripple.com/wiki/Gateway_demurrage), require the hex version.) |
| counterparty | String | (New in [v1.4.0](https://github.com/ripple/ripple-rest/releases/tag/1.4.0)) The Ripple address of the account that is a counterparty to this currency. This is usually an [issuing gateway](https://wiki.ripple.com/Gateway_List). Always omitted, or an empty string, for XRP. |
| issuer | String | (Prior to 1.4.0) **DEPRECATED** alias for `counterparty`.


Example Amount Object:

```js
{
  "value": "1.0",
  "currency": "USD",
  "counterparty": "rMwjYedjc7qqtKYVLiAccJSmCwih4LnE2q"
}
```

or for XRP:

```js
{
  "value": "1.0",
  "currency": "XRP",
  "counterparty": ""
}
```

The `value` field can get very large or very small. See the [Currency Format](https://wiki.ripple.com/Currency_Format) for the exact limits of Ripple's precision.

### Amounts in URLs ###

When an amount of currency has to be specified in a URL, you use the same fields as the JSON object -- value, currency, and counterparty -- but concatenate them with `+` symbols in that order.

Example Amount:

`1.0+USD+rMwjYedjc7qqtKYVLiAccJSmCwih4LnE2q`

When specifying an amount of XRP, you must omit the counterparty entirely. For example:

`1.0+XRP`

### Counterparties in Payments ###

Most of the time, the `counterparty` field of a non-XRP currency indicates the account of the gateway that issues that currency. However, when describing payments, there are a few nuances that are important:

* There is only ever one balance for the same currency between two accounts. This means that, sometimes, the `counterparty` field of an amount actually refers to a counterparty that is redeeming issuances, instead of the account that created the issuances.
* You can omit the counterparty from the `destination_amount` of a payment to mean "any counterparty that the destination accepts". This includes all accounts to which the destination has extended trust lines, as well as issuances created by the destination which may be held on other trust lines. 
    * For compatibility with `rippled`, setting the `counterparty` of the `destination_amount` to be the destination account's address means the same thing.
* You can omit the counterparty from the `source_amount` of a payment to mean "any counterparty the source can use". This includes creating new issuances on trust lines that other accounts have extended to the source account, as well as issuances from other accounts that the source account possesses.
    * Similarly, setting the `counterparty` of the `source_amount` to be the source account's address means the same thing.


