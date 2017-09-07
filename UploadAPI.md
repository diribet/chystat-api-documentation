# AQDEF upload API

Using this REST API a client is able to upload an AQDEF file to the [chy.stat](https://www.chystat.com) application.

- [What is AQDEF](#what-is-aqdef)
- [Authentication](#authentication)
- [Request parameters](#request-parameters)
- [Response](#response)
- [Error codes](#error-codes)

## What is AQDEF

AQDEF stands for Advanced Quality Data Exchange Format.
It is a data format developed by Q-DAS® in cooperation with leading companies of the manufacturing industry.

We are providing an open-source Java library for working with this format. See [aqdef-tools](https://github.com/diribet/aqdef-tools) for more information.

## Authentication

For authentication we are using [JWT (JSON Web Tokens)](https://jwt.io).
Note that there is no login mechanism in chy.stat API where the client would obtain a token, so the client is responsible for generating the token itself.
The token must meet some specific criteria, otherwise the request will be rejected with an appropriate [response](#response).

We are expecting the token in form of Base64 encoded strings joined together as 

`Base64(Header).Base64(Payload).Base64(Signature)`

The token must be sent in `Authorization` request header. See [request parameters](#request-parameters) section below.

### JWT Header

Header is a standard JWT header where is defined token type and algorithm. 

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### JWT Payload

Payload must contain a standard claim called `iat` - Issuaed At. It's a [UNIX time](https://en.wikipedia.org/wiki/Unix_time) representing time 
where the token was created. It will be used for validating the token age. By default we reject tokens older than 10 minutes.

```json
{
  "iat": 1504783221
}
```

### JWT Signature

We accept only HS256 algorithm used for signing the token.

## Request parameters

## Response

## Error codes

Table of error codes and its meaning

Code  | Name                      | Description
----- | ------------------------- | --------------------------------------------------------
0     | General error             | General error where no specific code can't be used.
20    | Authentication error      | Authentification failed. Missing or invalid JWT.
30    | Missing request param     | Any of the required request parameters is missing.
40    | Invalid request param     | Any of the request parameters is invalid or can't be processed by server.
110   | Unknown datasource ID     | The target datasource does not exists.
120   | Upload is not running     | The target upload is not running right now.
210   | Invalid AQDEF file name   | File name/type is invalid.
220   | AQDEF file not parseable  | AQDEF file is not parseable, it has a bad syntax.
230   | Missing DFD file          | Trying to upload a DFX file, but missing its DFD file.