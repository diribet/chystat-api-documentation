# AQDEF upload API

Using this REST API a client is able to post an AQDEF file to a target upload within a [chy.stat](https://www.chystat.com) application.
Note that the AQDEF upload API is designed to be asynchronous. It means when you post an AQDEF file,
there is no guaranteed time when the file will be uploaded by the chy.stat uploading system.

- [What is AQDEF](#what-is-aqdef)
- [Authentication](#authentication)
- [Request](#request)
- [Response](#response)
- [Error codes](#error-codes)

## What is AQDEF

AQDEF stands for Advanced Quality Data Exchange Format.
It is a data format developed by Q-DAS® in cooperation with leading companies of the manufacturing industry.

We are providing an open-source Java library for working with this format. See [aqdef-tools](https://github.com/diribet/aqdef-tools) for more information.

## Request

There is only one available URL for the AQDEF upload API - **api/v1/upload**

We are expecting here a multipart POST requests with appropriate [request parameters](#request-parameters) when uploading AQDEF files.

### Request parameters

Header

Parameter      | Required        | Content example
-------------- | --------------- | ---------------------------------------------------------------------------
Authorization  | required        | Bearer eyJ0eXAiOiJKV1QiLCJhbGci8IJW.e30.0LHZndaAoGVj7BDgj8wHRqR7ApyJpXcOe4c

Body (multipart/form)

Parameter      | Required        | Content example
-------------- | --------------- | ------------------------------------
datasourceId   | required        | 31944cd8-fab8-4b0d-a866-39e82d7ce40d
aqdefFile      | required        | 
path           | required        | AP125/B02/test.DFQ
charset        | optional        | cp1250

#### Parameter "Authorization"

See [authentication section](Authentication.md)

#### Parameter "datasourceId"

The datasource to which is client uploading files to. 
To get that ID:
- login to a chy.stat application with admin rights
- go to section *System settings* :arrow_right: *General* :arrow_right: *Upload*
- select a target *Upload*
- go to its *Data Sources* tab and select or create an API datasource

#### Parameter "aqdefFile"

It's the uploaded file itself.

#### Parameter "path"

When uploading an AQDEF file, you can tell a relative path where the file will be read from at the server side.
The path must at least contain name of the file. It's the most common scenario.

#### Parameter "charset"

Character encoding used for AQDEF file encoding. Default is UTF-8.

#### An example POST request

```
POST /chystat/api/upload HTTP/1.1
Host: my.domain.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGci8IJW.e30.0LHZndaAoGVj7BDgj8wHRqR7ApyJpXcOe4c
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

Since the AQDEF upload API is designed to be asynchronous, we provide only two type of response.

- HTTP 200 OK - upload request has been accepted and the file will be uploaded as soon as possible
- HTTP 40x/50x - upload request has been rejected

In case of rejection, the response contains a JSON body with a specific API [error code](#error-codes) and a message.

## Error codes

These are Upload API specific error codes on top of the [general](README.md#error-codes-table) ones.

Code  | HTTP status code  | Name                      | Description
----- | ----------------- | ------------------------- | --------------------------------------------------------------
110   | 404               | Unknown datasource ID     | The target datasource does not exists.
120   | 503               | Upload is not running     | The target upload is not running right now.
210   | 400               | Invalid AQDEF file name   | File name/type is invalid.
220   | 400               | AQDEF file not parseable  | AQDEF file is not parseable, it has a bad syntax.
230   | 400               | Missing DFD file          | Trying to upload a DFX file, but missing its DFD file.