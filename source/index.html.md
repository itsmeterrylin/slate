---
title: SelfKey API Documents

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - javascript
  - react
  - vue

toc_footers:
  - <a href='https://www.selfkey.org/download'>Download - SelfKey Identity Wallet</a>
  - <a href='https://github.com/lord/slate'>Download - SelfKey Extension</a>
  - <br>
  - <a href='mailto:help@selfkey.org'>help@selfkey.org</a>

includes:
  - errors

search: true
---

# Introduction

<b>Welcome to the SelfKey API.</b>

You can use our API to access endpoints in the SelfKey Identity Wallet desktop application and the SelfKey Extension browser plugin. You can also view code examples in the dark area to the right, switching languages between the tabs. If anything is unclear, please contact us at [help@selfkey.org](mailto:help@selfkey.org).

# SelfKey Extension

## Overview
By installing the SelfKey Extension browser plugin, a user can deploy their blockchain address for authentication with your website. The SelfKey Identity Wallet (Mac OS/Win/Linux) acts as the backend layer that connects to the Ethereum blockchain.

## Part 1: User Requirements
To successfully use their blockchain address as an authentication method, there are two applications that must be installed and activated by the user. If both steps below are not completed, there will be instructions on how to fix this after clicking "Login" on your website.

* Step 1: [SelfKey Identity Wallet] (https://selfkey.org/download/) must be installed, opened, and logged in to an Ethereum address.
* Step 2: [Selfkey Extension] browser plugin must be installed and activated.

## Part 2: Frontend Integration

To enable the SelfKey Extension as an authentication method for your website, a JavaScript library needs to be installed. This displays a "Login With SelfKey" button on your website for users to click. Clicking this button starts the login workflow. Exact instructions can be found in [Selfkey Client Lib] and more information at [LWS Config].

## Part 3: Backend Integration

Are you using our compliance tool [KYC-Chain] (https://www.kyc-chain.com) or an in-house solution for verifying the identities of new users?

We have <b>two options</b> for the backend integration of the SelfKey Extension.

### Option 1: For Non KYC-Chain Clients

The following endpoint should be implemented on your backend: [Authentication & Proof Of Ownership Challenge-Response, LWS]

* Authentication – Challenge
* Authentication – Challenge Reply
* LWS - Create User file
* LWS - Get user token
* LWS - Login endpoint (optional if onAuthCallback is implemented)

Currently we don't to have tools to make this process easier, but there are several options:

* Selfkey Node.js lib
* Selfkey ASP.NET lib
* Selfkey Service

### Option 2: For KYC-Chain Clients

If your website is integrated with KYC-Chain, you can leverage our existing Authentication & Proof Of Ownership Challenge-Response endpoints:

1. KYC Chain should configure your instance to use RSA algorithm for token signatures.
2. You should receive a RSA public key and configure it for token verification.
3. You should override authentication endpoints in their config to point to KYC-Chain.


## Part 4: Flowchart

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Dolor sit amet `Lorem Ipsum` incididunt ut labore et dolore magna aliqua.


`Authorization: Lorem Ipsum`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# SelfKey Marketplace

## Crypto Exchanges

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Incorporations

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Bank Accounts

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete
