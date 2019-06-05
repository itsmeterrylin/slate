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
Note: If both are not installed, there will be instructions after clicking "Login with SelfKey" on your website.
</aside>


## Part 2: Frontend Integration

To enable the SelfKey Extension as an authentication method for your website, a JavaScript library needs to be installed. This displays a "Login With SelfKey" button on your website for users to click. Clicking this button starts the login workflow. Exact instructions can be found in [Selfkey Client Lib] and more information at [LWS Config].


### Installation Instructions:

1. Integrate Selfkey Client Lib into the webpage
2. Select where you want the "Login With SelfKey" button to appear
3. Initialize the "Login With SelfKey" button by passing LWS Config specific to Relying Party to Selfkey Client Lib


## Part 3: Backend Integration



<aside class="notice">
Question: Are you using <a href ="https://www.kyc-chain.com">KYC-Chain</a> or an in-house compliance tool for verifying identities of new users?
</aside>

We have two options for the SelfKey Extension backend integration.

### Option 1: For Non KYC-Chain Clients

The following endpoint should be implemented on your backend: [Authentication & Proof Of Ownership Challenge-Response, LWS]

* Authentication – Challenge
* Authentication – Challenge Reply
* LWS - Create User file
* LWS - Get user token
* LWS - Login endpoint (optional if onAuthCallback is implemented)

Currently we don't to have tools to make this process easier, but there are several options:

* [Selfkey Node.js lib] - WORK IN PROGRESS
* [Selfkey ASP.NET lib] - WORK IN PROGRESS
* [Selfkey Service] - WORK IN PROGRESS

### Option 2: For KYC-Chain Clients

If your website is integrated with KYC-Chain, you can leverage our existing [Authentication & Proof Of Ownership Challenge-Response] endpoints:

1. KYC Chain should configure your instance to use RSA algorithm for token signatures.
2. You should receive a RSA public key and configure it for token verification.
3. You should override authentication endpoints in their config to point to KYC-Chain.


## Part 4: Flowchart

https://drive.google.com/file/d/1sxYn5cEpP1dJhe-A5peAn3bY5pmpGems/view?usp=sharing

<aside class="notice">
[NEED TO UPDATE DIAGRAM]
</aside>

# SelfKey Marketplace

## 1: Crypto Exchanges

Information on cryptocurrency exchanges are loaded into the SelfKey Identity Wallet from an Airtable file. There are two files: one for development (named ExchangesDev) and production (named Exchanges). To access the development Airtable, run the [SelfKey Identity Wallet] (https://www.selfkey.org/download) from terminal with: NODE_ENV=development


### How To Integrate A New Exchange (Non KYC-Chain Clients):

1. Add details to the Airtable.
2. Build config object according to this spec (Requirements should be negotiated with the relying party)
3. Paste it as JSON inside 'relying_party_config' field.

> Configuration:

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

> Configuration:

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
}

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
}
```

* Currently we have one hardcoded relying party – Flag Theory .
* To further integrate other relying parties further development is needed
* Each jurisdiction is a template in Flag Theory KYC-Chain instance.

<aside class="warning">FURTHER DEVELOPMENT NEEDED</aside>



## 3: Bank Accounts

<aside class="warning">FURTHER DEVELOPMENT NEEDED</aside>







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

How To Implement JWT Tokens:

* [JSON Web Token: Wikipedia] (https://en.wikipedia.org/wiki/JSON_Web_Token)
* [JWT.io] (https://jwt.io/)
* [JWT Handbook] (https://auth0.com/resources/ebooks/jwt-handbook)
* [Auth0 JWT Docs] (https://auth0.com/docs/jwt)

### Allowed Algorithms
* HMAC - Default
* RSA - Suitable for multi server authentication (STILL IN DEVELOPMENT)



## Part 3: Challenge Token

Besides several requirements below, you are free to include any data in your token for verification.

Required Claims:

* sub: (Subject) The wallet's public key
* iat: (Issued At) the epoch timestamp that the JWT was issued at
* exp: (Expiration)the epoch timestamp at which the JWT expires
* nonce: (Number used Once) a random string of bytes (base64-encoded) which must be signed in order to authenticate
* typ: "IDW_CHALLANGE"
* Recommended token expiration time: 30 min

## Part 4: Wallet Token

Besides several requirements below, you are free to include any data in your token for verification.

Required Claims:

* sub: (Subject) The wallet's public key
* iat: (Issued At) the epoch timestamp that the JWT was issued at
* exp: (Expiration)the epoch timestamp at which the JWT expires
* typ: "IDW_ACCESS"
* Recommended token expiration time: 60 min

## Part 5: How To Verify Wallet Token On Other Servers

JWT token can be verified on other servers, not just one. This allows our API to reside in several different services, not necessary all endpoints in one. For this to work, you must initialize your JWT verification logic with a shared secret key between the servers.

A better approach would be to use RS256 algorithm, and have your private key be accessible only to the server that hosts the challenge/response endpoint. Afterwards, any other server that needs to verify it can use the public key.



# Relying Party Configuration

5/31/2019: Default endpoints outdated with implementation, Confluence document needs updating.

# REST API Spec

## Overview

* Our REST API is broken down into three parts: 1) Authentication, 2) SelfKey Extension, 3) KYC for New Users.
* Authentication: Verifying and proving user has ownership of an Ethereum address via a challenge-response protocol.
* SelfKey Extension: Bridge between the SelfKey Wallet and your website for authentication or requesting files.
* KYC for New Users: Verify identities based off existing integrated templates with our compliance solution KYC-Chain.

<aside class="notice">
NOTE: All communications must be done via a secure SSL connection.
</aside>

## Headers Sent By SelfKey Identity wallet

### User-Agent
* For each API request, the SelfKey Identity Wallet will send the following user agent: SelfKeyIDW/[WALLET_VERSION] where WALLET_VERSION is a semver version of our application.
* Example:  User-Agent: SelfKeyIDW/1.0.0-beta

### Origin
* For each API request, the SelfKey Identity Wallet will send an 'Origin' header indicating who initialized the request.
* The default value is IDW indicating that the request was initialized by the SelfKey Identity Wallet.
* For our browser plugin SelfKey Extension, it will rely on the SelfKey Identity Wallet to determine which side initialized the request, and the Origin header will be set to this value.
* Example: "Origin: IDW" or "Origin: https://confluence.kyc-chain.com"




## 1: Authentication
* The goal of this function is to prove that the user has ownership over the presented public Ethereum address.
* This is done through a cryptographic challenge-response protocol as detailed below.


### Challenge

Overview: This request is dispatched by the SelfKey Identity Wallet to start establishing the authentication session with your website.

### Request


`GET /auth/challenge/${publicKey}`


URL Params:

* publicKey - standard Ethereum public key with (Note 0x prefix is required)

Example:

`GET /auth/challenge/0x3a1076bf45ab87712ad64ccb3b10217737f7faacbf2872e88fdd9a537d8fe266`

Headers:

* User-Agent,
* Origin,
* Accept: application/json

### Response

> JSON payload containing one key 'jwt'. It will contain the challenge token.

```Shell
`{
  "jwt":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIweDc0NWZhYmQyZWZkYmY5ZGViMzE2NDJiMjA1NGVlMmJjNzg2ZWJlYTA5NmQzNmMwYjFiMzA1ZWRhYWY5ODEzYzZiNDA1MWEzMzQyNjZjOWY0MGI3MTQzODFjMDNkYTVkYTMxNWVjMjA3MDAwZmE3YmI3NDg2OGE1ZTYxNDIxZTMxIiwiaWF0IjoxNTE2MjM5MDIyLCJub25jZSI6IjlWeDFRUmswbzk2UGNKcDhXbnhvNy9DMjdCQnJ0aFdHZTlPcUZ6OGRwa2VjbVNVQ0Q3RnJQNUhpZW5vWUg3OTN1cG1nYmtLNFY3c0JMKzR6YWJkQjJRPT0iLCJzY29wZSI6ImF1dGgvY2hhbGxlbmdlIn0.5V2BBI53C0NOzmaIcvNNg_04O6MmTQc4oFvRJ8vf5i0"
}`

```
Success (Status Code: 200)

Body:

JSON payload containing one key 'jwt'. It will contain the challenge token.

`{
  "jwt":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIweDc0NWZhYmQyZWZkYmY5ZGViMzE2NDJiMjA1NGVlMmJjNzg2ZWJlYTA5NmQzNmMwYjFiMzA1ZWRhYWY5ODEzYzZiNDA1MWEzMzQyNjZjOWY0MGI3MTQzODFjMDNkYTVkYTMxNWVjMjA3MDAwZmE3YmI3NDg2OGE1ZTYxNDIxZTMxIiwiaWF0IjoxNTE2MjM5MDIyLCJub25jZSI6IjlWeDFRUmswbzk2UGNKcDhXbnhvNy9DMjdCQnJ0aFdHZTlPcUZ6OGRwa2VjbVNVQ0Q3RnJQNUhpZW5vWUg3OTN1cG1nYmtLNFY3c0JMKzR6YWJkQjJRPT0iLCJzY29wZSI6ImF1dGgvY2hhbGxlbmdlIn0.5V2BBI53C0NOzmaIcvNNg_04O6MmTQc4oFvRJ8vf5i0"
  }`



Error: Invalid Public Key (Status Code: 422)

Body:

`{ "code": "invalid_key" "message": "Invalid public key"}`

### Challenge Reply

* Request is dispatched by the SelfKey Identity Wallet to verify ownership of an Ethereum private key.
* Payload is ECDA signature for the provided nonce. There are five types of responses:
* 1) Success, 2) Error - Bad Request, 3) Error - Not Authorized, 4) Error - Forbidden, and 5) Error - Bad Payload.
* Hardware Wallets: For users with Trezor or Ledger, they will have to manually confirm on devices for ECDA signatures.


Request:

`POST /auth/challenge/`

Headers:

* User-Agent,
* Origin,
* Accept: application/json
* Authorization: challenge token received from challenge endpoint

Body:

* JSON payload containing one key 'signature'.
* It will contain an ECDA signature generated by signing the nonce with Ethereum private key.
* Represented as hex (rpc sig).

`{"signature": "0xf62e386b48761c61d5e0a54d90ba6bea1ee9648d9e26f1301e9be2306843190357c1d1c915244aa171bc34b0c2c6b66ad546ef651eeaa7add8335675e3e5454601"}`

### Responses


Success (Status Code: 200)

Body:

A JSON payload containing one key 'jwt. It will contain the wallet token.

`{  "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIweDc0NWZhYmQyZWZkYmY5ZGViMzE2NDJiMjA1NGVlMmJjNzg2ZWJlYTA5NmQzNmMwYjFiMzA1ZWRhYWY5ODEzYzZiNDA1MWEzMzQyNjZjOWY0MGI3MTQzODFjMDNkYTVkYTMxNWVjMjA3MDAwZmE3YmI3NDg2OGE1ZTYxNDIxZTMxIiwiaWF0IjoxNTE2MjM5MDIyLCJub25jZSI6IjlWeDFRUmswbzk2UGNKcDhXbnhvNy9DMjdCQnJ0aFdHZTlPcUZ6OGRwa2VjbVNVQ0Q3RnJQNUhpZW5vWUg3OTN1cG1nYmtLNFY3c0JMKzR6YWJkQjJRPT0iLCJzY29wZSI6ImF1dGgvY2hhbGxlbmdlIn0.5V2BBI53C0NOzmaIcvNNg_04O6MmTQc4oFvRJ8vf5i0"}
`

Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`

Error - Not Authorized (Status Code: 401)

`{ "code": "token_invalid", "message": "Invalid token: Missing nonce claim"}`

`{"code": "token_invalid","message": "Malformed authorization header"}`

Error - Forbidden (Status Code: 403)

`{"code": "forbidden","message": "Insufficient permissions"}`

Error - Bad Payload (Status Code: 422)

`{"code": "signature_invalid","message": "Invalid signature"}`

`{"code": "signature_missing","message": "Missing signature"}`


## 2: SelfKey Extension

### Create User File
`POST /users/files`

### Request

Headers

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token
* Content-Type: multipart/form-data

Body: Binary File Upload

### Response


Success (Status Code: 200)

Body: JSON object with single key "id" representing the created file id
`{
   "id": "file-id-1"
}`


Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`


Error - Not Authorized (Status Code: 401)

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`


Error - Forbidden (Status Code: 403)

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`


Error - Bad Payload (Status Code: 422)

`{
  "code": "file_invalid",
  "message": "Should be a single file"
}`



### Create User






Request:

`POST /users`


Headers:

* User-Agent,
* Origin,
* Accept: application/json
* Authorization: wallet token.

Body:

JSON object containing ID attributes from the SelfKey Identity Wallet. Each ID attribute contains at least the following keys:

* id (optional): provided in SelfKey Extension config, used for attributing internal application structure
* schemaId: The id of one of the supported id attributes. Can be found here: https://platform.selfkey.org/repository.json
* value: The value of the attribute.

Note, document attributes should have all files uploaded first via the files endpoint and their content replaced by id.


`
[
    { "id": "first_name", "schemaId": "http://platform.selfkey.org/schema/attribute/first-name.json", "value": "First" },
    { "id": "passport", "schemaId": "https://platform.selfkey.org/schema/attribute/passport.json", "value": {
         "image": { "mimeType": "image/jpg", "size": 3000000000, "content": "file-id-1"},
         "issued": "15-05-2019",
         "expires": "16-05-2019",
         "selfie": { "mimeType": "image/jpg", "size": "28525854564", "content": "file-id-2"},
     } },
     {
        "id": "nationality",
        "schemaId": "https://platform.selfkey.org/schema/attribute/nationality.json",
        "value": { "country": "AT", "denonym": "Austrian"}
     }
]
`


### Response

Success (Status Code: 201 – Created With No Body)

Error – Bad Request (Status Code: 422)

`{
  "code": "bad_request",
  "message": "Error validating attributes"
}`


### Get User Token

```shell
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIweDc0NWZhYmQyZWZkYmY5ZGViMzE2NDJiMjA1NGVlMmJjNzg2ZWJlYTA5NmQzNmMwYjFiMzA1ZWRhYWY5ODEzYzZiNDA1MWEzMzQyNjZjOWY0MGI3MTQzODFjMDNkYTVkYTMxNWVjMjA3MDAwZmE3YmI3NDg2OGE1ZTYxNDIxZTMxIiwic2NvcGUiOiJhdXRoL2xvZ2luIiwiaWF0IjoxNTQ3NjIyNDA1fQ.xajZn2UmKtCT1WeLleye8DoCCcYnbavVnZ5TZ0bK7CM"
}
```

```javascript
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIweDc0NWZhYmQyZWZkYmY5ZGViMzE2NDJiMjA1NGVlMmJjNzg2ZWJlYTA5NmQzNmMwYjFiMzA1ZWRhYWY5ODEzYzZiNDA1MWEzMzQyNjZjOWY0MGI3MTQzODFjMDNkYTVkYTMxNWVjMjA3MDAwZmE3YmI3NDg2OGE1ZTYxNDIxZTMxIiwic2NvcGUiOiJhdXRoL2xvZ2luIiwiaWF0IjoxNTQ3NjIyNDA1fQ.xajZn2UmKtCT1WeLleye8DoCCcYnbavVnZ5TZ0bK7CM"
}
```

Request:

`GET /users/token`

Headers:

* User-Agent,
* Origin,
* Accept: application/json
* Authorization: wallet token.

Success (Status Code: 200)

Body:

JSON object, structure is defined by relying party. This object is passed back to your website.If your website waits for the response to be parsed and acted upon automatically on client side, the payload should contain one of these two keys:

* "token" – Will be posted to [Login Endpoint]
* "redirectTo" – will cause a redirect to the provided URL



Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`


Error - Not Authorized (Status Code: 401)

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`


`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`



Error - Forbidden (Status Code: 403)

Body:

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`


### Login Endpoint

Request:

`POST /login
`
Headers:

* User-Agent – provided by browser
* Origin – provided by browser
* Accept: application/json


Body:

JSON object containing the payload received from /users/token endpoint

`{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIweDc0NWZhYmQyZWZkYmY5ZGViMzE2NDJiMjA1NGVlMmJjNzg2ZWJlYTA5NmQzNmMwYjFiMzA1ZWRhYWY5ODEzYzZiNDA1MWEzMzQyNjZjOWY0MGI3MTQzODFjMDNkYTVkYTMxNWVjMjA3MDAwZmE3YmI3NDg2OGE1ZTYxNDIxZTMxIiwic2NvcGUiOiJhdXRoL2xvZ2luIiwiaWF0IjoxNTQ3NjIyNDA1fQ.xajZn2UmKtCT1WeLleye8DoCCcYnbavVnZ5TZ0bK7CM"
}`


Response:

Success (Status Code: 200)

Body: JSON object containing 'redirectTo' key

Example:

`{
  "redirectTo": "/success.html"
}`

`{
  "redirectTo": "/login-failed.html"
}`



## 3: KYC (SelfKey Marketplace)

### List Templates  

* These templates are created to run identity verification checks based on submitted attributes or documents.
* Returns a list of templates for the relying party, and this endpoint does not require Authorization.

Request:

`GET /templates`

Headers

* User-Agent,
* Origin,
* Accept: application/json


Response:

Success (Status Code: 200)

Example:

`[
    {
        "name": "Token Sale Signup for Individuals",
        "description": "For individuals wishing to participate in the Example Co token sale.",
        "templateId": "507f1f77bcf86cd799439011",
    },
    {
        "name": "Token Sale Signup for Companies",
        "description": "For companies, trusts or other organizations wishing to participate in the Example Co token sale.",
        "templateId": "534bad4b8342419e5a94b1d2",

    }
]`


### Get Template Details

Returns a specific template details.

Request:

`GET /templates/${templateId}`

URL params:

* templateId: the id of the requested template

Headers

* User-Agent,
* Origin,
* Accept: application/json


Response:

Success (Status Code: 200)

Example:

`{
  "id": "5c6619ae1ea6440111989852",
  "description": "Standard Attributes and Documents",
  "atributes": [
    {
      "id": "5c4e6edda58d2900401ba61b",
      "required": true,
      "schemaId": "http://platform.selfkey.org/schema/attribute/first-name.json"
    },
    {
      "id": "5c4e6efaa58d2900401ba61d",
      "required": true,
      "schemaId": "http://platform.selfkey.org/schema/attribute/last-name.json "
    },
    {
      "id": "5c511c64c7315f003ecfe2dc",
      "required": true,
      "schemaId": "http://platform.selfkey.org/schema/attribute/email.json"
    },
    {
      "id": "5c4e6e6ba58d2900401ba615",
      "required": true,
      "schemaId": "http://platform.selfkey.org/schema/attribute/bank-statement.json"
    },
    {
      "id": "5c4e6e89a58d2900401ba617",
      "required": false,
      "schemaId": "http://platform.selfkey.org/schema/attribute/bank-statement.json"
    },
    {
      "id": "5c4e6ea6a58d2900401ba619",
      "required": true,
      "schemaId": "http://platform.selfkey.org/schema/attribute/passport.json"
    }
  ],
  "name": "Standard Attributes"
`





### Create KYC File

`POST /files`


Request:

Headers:

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token
* Content-Type: multipart/form-data

Body: binary file upload


Response:

Success (Status Code: 200)

Body: JSON object with single key "id" representing the created file id

Example:

`{
   "id": "file-id-1"
}`



Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`


Error - Not Authorized (Status Code: 401)

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`


Error - Forbidden (Status Code: 403)

Body:

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`


Error - Bad Payload (Status Code: 422)

Example:

`{
  "code": "file_invalid",
  "message": "Should be a single file"
}`


### GET User File

`GET /users/files/${fileId}`


Request:

URL params:

* fileId

Headers:

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token


Success (Status Code: 200)

`{ url: 'https://myurl.com/documents/23123' }`


Error - Not Found (Status Code: 404)

`{ code: 'not_found', message: 'File not found' }`


Error - Not Authorized (Status Code: 401)

Body

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`



Error - Forbidden (Status Code: 403)

Body:

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`


### Create KYC Application

`POST /applications`

Request:

Headers:

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token

Body: JSON object containing application object

Application object will contain:

* templateId – the id of a template which was used to fill application object
* attributes – a list of id attributes, each attribute contains
* id: internal KYC system id assigned to the attribute (from template)
* schemaId: the id of the schema filled (list is available in https://platform.selfkey.org/repository.json)
* data: the data filled according for the schema for this attribute

`{
  "templateId": "5c6533c440e7080070dda1f4",
  "attributes": [
    {
      "id": "5c657861e97908003ec70d19",
      "schemaId": "http://platform.selfkey.org/schema/attribute/first-name.json",
      "data": "John"
    },
    {
      "id": "5c657880e97908003ec70d1b",
      "schemaId": "http://platform.selfkey.org/schema/attribute/last-name.json",
      "data": "Doe"
    },
    {
      "id": "5c657898e97908003ec70d1d",
      "schemaId": "http://platform.selfkey.org/schema/attribute/email.json",
      "data": "john.doe@domain.com"
    },
    {
      "id": "5c6578c4e97908003ec70d1f",
      "schemaId": "http://platform.selfkey.org/schema/attribute/passport.json",
      "data": {
        "image": {
          "mimeType": "image/png",
          "size": 50000,
          "content": "5c6619ae1ea6440111989852"
        },
        "issued": "2019-02-19",
        "expires": "2029-02-19"
      }
    }
  ]
}`


Response:

Success (Status Code: 200)

Body: JSON object with single key "id" representing the created file id

Example:

`{
  "id": "5c6623537406490150f93b5f",
  "applicationUrl": "http://localhost/v2/applications/5c6623537406490150f93b5f"
}`



Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`


Error - Not Authorized (Status Code: 401)

Body

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`


Error - Forbidden (Status Code: 403)

Body:

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`


Error - Bad Payload (Status Code: 422, Several Errors Possible)

Example:

`{
  "code": "file_invalid",
  "message": "Should be a single file"
}`


### List KYC Applications

`GET /applications`


Request

Headers:

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token



Response:

Success (Status Code: 200)

Body: JSON object containing a list of application objects

Example:

`[
  {
    "id": "5c6e013704fb92059af59147",
    "template": "5c6dfa25b0745105685253bd",
    "statusName": "In Progress",
    "currentStatus": 6
  },
  {
    "id": "5c6e17dd069cfc060b256802",
    "template": "5c6dfa25b0745105685253bd",
    "statusName": "In Progress",
    "currentStatus": 6
  }
]`



Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`


Error - Not Authorized (Status Code: 401)

Body

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`




Error - Forbidden (Status Code: 403)

Body

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`



### Update KYC Applications

`PATCH /applications/${applicationId}`

Takes attributes from payload and updates them inside application object on server

URL params:

* applicationId – The id of the application to update



Request:

Headers:

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token

Body: JSON object containing application object

Application object will contain:

* templateId – the id of a template which was used to fill application object
* attributes – a list of id attributes, each attribute contains
* id: internal kyc system id assigned to the attribute ( from template)
* schemaId: the id of the schema filled (list is available in https://platform.selfkey.org/repository.json)
* data: the data filled according for the schema for this attribute

`{
  "templateId": "5c6533c440e7080070dda1f4",
  "attributes": [
    {
      "id": "5c657861e97908003ec70d19",
      "schemaId": "http://platform.selfkey.org/schema/attribute/first-name.json",
      "data": "John"
    },
    {
      "id": "5c657880e97908003ec70d1b",
      "schemaId": "http://platform.selfkey.org/schema/attribute/last-name.json",
      "data": "Doe"
    },
    {
      "id": "5c657898e97908003ec70d1d",
      "schemaId": "http://platform.selfkey.org/schema/attribute/email.json",
      "data": "john.doe@domain.com"
    },
    {
      "id": "5c6578c4e97908003ec70d1f",
      "schemaId": "http://platform.selfkey.org/schema/attribute/passport.json",
      "data": {
        "image": {
          "mimeType": "image/png",
          "size": 50000,
          "content": "5c6619ae1ea6440111989852"
        },
        "issued": "2019-02-19",
        "expires": "2029-02-19"
      }
    }
  ]
}`



Response:

Success (Status Code: 204)

Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`



Error - Not Authorized (Status Code: 401)

Body:

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`


Error - Forbidden (Status Code: 403)

Body:

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`



Error - Bad Payload (Status Code: 422, Several Errors Possible)

Example:

`{
  "message": "Attribute referenced files not found"
}`



### Get Application Details

`GET /applications/${applicationId}`

Returns all available details about a specific application

URL params:

* applicationId – The id of the application.


Request

Headers:

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token


Response

Success (Status Code: 200)

Body: JSON object containing a list of application objects

Example:

`{
  "id": "5c6e013704fb92059af59147",
  "payments": [],
  "template": "5c6dfa25b0745105685253bd",
  "statusName": "In Progress",
  "currentStatus": 6
}`



Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`



Error - Not Authorized (Status Code: 401)

Body

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`


Error - Forbidden (Status Code: 403)

Body:

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`



### Create KYC Application Payment

`POST /applications/${applicationId}/payments`


Request

Headers:

* User-Agent
* Origin
* Accept: application/json
* Authorization: wallet token

Body: JSON object containing application payment

Application payment object will contain:

* transactionHash – the hash of an ethereum transaction

`{
  "transactionHash": "0x8b69a0ca303305a92d8d028704d65e4942b7ccc9a99917c8c9e940c9d57a9662"
}`


Response

Success (Status Code: 200)

Body: JSON object with created payment details

Example:

`{
  "createdAt": "2019-02-21T03:44:47.555Z",
  "id": "5c6e1eaf049ff100646cedd8",
  "transactionHash": "0x8b69a0ca303305a92d8d028704d65e4942b7ccc9a99917c8c9e940c9d57a9662"
}`


Error - Bad Request (Status Code: 400)

`{
  "code": "token_missing",
  "message": "Missing authorization header"
}`


Error - Not Authorized (Status Code: 401)

Body

`{
  "code": "token_invalid",
  "message": "Invalid token: Missing nonce claim"
}`

`{
  "code": "token_invalid",
  "message": "Malformed authorization header"
}`


Error - Forbidden (Status Code: 403)

Body:

`{
  "code": "forbidden",
  "message": "Insufficient permissions"
}`

Error - Bad Payload (Status Code: 422, Several Errors Possible)

Example:

`{
  "message": "Transaction hash is missing"
}`



# Library

## Node.JS

## ASP.NET

## SelfKey Service

# Simple Relying Party Integration Example
