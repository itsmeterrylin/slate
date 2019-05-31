---
title: SelfKey API Documents

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - javascript

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
To successfully use their blockchain address as an authentication method, there are two applications that must be installed and activated by the user.

* Step 1: [SelfKey Identity Wallet] (https://selfkey.org/download/) must be installed, opened, and logged in to an Ethereum address.
* Step 2: [Selfkey Extension] browser plugin must be installed and activated.

<aside class="success">
Note: If both applications are not installed, there will be instructions after clicking "Login with SelfKey".
</aside>


## Part 2: Frontend Integration

To enable the SelfKey Extension as an authentication method for your website, a JavaScript library needs to be installed. This displays a "Login With SelfKey" button on your website for users to click. Clicking this button starts the login workflow. Exact instructions can be found in [Selfkey Client Lib] and more information at [LWS Config].

## Part 3: Backend Integration



<aside class="notice">
Question: Are you using <a href ="https://www.kyc-chain.com">KYC-Chain</a> or an in-house compliance tool for verifying identities of new users?
</aside>



### Option 1: For Non KYC-Chain Clients

The following endpoint should be implemented on your backend: [Authentication & Proof Of Ownership Challenge-Response, LWS]

* Authentication – Challenge
* Authentication – Challenge Reply
* LWS - Create User file
* LWS - Get user token
* LWS - Login endpoint (optional if onAuthCallback is implemented)

Currently we don't to have tools to make this process easier, but there are several options:

* [Selfkey Node.js lib]
* [Selfkey ASP.NET lib]
* [Selfkey Service]

### Option 2: For KYC-Chain Clients

If your website is integrated with KYC-Chain, you can leverage our existing [Authentication & Proof Of Ownership Challenge-Response] endpoints:

1. KYC Chain should configure your instance to use RSA algorithm for token signatures.
2. You should receive a RSA public key and configure it for token verification.
3. You should override authentication endpoints in their config to point to KYC-Chain.


## Part 4: Flowchart

https://drive.google.com/file/d/1sxYn5cEpP1dJhe-A5peAn3bY5pmpGems/view?usp=sharing

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# SelfKey Marketplace

## 1: Crypto Exchanges

Information on cryptocurrency exchanges are loaded into the SelfKey Identity Wallet from an Airtable file. There are two files:: one for development (named ExchangesDev) and production (named Exchanges). To access the development Airtable, run the [SelfKey Identity Wallet] (https://www.selfkey.org/download) with: NODE_ENV=development


### How To Integrate A New Exchange (Non KYC-Chain Clients):

Add details to the Airtable.
Build config object according to this spec (Requirements should be negotiated with the relying party)
Paste it as JSON inside 'relying_party_config' field.

```shell
{
        "rootEndpoint": INSTANCE_URL,
        "endpoints": {
            "/templates/:id": "INSTANCE_URL/templates/:id?format=minimum"
            "/user/token": "INSTANCE_URL/auth/token"
        }
    }
```

```javascript

{
        "rootEndpoint": INSTANCE_URL,
        "endpoints": {
            "/templates/:id": "INSTANCE_URL/templates/:id?format=minimum"
            "/user/token": "INSTANCE_URL/auth/token"
        }
    }
```

### How To Integrate A New Exchange (KYC-Chain Clients)

Integration with crypto exchanges that use KYCC is far more seamless.
They will not need to implement anything marketplace related on their site.
The configuration for them will usually look like this:


### For KYC-Chain

* Setup the relying party instance.
* Specify the required JWT algorithm (HMAC/RSA).
* Setup one or more templates for it.
* Provide KYC-Chain instance URL to the SelfKey Team.

### For Independent Integrators (Non KYC-Chain Clients):

Currently there are no tools are available for independent implementation.
The only option is to implement the API calls described in [KYC (Marketplace)] and [Authentication].
A Node.JS tutorial for implementing the endpoints can be found here: Simple Relying Party Implementation Example
To fix this situation, we are working on the following:

* [Selfkey Node.js lib]
* [Selfkey ASP.NET lib]
* [Selfkey Service]




## 2: Incorporations


```shell
const incorporationsRPDetails = {
    name: 'Incorporations',
    status: 'Active',
    description: 'Incorporations',
    relying_party_config: {
        rootEndpoint: config.incorporationsInstance,
        endpoints: {
            '/templates/:id': `${config.incorporationsInstance}templates/:id?format=minimum`,
            '/user/token': `${config.incorporationsInstance}auth/token`
        }
    }
};

```

```javascript
const incorporationsRPDetails = {
    name: 'Incorporations',
    status: 'Active',
    description: 'Incorporations',
    relying_party_config: {
        rootEndpoint: config.incorporationsInstance,
        endpoints: {
            '/templates/:id': `${config.incorporationsInstance}templates/:id?format=minimum`,
            '/user/token': `${config.incorporationsInstance}auth/token`
        }
    }
};
```

* Currently we have one hardcoded relying party – Flag Theory .
* To further integrate other relying parties further development is needed
* Each jurisdiction is a template in Flag Theory KYC-Chain instance.

<aside class="warning">FURTHER DEVELOPMENT NEEDED</aside>



## 3: Bank Accounts

<aside class="warning">FURTHER DEVELOPMENT NEEDED</aside>

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

# Authentication & Proof of Ownership Challenge

## Overview

The purpose of this function is to prove the user has ownership over this public key presented for authentication. To do this, we issue a "Challenge-Response" protocol to cryptographically prove ownership via private key. For Details about API endpoints, please consult: REST API Spec.

<aside class="notice">Note: All communications MUST be done via secure SSL connection.</aside>

## Part 1: Challenge-Response Protocol

1. The SelfKey Identity Wallet requests a unique nonce from the your website and sends the public key.
2. Your website responds with a Challenge JWT Token containing the public key in subject and nonce claim.
3. The wallet receives the Challenge Token, decodes it and extracts the nonce.
4. The wallet creates a signature of the nonce signed with it's private key.
5. The wallet replies to your website with the challenge JWT token and signature.
6. Your website verifies that signature corresponds to the public key and nonce inside the JWT challenge token.
7. Your website replies to the wallet by issuing a Wallet JWT token.
8. Wallet uses the "Wallet JWT Token" to perform other requests to your website.


## Part 2: JWT-JSON Web token
What is JWT and how to implement it?
Here are several links to answer that:
https://en.wikipedia.org/wiki/JSON_Web_Token
https://jwt.io/
jwt-handbook-v0_14_1.pdf
https://auth0.com/docs/jwt
Allowed Algorithms
HMAC - Default
RSA - Suitable for multi server authentication (Not developed yet by KYCC)



## Part 3: Challenge Token
Besides several requirements below, you are free to include any data in your token and it will available for you when you'll need to verify it.

Required Claims:

sub: (Subject) The wallet's public key
iat: (Issued At) the epoch timestamp that the JWT was issued at
exp: (Expiration)the epoch timestamp at which the JWT expires
nonce: (Number used Once) a random string of bytes (base64-encoded) which must be signed in order to authenticate
typ: "IDW_CHALLANGE"
Recommended token expiration time: 30 min

## Part 4: Wallet Token

Besides several requirements below, you are free to include any data in your token and it will available for you when you'll need to verify it.
Required Claims:
sub: (Subject) The wallet's public key
iat: (Issued At) the epoch timestamp that the JWT was issued at
exp: (Expiration)the epoch timestamp at which the JWT expires
typ: "IDW_ACCESS"
Recommended token expiration time: 60 min

## Part 5: How To Verify Wallet Token On Other Servers

JWT token can be verified on other servers, not just one.
This allows our API to reside in several different services, not necessary all endpoints in one.
For this to work, you must initialize your JWT verification logic with a shared secret key between the servers.
A better approach would be to use RS256 algorithm, and have your private key be accessible only to the server that hosts the challenge/response endpoint.
Afterwards, any other server that needs to verify it can use the public key.



# Relying Party Configuration

5/31/2019: Default endpoints outdated with implementation, Confluence document needs updating.

# REST API Spec

## Overview


## Headers Sent By SelfKey Identity wallet


## 1: Authentication

### Challenge
### Challenge Reply

## 2: SelfKey Extension
### Create User File
### Create User
### Get User Token
### Login Endpoint

## 3: KYC (SelfKey Marketplace)
### List Templates  
### Create KYC File
### GET User File
### Create KYC Application 
### List KYC Applications
### Update KYC Applications
### Get Application Details
### Create KYC Application Payment



# Library

## Node.JS

## ASP.NET

## SelfKey Service

# Simple Relying Party Integration Example
