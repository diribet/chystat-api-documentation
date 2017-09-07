# AQDEF upload API

Using this REST API a client is able to upload an AQDEF file to the [chy.stat](https://www.chystat.com) application.

- [What is AQDEF](#what-is-aqdef)
- [Authentication](#authentication)
- [Request](#request)
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

We are expecting the token in a form of Base64 encoded strings joined together as 

`Bearer Base64(Header).Base64(Payload).Base64(Signature)`

The keyword `Bearer` is part of the JWT specification, so we are following it.
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

## Request

There is only one available URL for the AQDEF upload API 

**/api/upload**

We are expecting here multipart POST requests with appropriate [request parameters](#request-parameters) when uploading AQDEF files.

### Request parameters

Header:

Parameter      | Description      | Required        | Content example
-------------- | ---------------- | --------------- | -------------------------------------------------------------------------------
Authorization  | Contains JWT     | **required**    | Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUz.e30.0LHZndaAoGVj7BDgj8wHRqR7ApyJpXcYW3RnW

Body (multipart/form):

Parameter      | Description                                  | Required                  | Content example
-------------- | -------------------------------------------- | ------------------------- | ------------------------------------
datasourceId   | Targed datasource ID                         | **required**              | 31944cd8-fab8-4b0d-a866-39e82d7ce40d
aqdefFile      | AQDEF text file                              | **required**              | 
path           | Path relative to a virtual target directory  | **required**              | AP125/B02/test.DFQ
charset        | AQDEF file encoding                          | optional (default UTF-8)  | cp1250

#### datasourceId

You have to tell which datasource you are uploading files to.

#### aqdefFile

It's the file you are uploading.

#### path

When uploading an AQDEF file, you can tell a relative path where the file will be read from at the server side.
The path **MUST** at least contain name of the file.

#### charset

Character encoding used for AQDEF file encoding

#### An example POST request

```
POST /chystat/api/upload HTTP/1.1
Host: my.domain.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUz.e30.0LHZndaAoGVj7BDgj8wHRqR7ApyJpXcYW3RnW
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="datasourceId"

31944cd8-fab8-4b0d-a866-39e82d7ce40d
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="aqdefFile"; filename="test.DFQ"
Content-Type: 

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="path"

AP125/B02/test.DFQ
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

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