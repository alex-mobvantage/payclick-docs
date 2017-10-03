---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://transferless.herokuapp.com/signup'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the TransferLess API docs. You can use the API to create, cancel and view payments programmatically.

# Authentication

TransferLess uses API keys to allow access to the API. You can find your API key on your [dashboard](https://transferless.herokuapp.com/dashboard).

TransferLess expects the API key to be included in all API requests to the server in a parameter called `access_key`.

# Transactions

## The Transaction object

This is an object representing a transaction (payment) between a sender and recipient. Transactions don't necessarily represent a successful payment from the sender to the recipient, as transactions can be either be cancelled (by the sender), declined (by the recipient), or in an error state if the payment failed to go through.

Below are the attributes of the Transaction object:

Parameter | Description
--------- | -----------
id | The transaction ID
status | The transaction's status. Can be one of `pending`, `sent`, `cancelled`, `declined` or `error`
amount | The amount of currency being sent, in cents
created_at | The time the transaction was created, in UTC
description | A description of the payment's purpose. This description will be shown to the recipient upon redemption. 
recipient_email | The email of the recipient
completed_at | The time the transaction was completed. Any non-pending transactions will have this field set, indicating the time the transaction status changed.

## Create a transaction

```shell
curl "https://transferless.herokuapp.com/api/transactions/create"
  -X POST
  --data "access_key=<YOUR KEY>&recipient_email=<RECIPIENT'S EMAIL>&amount=<AMOUNT>"
```

> The above command returns JSON structured like this:

```json
{
  "id": 40,
  "status": "pending",
  "amount": 1000,
  "created_at": "2017-10-02T19:09:25.762Z",
  "description": null,
  "recipient_email": "recipient@email.com",
  "link": "https:\/\/transferless.herokuapp.com\/receive\/40\/71a93b70d26f41ede9c0aca0c202fd4d1c6a074a",
  "completed_at": null
}
```

This endpoint creates a new Transaction object

### HTTP Request

`POST https://transferless.herokuapp.com/api/create`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
access_key | true | Your developer key found on the dashboard
recipient_email | true | The recipient's email
amount | true | The amount to send, in cents
description | false | Optionally provide a description for the payment. Must be no more than 128 characters

### Response

The response will contain the newly created [Transaction](#the-transaction-object) object

## List all transactions

```shell
curl "https://transferless.herokuapp.com/api/transactions?access_key=<YOUR KEY>"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 40,
    "status": "pending",
    "amount": 1000,
    "created_at": "2017-10-02T19:09:25.762Z",
    "description": null,
    "recipient_email": "recipient@email.com",
    "link": "https:\/\/transferless.herokuapp.com\/receive\/40\/71a93b70d26f41ede9c0aca0c202fd4d1c6a074a",
    "completed_at": null
  },
  {
    "id": 39,
    "status": "pending",
    "amount": 1000,
    "created_at": "2017-10-02T19:04:30.533Z",
    "description": "Payment for September 2017",
    "recipient_email": "recipient2@email.com",
    "link": "https:\/\/transferless.herokuapp.com\/receive\/39\/39ef0fd1a8bf764f1f7eaf204c28034e24686bf3",
    "completed_at": null
  }
]
```

This endpoint retrieves all transactions created by the account.

### HTTP Request

`GET https://transferless.herokuapp.com/api/transactions`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
access_key | true | Your developer key found on the dashboard

### Response

The response will contain an array of [Transaction](#the-transaction-object) objects. 

## Get a Specific Transaction

```shell
curl "https://transferless.herokuapp.com/api/transactions/1?access_key=<YOUR KEY>"
```

> The above command returns JSON structured like this:

```json
{
  "id": 40,
  "status": "pending",
  "amount": 1000,
  "created_at": "2017-10-02T19:09:25.762Z",
  "description": null,
  "recipient_email": "recipient@email.com",
  "link": "https:\/\/transferless.herokuapp.com\/receive\/40\/71a93b70d26f41ede9c0aca0c202fd4d1c6a074a",
  "completed_at": null
}
```

This endpoint retrieves a specific Transaction.

### HTTP Request

`GET https://transferless.herokuapp.com/api/transactions/:id`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
access_key | true | Your developer key found on the dashboard
id | true | The transaction ID to retrieve

### Response

The response will be a single [Transaction](#the-transaction-object)

## Cancel a Transaction

```shell
curl "https://transferless.herokuapp.com/api/transactions/1/cancel"
  -X POST
  --data "access_key=<YOUR KEY>"
```

This endpoint cancels a pending transaction.

<aside class="warning">Note: You can only cancel pending Transactions. Trying to cancel a Transaction with any other state will result in an error</aside>

### HTTP Request

`POST https://transferless.herokuapp.com/api/transactions/:id/cancel`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
access_key | true | Your developer key found on the dashboard
id | true | The transaction ID to cancel

### Response

Returns HTTP status 200 if the transaction was cancelled.