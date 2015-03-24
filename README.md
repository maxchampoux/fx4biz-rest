# FX4BIZ-REST API #

The FX4BIZ-REST API provides a simplified, easy-to-use interface to the FX4BIZ accounts & operations via a RESTful API. This page explains how to use the API to execute FX trades, send cross-boarder payments and monitoring accounts with FX4Biz.

We recommend FX4BIZ-REST for financial institutions just getting started with FX4BIZ, since it provides high-level abstractions and convenient simplifications in the data format. 

## Available API Routes ##

Our API is divided into sections based on different concepts in our system. Each section is made up of a series of calls.

#### Authenticate ####

* [Get User Login - `GET /login-user`](#get-login-user)
* [Get End Session - `GET /end-session`](#get-end-session)

#### Accounts ####

* [Submit New Account - `POST /accounts`](#post-account-create)
* [Retrieve Account list - `GET /accounts`](#get-account-list)
* [Retrieve Account Balance - `GET /account/{account_id}/balance`](#get-account-balance)
* [Retrieve Account Details - `GET /account/{account_id}/details`](#get-account-details)
* [Update Account Details - `PUT /account/{account_id}/details`](#put-account-details)
* [Retrieve Transfer History - `GET /transfers`](#get-transfer-history)
* [Retrieve Transfer Details - `GET /transfer/{transfer_id}`](#get-transfer-details)
* [Delete Account - `DELETE /account/{account_id}`](#delete-account)

#### Payments ####

* [Submit Payment - `POST /payments`](#submit-payment)
* [Confirm Payment - `PUT /payment/{payment_id}/confirm`](#confirm-payment)
* [Retrieve Payment History - `GET /payments`](#get-payment-history)
* [Retrieve Payment Details - `GET /payment/{payment_id}`](#get-payment-history)
* [Update Payment Details - `PUT /payment/{payment_id}`](#put-payment-details)
* [Cancel Payment  - `DELETE /payment/{payment_id}`](#delete-payment)

#### Trades ####

* [Retrieve Rates - `GET /rates`](#get-rates)
* [Request Quote - `POST /quotes`](#get-quote)
* [Submit Trade - `POST /trades`](#get-trade)
* [Cancel Trade - `DELETE /trade/{trade_id}`](#cancel-trade)
* [Retrieve Trades Book - `GET /trades`](#get-trade-book)
* [Retrieve Trade Details - `GET /trade/{trade_id}`](#get-trade-details)

#### Objects List ####

* [Account Object](#account_object)
* [Address Object](#address_object)
* [Beneficiary Bank Object](#beneficiary_bank_object)
* [Beneficiary Object](#beneficiary_object)
* [Correspondent Bank Object](#correspondent_bank_object)

#### Formatting Conventions ####

* [Errors](#errors_conventions)
* [Quoted numbers](#quoted_numbers)

## API Overview ##

### FX4BIZ Concepts ###

FX4BIZ is a payment institution for FX transactions and cross-boarder payment. You can use FX4BIZ to hold accounts, in any currency, at no cost, make real time FX trades & send money anywhere in the world.

A FX4BIZ payment can be sent using FX4BIZ wallet account, directly from one account to another, at no cost.  Payments can also be sent in external bank accounts, prealably referenced in the platform.

Payments are made between two accounts, by specifying the #id destination accounts of the funds. FX4BIZ will automatically debit the wallet account in the currency at the date specified in the payments instructions. If no date is specified we will debit the wallet account in the payment currency at the closest operation date available. A payment also involves an amount to be transferred.

FX trades are made between two wallet accounts. FX4BIZ will automatically debit the source wallet account and credit the destination wallet account at the date specified in the FX trade instructions. If no date is specified, we will execute the operation at the closest tradable date available. A FX trades also involves an amount, which includes both the numeric amount and the currency in order to define is this amount is to be buy or sell, for example: '100000.00+GBP'.

Although the FX4BIZ-REST API provides a high-level interface to FX4Biz, there are also API methods for monitoring your wallet account balance & transfer informations of received or sent payments on those accounts. 

### Placing Trades ###

FX4BIZ provides a deliverable FX facility and deliverable FX liquidity via the FX4Biz-rest API. You will become counterparty to FX4BIZ and can market and sell deliverable FX services to corporate and private clients as well as using such services on their behalf.

The FX4Biz-rest API supports online trading for the following contracts: TOD (Same-day settled for those currencies than can be), TOM (next-day settled), SPOT (T+2) and forward contracts up to one year. 

| Same day value Currencies | Cut Off Time |
|------|------|
| CHF | 8:30 |
| GBP | 10:30 |
| EUR | 16:00 |
| USD | 16:30 |

| Next day value Currencies (Times stated are for the day prior to the Value Date) | Cut Off Time |
|-------|------|
| AOA, ARS, BIF, BRL, CDF, CLP, COP, CRC, DJF, DOP, GHS, HNL, KES, MAD, NPR, PEN, PHP, RUB, TND, TRY, TZS, UGX, XOF/XAF | 10:00 |
| AED, AUD, CAD, CZK, DKK, HKD, HUF, JPY, NOK, NZD, PLN, SEK, SGD, ZAR | 10:30 |

# Authentication Services #

## <a id="get-login-user"></a> Login ##
-> TBD

## <a id="get-end-session"></a> End session ##
-> TBD

# <a id="account_services"></a> Account Services # 

There are two kinds of accounts with FX4BIZ. What we call `wallet` account, which is account hold in the FX4Biz' book and `external bank` account, which is an account hold in another bank.

As an example, a response for `GET /Account/{account_id}/details` object looks like this:
```js
{
    "Account": {
        "id": "xxx"
        "status": "active",
        "type": "wallet",
        "created_date": "2014-01-12T00:00:00+00:00",
        "created_by": "api",
        "tag": "My wallet account EUR",
        "number": "xxx4548",
        "currency": "EUR",
        "correspondant_bank":{
            "bic": "AGRIFRPP",
            "name": "CREDIT AGRICOLE SA",
            "address": {
                "street": "BUILDING PASTEUR, BLOC 1: 91-93, BOULEVARD PASTEUR",
                "post_code": "75015",
                "city_name": "Paris",
                "state_or_province": "",
                "country": "FRANCE"
            }
        },
        "beneficiary_bank": {
            "bic": "FXBBBEBB",
            "name": "FX4BIZ SA",
            "address": {
                "street": "Avenue Louise, 350",
                "post_code": "1050",
                "city_name": "Bruxelles",
                "state_or_province": "Bruxelles-Capitale",
                "country": "FR"
            }
         },
        "beneficiary": {
            "name": "John Doe",
            "address": {
                "street": "1 My Road",
                "post_code": "ZIP",
                "city_name": "London",
                "state_or_province": "",
                "country"; "UK",
            }
        }
        
    }
}
```

## <a id="post-account-create"></a> Submit account ##

```
Method: POST 
URL: /accounts/
```
This service permits to reference a new account. This service include verifications on the format of the account created.
The API has been made in order to accept the local specification of cross-boarder payments.

The Api accepts the following formats of external bank accounts :
- Bic & Iban
- Local UK format
- Local US format
- Local CA format
- Other local formats (unspecified but different from the previous).

**Parameters.*

| Field | Type | Description |
|-------|------|-------------|
| Correspondent Bank | [Correspondent Bank Object](#correspondent_account_object) | **Required for local format.** The intermediary bank details. |
| Beneficiary Bank | [Beneficiary Bank Object](#beneficiary_bank_object) | **Required.** The recipient bank details. |
| number | String | **Required.** The recipient account number or Iban. `xxx4548` |
| currency | String | **Required.** Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying the account currency. `EUR` |
| tag | String | Custom Data. `External bank account EUR` |

## <a id="get-accounts-list"></a> Retrieve accounts list ##

```
Method: GET 
URL: /accounts/
```
Retrieve the list of accounts referenced.
If you only want to retrieve the list of your wallets accounts, you have to sort the list by `wallet` types.

**Parameters.*

| Field | Type | Description |
|-------|------|-------------|
| type | String | Sort the list by type of account. `wallet` |

## <a id="get-account-balances"></a> Get account balances ##

```
Method: GET 
URL: /account/{account_id}/balance
```
Retrieve the [balance](#balance_object) of a wallet account hold in the FX4Biz books.
It is not possible to retrieve the balance of an external account. If the id given in the url parameters do not match with one of your wallet account, the json response will be an error.
It is possible to retrieve the balance of a wallet account on a specific date. You have to mention the closing date to retrieve the balance of you wallet on a past date.
You can have the closing balance of your wallet account on a past date by specifying the date wished.

| Field | Type | Description |
|-------|------|-------------|
| date | Date | `YYYY-MM-DD` |

## <a id="get-account-details"></a> Retrieve account details ##

```
Method: GET 
URL: /account/{account_id}
```
Retrieve bank details on an account.

## <a id="put-account-details"></a> Update account details ##

```
Method: PUT 
URL: /account/{account_id}
```
Update information on an account or modify beneficiary bank or correspondent bank related to this one. 

## <a id="get-transfer-history"></a> Get transfer history ##

```
Method: GET 
URL: /transfers
```
Request the list of transfers that has been received or sent on a specific period of time.

**Parameters:**

| Field | Type | Description |
|-------|------|-------------|
| `from_date` | Date | `YYYY-MM-DD`  |
| `to_date` | Date | `YYYY-MM-DD`  |

## <a id="get-transfer-details"></a> Retrieve transfer details ##

```
Method: GET 
URL: /transfer/{transfer_id}
```
Request information on a particular [transfer](#transfer_object) that has been credited or debited to a wallet.

## <a id="delete-account"></a> Delete account ##

```
Method: DELETE 
URL: /account/{account_id}
```
Delete an account.

# <a id="payment_object"></a> Payment Objects #

Sending a payment involves two steps:

1. Generate the payment object with the [Create Payment method](#submit-payment). 
When you submit a payment to be scheduled, you assign a unique id to that payment. 
*Caution:* The payment created will be automatically rolled to the next closest working days if not confirmed in the scheduled date of operation.

2. Confirm the payment to the API for processing, using the [Confirm Payment method](#confirm-payment). 
When you confirm a payment for processing, make sure you have sufficient funds in your wallet account balance. The funds transfer will be automatically locked-in if the wallet account balance is not sufficient. Make sure you always have enough funds on your wallet.
*Caution:* Payment submission is an asynchronous process, so payments can fail even after they have been submitted and confirmed successfully. 

This `Payment` format is intended to be straightforward to create and parse, from strongly or loosely typed programming languages. Once a transaction is processed and validated it also includes information about the final details of the payment.

As an example, a response for `GET /payment/{:id}` object looks like this:
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
Method: POST 
URL: /payments
```
Use this path in order to schedule a new payment.

## <a id="confirm-payment"></a> Confirm a payment ##

```
PUT /payments/{:id}/confirm
```
Payments that has been scheduled must be confirmed in order to be release. If the payment is not confirmed on scheduled date of operation, it will be postponed to the next operation date available.

## <a id="get-payment-history"></a> Get payment history ##

```
PUT /payments
```


## <a id="account_object"></a> Account Object ##

When an account is specified as part of a JSON body, it is encoded as an object with the following fields:

**Object resources.**

| Field | Type | Description |
|-------|------|-------------|
| id |  String | The id of the account. `xxx` |
| creation_date |  DateTime | The creation date of the object: `xxx` |
| currency | String | Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying which currency. `USD` |
| reference |  String | Custom data. `reference` |
| beneficiary | [Beneficiary Object](#beneficiary_object) | The owner of the account. |
| number | String | Iban or account number. `xxx384` |

## <a id="address_object"></a> Address Object ##

When an address is specified as part of a JSON body, it is encoded as an object with four fields:

**Object resources.**

| Field | Type | Description |
|-------|------|-------------|
| street | String | `350 Avenue Louise` |
| post_code | String | `1050` |
| city | String | `Bruxelles` |
| state_or_province | String | `Bruxelles-Capitale` |
| country | String | `BE` |

## <a id="amounts_object"></a> Amounts Object ##

When an amount of currency is specified as part of a JSON body, it is encoded as an object with two fields:

**Object resources.**

| Field | Type | Description |
|-------|------|-------------|
| `value`  | String (Quoted decimal) | The quantity of the currency. |
| `currency` | String | Three-digit [ISO 4217 Currency Code](http://www.xe.com/iso4217.php) specifying which currency. Alternatively, a 160-bit hex value. |

## <a id="balance_object"></a> Balance Object ##

| Field | Type | Description |
|-------|------|-------------|


## <a id="beneficiary_bank_object"></a> Beneficiary Bank Object ##

| Field | Type | Description |
|-------|------|-------------|
| bic | String | **Required if swift format.** Eight or eleve-digit [ISO 9362 Business Identifier Code](http://en.wikipedia.org/wiki/ISO_9362) specifying the Recipient Bank: `CHASUS33XXX` |
| `clearing_type` | String | **Required if local format.** |
| `clearing_code` | String | **Required if local format.** |
| `name` | String | **Required if local format.** |
| `address` | [Address Object](#address_object) | **Required if local format.** |

Example Beneficiary Bank Object:

```js
{
  "bic": "CHASUS33",
  "clearing_type": "FW",
  "clearing_code": "021000021",
  "name": "JPMORGAN CHASE BANK, N.A.",
  "address": {
      "street": "4 NEW YORK PLAZA, FLOOR 15",
      "post_code": "10004",
      "city": "NEW YORK",
      "state_or_province": "NY",
      "Country": "US"
  }
}
```

## <a id="beneficiary_object"></a> Beneficiary Object ##

| Field | Type | Description |
|-------|------|-------------|
| `name` | String | **Required.** |
| `type` | String | **Required.** |
| `address` | [Address Object](#address_object) |  |

Example Beneficiary Object:

```js
{
  "name": "John Doe",
  "name": "Individual",
  "address": {
      "street": "350 Avenue Louise",
      "post_code": "1050",
      "city": "Bruxelles",
      "state_or_province": "Bruxelles-Capitale",
      "Country": "BE"
  }
}
```

## <a id="correspondent_bank_object"></a> Correspondent Bank Object ##

| Field | Type | Description |
|-------|------|-------------|
| bic | String | **Required if local format.** `CHASUS33` |
| `clearing_type` | String |  |
| `clearing_code` | String |  |
| `name` | String |  |
| `address` | [Address Object](#address_object) |  |

## <a id="payment_object"></a> Payment Object ##

| Field | Type | Description |
|-------|------|-------------|
| payment_id | String | **Required.** id of the beneficiary account. `xxx` |
| amount | [Amount Object](#amount_object) | **Required.** The nominal amount to be transfered. `10,000.00 GBP` |
| date | Date | `YYYY-MM-DD` |

## <a id="rate_object"></a> Rate Object ##

When a rate is specified as part of a JSON body, it is encoded as an object with four fields:

| Field | Type | Description |
|-------|------|-------------|
| mid_market | String (Quoted decimal) | The average rate of the market between the bid and the ask rate |
| core | String (Quoted decimal) | The interbank rate provided by the FX partner of FX4BIZ |
| applied | String (Quoted decimal) | The rate applied by FX4Biz for this transaction |
| currency_pair | String | The cross of currency used for the rates provided |

Example Rate Object:

```js
{
  "mid_market": "1.1005",
  "core": "1.1004",
  "applied": "1.1002",
  "currency_pair": "EURUSD",
}
```
# Formatting Conventions #

The `FX4BIZ-rest` API conforms to the following general behavior for [RESTful API](http://en.wikipedia.org/wiki/Representational_state_transfer):

* You make HTTP (or HTTPS) requests to the API endpoint, indicating the desired resources within the URL itself. (The public server, for the sake of security, only accepts HTTPS requests.)
* The HTTP method identifies what you are trying to do.  Generally, HTTP `GET` requests are used to retrieve information, while HTTP `POST` requests are used to make changes or submit information.
* If more complicated information needs to be sent, it will be included as JSON-formatted data within the body of the HTTP POST request.
  * This means that you must set `Content-Type: application/json` in the headers when sending POST requests with a body.
* Upon successful completion, the server returns an [HTTP status code](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) of 200 OK, and a `Content-Type` value of `application/json`.  The body of the response will be a JSON-formatted object containing the information returned by the endpoint.

As an additional convention, all responses from FX4Biz-REST contain a `"success"` field with a boolean value indicating whether or not the success

## <a id="errors_conventions"></a>Errors ##

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

## <a id="quoted_numbers"></a>Quoted Numbers ##

In any case where a large number should be specified, FX4Biz-REST uses a string instead of the native JSON number type. This avoids problems with JSON libraries which might automatically convert numbers into native types with differing range and precision.

You should parse these numbers into a numeric data type with adequate precision. If it is not clear how much precision you need, we recommend using an arbitrary-precision data type.
