# FX4Biz-REST API #

The FX4Biz-REST API provides a simplified, easy-to-use interface to the FX4BIZ accounts & operations via a RESTful API. This page explains how to use the API to execute FX trades, send cross-boarder payments and monitoring accounts with FX4Biz.

We recommend FX4Biz-REST for financial institutions just getting started with FX4Biz, since it provides high-level abstractions and convenient simplifications in the data format. 

## Available API Routes ##

Our API is divided into sections based on different concepts in our system. Each section is made up of a series of calls.

#### Authenticate ####

* [Get User Login - `GET /login-user`](#get-login-user)
* [Get End Session - `GET /end-session`](#get-end-session)

#### Accounts ####

* [Submit New Account - `POST /accounts`](#post-account-create)
* [Get Account list - `GET /accounts/list`](#get-account-list)
* [Get Account Balances - `GET /accounts/{:id}/balances`](#get-account-balances)
* [Get Account Details - `GET /accounts/{:id}/details`](#get-account-details)
* [Get Transfer History - `GET /accounts/{:id}/transfer/list`](#get-transfer-history)
* [Get Transfer Details - `GET /accounts/transfers/{:id}/details`](#get-transfer-details)
* [Delete Account - `DELETE /accounts/{:id}/delete`](#delete-account)

#### Payments ####

* [Submit Payment - `POST /payments`](#submit-payment)
* [Confirm Payment - `GET /payments/{:id}/confirm`](#confirm-payment)
* [Get Payment History - `GET /payments/history`](#get-payment-history)
* [Get Payment Details - `GET /payments/{:id}/details`](#get-payment-history)
* [Post Payment Update - `POST /payments/{:id}/update`](#post-payment-update)
* [Cancel Payment  - `DELETE /payments/{:id}/delete`](#delete-payment)

#### Trades ####

* [Submit Trade - `POST /trades`](#Submit-trade)
* [Cancel Trade - `DELETE /trades/{:id}/delete`](#cancel-order)
* [Get Trade Book - `GET /trades/book`](#get-trade-book)
* [Get Trade Details - `GET /trades/{:id}/details`](#get-order-book)
* [Get Order Transaction - `GET /accounts{:address}/orders/{:hash}`](#get-order-transaction)

#### Rates ####

* [Get Rates - `GET /rates`](#Submit-rates)

#### Quotes ####

* [Request Quote - `POST /quotes`](#Submit-rates)

#### Notifications ####

* [Get Notifications History - `GET /notifications/history`](#get-notifications-history)

#### Status ####

* [Check Connection - `GET /server/connected`](#check-connection)
* [Get Server Status - `GET /server`](#get-server-status)


## API Overview ##

### FX4Biz Concepts ###

FX4Biz is a payment institution for FX transactions and cross-boarder payment. You can use FX4Biz to hold accounts, in any currency, at no cost, make real time FX trades & send money anywhere in the world.

A FX4Biz payment can be sent using FX4Biz' wallet account, directly from one account to another, at no cost.  Payments can also be sent in external bank accounts, prealably referenced in the platform.

Payments are made between two accounts, by specifying the #id destination accounts of the funds. FX4Biz will automatically debit the wallet account in the currency at the date specified in the payments instructions. If no date is specified we will debit the wallet account in the payment currency at the closest operation date available. A payment also involves an amount to be transferred.

FX trades are made between two wallet accounts. FX4Biz will automatically debit the source wallet account and credit the destination wallet account at the date specified in the FX trade instructions. If no date is specified, we will execute the operation at the closest tradable date available. A FX trades also involves an amount, which includes both the numeric amount and the currency in order to define is this amount is to be buy or sell, for example: '100000.00+GBP'.

Although the FX4Biz-REST API provides a high-level interface to FX4Biz, there are also API methods for monitoring your wallet account balance & transfer informations of received or sent payments on those accounts. 

### Sending Payments ###

Sending a payment involves two steps:

1. Generate the payment object with the [Create Payment method](#post-account-create). 
When you submit a payment to be scheduled, you assign a unique id to that payment. Thi is a string which uniquely identifies the payment, and ensures that you do not accidentally submit the same payment twice. You can also use the id of the payment to retrieve a payment once he has been submitted.
-> Is it true?
*Caution:* The payment created will be automatically rolled to the next closest working days if not confirmed in the scheduled date of operation.

2. Confirm the payment to the API for processing, using the [Confirm Payment method](#confirm-payment). 
When you confirm a payment for processing, make sure you have sufficient funds in your wallet account balance. The funds transfer will be automatically locked-in if the wallet account balance is not sufficient. Make sure you always have enough funds on your ewallet.
*Caution:* Payment submission is an asynchronous process, so payments can fail even after they have been submitted and confirmed successfully. 
-> is it true?

### Placing Trades ###

FX4BIZ provide a deliverable FX facility and deliverable FX liquidity via the FX4Biz-rest API. You will become counterparty to FX4BIZ and can market and sell deliverable FX services to corporate and private clients as well as using such services on their behalf.

The FX4Biz-rest API supports online trading for the following contracts: TOD (Same-day settled for those currencies than can be), TOM (next-day settled), SPOT (T+2) and forward contracts up to one year. 

| Same day value Currencies | Cut Off Time |
|-----------------------------------------------------------------------------------------------------------------------|------|
| CHF | 8:30 |
| GBP | 10:30 |
| EUR | 16:00 |
| USD | 16:30 |

| Next day value Currencies (Times stated are for the day prior to the Value Date) | Cut Off Time |
|-------|------|
| AOA, ARS, BIF, BRL, CDF, CLP, COP, CRC, DJF, DOP, GHS, HNL, KES, MAD, NPR, PEN, PHP, RUB, TND, TRY, TZS, UGX, XOF/XAF | 10:00 |
| AED, AUD, CAD, CZK, DKK, HKD, HUF, JPY, NOK, NZD, PLN, SEK, SGD, ZAR | 10:30 |

# Formatting Conventions #

The `FX4Biz-rest` API conforms to the following general behavior for [RESTful API](http://en.wikipedia.org/wiki/Representational_state_transfer):

* You make HTTP (or HTTPS) requests to the API endpoint, indicating the desired resources within the URL itself. (The public server, for the sake of security, only accepts HTTPS requests.)
* The HTTP method identifies what you are trying to do.  Generally, HTTP `GET` requests are used to retrieve information, while HTTP `POST` requests are used to make changes or submit information.
* If more complicated information needs to be sent, it will be included as JSON-formatted data within the body of the HTTP POST request.
  * This means that you must set `Content-Type: application/json` in the headers when sending POST requests with a body.
* Upon successful completion, the server returns an [HTTP status code](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) of 200 OK, and a `Content-Type` value of `application/json`.  The body of the response will be a JSON-formatted object containing the information returned by the endpoint.

As an additional convention, all responses from FX4Biz-REST contain a `"success"` field with a boolean value indicating whether or not the success

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

## Amounts in JSON ##

When an amount of currency is specified as part of a JSON body, it is encoded as an object with two fields:

| Field | Type | Description |
|-------|------|-------------|
| value | String (Quoted decimal) | The quantity of the currency |
| currency | String | Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying which currency. Alternatively, a 160-bit hex value. |

Example Amount Object:

```js
{
  "value": "10000.00",
  "currency": "USD",
}
```

## Rates in JSON ##

When a rate is specified as part of a JSON body, it is encoded as an object with four fields:

| Field | Type | Description |
|-------|------|-------------|
| mid_market | String (Quoted decimal) | The average rate of the market between the bid and the ask rate |
| core | String (Quoted decimal) | The interbank rate provided by the FX partner of FX4BIZ |
| applied | String (Quoted decimal) | The rate applied by FX4Biz for this transaction |
| currency_pair | String | The cross of currency used for the rates provided |

Example Rates Object:

```js
{
  "mid_market": "1.1005",
  "core": "1.1004",
  "applied": "1.1002",
  "currency_pair": "EURUSD",
}
```
## Address in JSON ##

When an address is specified as part of a JSON body, it is encoded as an object with four fields:

| Field | Type | Description |
|-------|------|-------------|
| street | String | 1 My Road |
| post_code | String | ZIP |
| city | String | Miami |
| state_or_province | String | FL |
| country | String | US |

Example Address Object:

```js
{
  "street": "350 Avenue Louise",
  "post_code": "1050",
  "city": "Brussels",
  "state_or_province": "Brussels capitale",
  "Country": "Belgium"
}
```

# <a id="authentification_object"></a> Authentification Objects #

## <a id="get-login-user"></a> Login ##
-> TBD

## <a id="get-end-session"></a> End session ##
-> TBD

# <a id="account_object"></a> Account Objects #

## <a id="post-account-create"></a> Submit account ##

```
POST /v1/accounts/submit
```
There are two kinds of accounts with FX4BIZ. What we call wallet account, which is account hold in the FX4Biz' book and external bank account, which is account hold in another bank.
The API has been made in order to accept the local specification of cross-boarder payments.

The Api accepts the following formats of external bank accounts :
- Bic & Iban
- Local UK format
- Local US format
- Local CA format
- Other local formats (unspecified but different from the previous).

#### Beneficiary bank connected to SWIFT network ####

Required parameters:

| Field | Type | Description |
|-------|------|-------------|
| `bic` | [Beneficiary Bank Object](#beneficiary_bank_object) -> String |  |
| `currency` | [Beneficiary Account Object](#beneficiary_account_object) -> String | Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying which currency. |
| `reference` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |
| `holder_name` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |
| `holder_type` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |
| `holder_address` | [Beneficiary Account Object](#beneficiary_account_object) -> [Address Object](#address_object) |  |
| `number` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |

Optional parameters:

| Field | Type | Description |
|-------|------|-------------|
| `bic` | [Correspondent Bank Object](#correspondent_bank_object) -> String  |  |
| `clearing_type` | [Beneficiary bank Object](#beneficiary_account_object) -> String  |  |
| `clearing_code` | [Beneficiary bank Object](#beneficiary_account_object) -> String  |  |
| `name` | [Beneficiary bank Object](#beneficiary_account_object) -> String |  |
| `bank_address` | [Beneficiary bank Object](#beneficiary_account_object) -> [Address Object](#address_object) |  |

#### Beneficiary bank connected to a local clearing network ####

Required parameters:

| Field | Type | Description |
|-------|------|-------------|
| `bic` | [Correspondent Bank Object](#correspondent_bank_object) -> String |  |
| `clearing_type` | [Beneficiary bank Object](#beneficiary_account_object) -> String |  |
| `clearing_code` | [Beneficiary bank Object](#beneficiary_account_object) -> String |  |
| `name` | [Beneficiary bank Object](#beneficiary_account_object) -> String |  |
| `bank_address` | [Beneficiary bank Object](#beneficiary_account_object) -> [Address Object](#address_object) |  |
| `currency` | [Beneficiary Account Object](#beneficiary_account_object) -> String | Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying which currency. |
| `reference` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |
| `holder_name` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |
| `holder_type` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |
| `holder_address` | [Beneficiary Account Object](#beneficiary_account_object) -> [Address Object](#address_object) |  |
| `number` | [Beneficiary Account Object](#beneficiary_account_object) -> String |  |

## <a id="get-accounts-list"></a> Get accounts list ##

```
GET /v1/accounts/list
```
Request the list of accounts hold in the FX4Biz books.

Response example:

```js
-> TBD
```

## <a id="get-account-balances"></a> Get account balances ##

```
GET /v1/accounts/{:id}/balance
```
Retrieve the balance of an account hold in the FX4Biz books (i.e. Wallet account balance)

Url parameters:

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | xxx |

Optional parameters:

| Field | Type | Description |
|-------|------|-------------|
| `statement_date` | Date | YYYY-MM-DD In the response you will have the closing balance of the account at this date. |

Response example:

```js
{
  "id": "xxx"
  "statement_date": "2014-01-12T00:00:00+00:00",
  "amount": {
      "value": "1057.25",
      "currency": "EUR"
  }
  "Reference": "Sample account name",
}
```

## <a id="get-account-details"></a> Get account details ##

```
GET /v1/accounts/{:id}/details
```
Retrieve bank details & information on an account.

Url parameters:

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | xxx |

Response example:

```js
{
  "id": "xxx"
  "status": "active",
  "CreatedDate"= "2014-01-12T00:00:00+00:00",
		"CreatedBy"= "API",
		"Reference": "Sample account name",
		"Type": "External account",
		"CorrespondantBank":{
			  "Bic": "CHASUS33",
			  "Clearing" : {
				 "Type": "FW",
				 "Code": "1234567",
			},
			"Name": "JP MORGAN CHASE",
			"Address": {
				  "Street": "1 My Road",
				  "PostCode": "ZIP",
				  "CityName": "London",
				  "StateOrProvince": "",
				  "Country"; "UK",
			}
		},
		"BeneficiaryBank": {
			"Bic": "CHASUS33",
			"Clearing" : {
				"Type": "FW",
				"Code": "1234567",
			},
			"Name": "JP MORGAN CHASE",
			"Address": {
				"Street": "1 My Road",
				"PostCode": "ZIP",
				"CityName": "London",
				"StateOrProvince": "",
				"Country"; "UK",
			}
		},
		"Beneficiary": {
			"AccountCurrency": "....",
			"AccountHolder": {
				"Name": "....",
				"Address": {
					"Street": "1 My Road",
					"PostCode": "ZIP",
					"CityName": "London",
					"StateOrProvince": "",
					"Country"; "UK",
				}
			}
			"AccountNumber": "....",
		},
	 }
}
```

## <a id="get-transfer-history"></a> Get transfer history ##

```
GET /v1/accounts/wallets/{:id}/transfers/history
```
Request the list of transfers that has been received or sent on a particular wallet account at a specific period of time.

Url parameters:

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | xxx |

optional parameters:

| Field | Type | Description |
|-------|------|-------------|
| `from_date` | Date | YYYY-MM-DD |
| `to_date` | Date | YYYY-MM-DD |

Response example:

```js
-> TBD
```

## <a id="get-transfer-details"></a> Retrieve transfer details ##

```
GET v1/accounts/wallets/transfers/{:id}/details
```
Request information on a particular transfer that has been credited or debited to a wallet.

Url parameters:

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | xxx |

Response example:

```js
-> TBD
```

## <a id="delete-account"></a> Delete account ##

```
DELETE v1/accounts/{:id}
```
Delete an account.

Url parameters:

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | xxx |

Response example:

```js
-> TBD
```

# <a id="payment_object"></a> Payment Objects #

This `Payment` format is intended to be straightforward to create and parse, from strongly or loosely typed programming languages. Once a transaction is processed and validated it also includes information about the final details of the payment.

An example Payment object looks like this:

```js
{
"payment": {
    "id": "xxx",
    "status": "Awaiting confirmation",
    "created_date": "2014-01-12T00:00:00+00:00",
    "created_by": "Api",
    "confirmed_date"= "2014-01-12T00:00:00+00:00",
    "confirmed_by": "Api",
    "confirmed_date"= "2014-01-12T00:00:00+00:00",
    "initial_operation_date"= "2014-01-12T00:00:00+00:00",
    "operation_date"= "2014-01-12T00:00:00+00:00",
    "account": {
        "source_id": "xxx",
        "destination_id": "xxx",
    }
    "type": "Standard",
    "amount": {
        "value": "125000.00",
        "currency": "USD"
    }
}
}
```
## <a id="submit-payment"></a> Submitting a payment ##

```
POST /v1/payments
```
-> TBD

| Field | Type | Description |
|-------|------|-------------|
| `id` | [Account Object](#account_object) |
| `source_id` | String | The FX4Biz id of the account sending the payment |
| `destination_id` | String | The FX4Biz id of the account receiving the payment |
| `amount` | [Amount Object](#amount_object) | The amount to deduct from the account sending the payment and that should be deposited into the account receiving the payment. |

Response example:

```js
-> TBD
```

## <a id="confirm-payment"></a> Confirm a payment ##

```
POST /v1/payments/{:id}/confirm
```
-> TBD

Url parameters:

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | xxx |

Response example:

```js
-> TBD
```
