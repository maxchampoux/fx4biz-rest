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
