# FX4Biz-REST API #

The FX4Biz-REST API provides a simplified, easy-to-use interface to the FX4BIZ accounts & operations via a RESTful API. This page explains how to use the API to execute FX trades, send cross-boarder payments and monitoring accounts with FX4Biz.

We recommend FX4Biz-REST for financial institutions just getting started with FX4Biz, since it provides high-level abstractions and convenient simplifications in the data format. 

## Available API Routes ##

Our API is divided into sections based on different concepts in our system. Each section is made up of a series of calls.

### Authenticate ###

* [Get User Login - `GET /v1/login-user`](#get-login-user)
* [Get End Session - `GET /v1/end-session`](#get-end-session)

#### Accounts ####

* [Submit Account - `POST /v1/accounts`](#post-account-create)
* [Get Wallets - `GET /v1/accounts/list`](#get-wallets-list)
* [Get Account Balances - `GET /v1/accounts/{:id}/balances`](#get-account-balances)
* [Get Account Details - `GET /v1/accounts/{:id}/details`](#get-account-details)
* [Get Transfer History - `GET /v1/accounts/{:id}/transfer/list`](#get-payment-history)
* [Get Transfer Details - `GET /v1/accounts/{:id}/transfers/{:id}/details`](#get-transfer-details)
* [Delete Account - `DELETE /v1/accounts/{:id}`](#delete-account)

#### Payments ####

* [Submit Payment - `POST /v1/payments`](#submit-payment)
* [Confirm Payment - `GET /v1/payments/{:id}/confirm`](#confirm-payment)
* [Get Payment History - `GET /v1/payments/history`](#get-payment-history)
* [Get Payment Details - `GET /v1/payments/{:id}/details`](#get-payment-history)
* [Post Payment Update - `POST /v1/payments/{:id}/update`](#post-payment-update)
* [Cancel Payment  - `DELETE /v1/payments/{:id}/delete`](#delete-payment)

#### Trades ####

* [Submit Trade - `POST /v1/trades`](#Submit-trade)
* [Cancel Trade - `DELETE /v1/trades/{:id}/delete`](#cancel-order)
* [Get Trade Book - `GET /v1/trades/book`](#get-trade-book)
* [Get Trade Details - `GET /v1/trades/{:id}/details`](#get-order-book)
* [Get Order Transaction - `GET /v1/accounts{:address}/orders/{:hash}`](#get-order-transaction)

#### Rates ####

* [Get Rates - `GET /v1/rates`](#Submit-rates)

#### Quotes ####

* [Request Quote - `POST /v1/quotes`](#Submit-rates)

#### Notifications ####

* [Get Notifications History - `GET /v1/notifications/history`](#get-notifications-history)

#### Status ####

* [Check Connection - `GET /v1/server/connected`](#check-connection)
* [Get Server Status - `GET /v1/server`](#get-server-status)


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
| currency | String | Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying which currency. Alternatively, a 160-bit hex value. |
| accountId | String | This is usually an id given by FX4Biz. 


Example Amount Object:

```js
{
  "value": "10000.00",
  "currency": "USD",
  "accountId": "rMwjYedjc7qqtKYVLiAccJSmCwih4LnE2q"
}
```

## <a id="payment_object"></a> Payment Objects ##

The `Payment` object is a simplified version of the standard Ripple transaction format.

This `Payment` format is intended to be straightforward to create and parse, from strongly or loosely typed programming languages. Once a transaction is processed and validated it also includes information about the final details of the payment.

An example Payment object looks like this:

```js
{

    "source_address": "rKXCummUHnenhYudNb9UoJ4mGBR75vFcgz",
    "source_tag": "",
    "source_amount": {
        "value": "0.001",
        "currency": "XRP",
        "counterparty": ""
    },
    "source_slippage": "0",
    "destination_address": "rNw4ozCG514KEjPs5cDrqEcdsi31Jtfm5r",
    "destination_tag": "",
    "destination_amount": {
        "value": "0.001",
        "currency": "XRP",
        "counterparty": ""
    },
    "invoice_id": "",
    "paths": "[]",
    "flag_no_direct_ripple": false,
    "flag_partial_payment": false
}
```

The fields of a Payment object are defined as follows:

| Field | Type | Description |
|-------|------|-------------|
| `source_account` | String | The Ripple address of the account sending the payment |
| `source_amount` | [Amount Object](#amount_object) | The amount to deduct from the account sending the payment. |
| `destination_account` | String | The Ripple address of the account receiving the payment |
| `destination_amount` | [Amount Object](#amount_object) | The amount that should be deposited into the account receiving the payment. |
| `source_tag` | String (Quoted unsigned integer) | (Optional) A quoted 32-bit unsigned integer (0-4294967294, inclusive) to indicate a sub-category of the source account. Typically, it identifies a hosted wallet at a gateway as the sender of the payment. |
| `destination_tag` | String (Quoted unsigned integer) | (Optional) A quoted 32-bit unsigned integer (0-4294967294, inclusive) to indicate a particular sub-category of the destination account. Typically, it identifies a hosted wallet at a gateway as the recipient of the payment. |
| `source_slippage` | String (Quoted decimal) | (Optional) Provides the `source_amount` a cushion to increase its chance of being processed successfully. This is helpful if the payment path changes slightly between the time when a payment options quote is given and when the payment is submitted. The `source_address` will never be charged more than `source_slippage` + the `value` specified in `source_amount`. |
| `invoice_id` | String | (Optional) Arbitrary 256-bit hash that can be used to link payments to an invoice or bill. |
| `paths` | String | A "stringified" version of the Ripple PathSet structure. You can get a path for your payment from the [Prepare Payment](#prepare-payment) method. |
| `no_direct_ripple` | Boolean  | (Optional, defaults to false) `true` if `paths` are specified and the sender would like the Ripple Network to disregard any direct paths from the `source_address` to the `destination_address`. This may be used to take advantage of an arbitrage opportunity or by gateways wishing to issue balances from a hot wallet to a user who has mistakenly set a trustline directly to the hot wallet. Most users will not need to use this option. |
| `partial_payment` | Boolean | (Optional, defaults to false) If set to `true`, fees will be deducted from the delivered amount instead of the sent amount. (*Caution:* There is no minimum amount that will actually arrive as a result of using this flag; only a miniscule amount may actually be received.) See [Partial Payments](https://ripple.com/build/transactions#partial-payments) |
| `memos` | Array | (Optional) Array of [memo objects](#memo-objects), where each object is an arbitrary note to send with this payment. |

Submitted transactions can have additional fields reflecting the current status and outcome of the transaction, including:

[[Source]<br>](https://github.com/ripple/ripple-rest/blob/59ea02d634ac4a308db2ba21781efbc02f5ccf53/lib/tx-to-rest-converter.js#L25 "Source")

| Field | Type | Description |
|-------|------|-------------|
| direction | String | The direction of the payment relative to the account from the URL, either `"outgoing"` (sent by the account in the URL) or `"incoming"` (received by the account in the URL) |
| result | String | The [Ripple transaction status code](https://wiki.ripple.com/Transaction_errors) for the transaction. A value of `"tesSUCCESS"` indicates a successful transaction. |
| timestamp | String | The time the ledger containing this transaction was validated, as a [ISO8601 extended format](http://en.wikipedia.org/wiki/ISO_8601) string in the form `YYYY-MM-DDTHH:mm:ss.sssZ`. |
| fee | String (Quoted decimal) | The amount of XRP charged as a transaction fee. |
| balance_changes | Array | Array of [Amount objects](#amount_object) indicating changes in balances held by the perspective account (i.e., the Ripple account address specified in the URI). |
| source_balance_changes | Array | Array of [Amount objects](#amount_object) indicating changes in balances held by the account sending the transaction as a result of the transaction. |
| destination_balance_changes | Array | Array of [Amount objects](#amount_object) indicating changes in balances held by the account receiving the transaction as a result of the transaction. |
| destination_amount_submitted | Object | An [Amount object](#amount_object) indicating the destination amount submitted (useful when `payment.partial_payment` flag is set to *true* |
| order_changes | Array | Array of [Amount objects](#amount_object) indicating changes to orders caused by the Payment. |
| source_amount_submitted | Object | An [Amount object](#amount_object) indicating the source amount submitted (useful when `payment.partial_payment` flag is set to *true* |

