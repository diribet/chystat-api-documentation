# chy.stat API documentation

We are offering a RESTful API to our [chy.stat](https://www.chystat.com) application. Please be aware that the API and its

- authentication/authorization
- versioning

are currently under development and as such it's very likely they will evolve or change.

## Authentication
All API endpoints are secured and requests to them has to be authenticated and authorized.

See the [authentication](Authentication.md) section for details.

## API sections

The API is broken into sections. A section is a part of the chy.stat application or a group of tasks that make up a logical unit.
Each section has is own URL path and documentation.
 
- [AQDEF upload](UploadAPI.md)

## Error codes

All API endpoints return standard HTTP status codes to indicate the result.
Additional information is sent in the response body in form of JSON. 
This JSON message contains an [error code](#error-codes-table) and a message with error description.

```json
{
  "code": 40,
  "message": "Unable to parse the 'path' request parameter"
}
```

### Error codes table

Error codes from 0 to 99 are general. 
Each API section can have it's own, more detailed error codes. 
These are documented in each API section.

Table of general API error codes and its meaning:

Code  | HTTP status code  | Name                      | Description
----- | ----------------- | ------------------------- | --------------------------------------------------------------
0     | 500               | General error             | General error where no specific code can't be used.
20    | 401               | Authentication error      | Authentication failed. Missing or invalid JWT.
21    | 403               | Authorization error       | Authorization failed. API Token doesn't have required permissions.
30    | 400               | Missing request param     | Any of the required request parameters is missing.
40    | 400               | Invalid request param     | Any of the request parameters is invalid or can't be processed.
