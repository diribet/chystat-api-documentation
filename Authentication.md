# Authentication

We use [HMAC authentication](https://en.wikipedia.org/wiki/HMAC) to authenticate inbound requests.

The authentication tokens are sent in form of [JWT (JSON Web Tokens)](https://jwt.io) 

The client is responsible for generating and sending the authentication token.
There are lot of [libraries](https://jwt.io/#libraries) that can be used to simplify the creation of JWTs.

The token has to be sent in the `Authorization` header using the `Bearer` schema 

`Authorization: Bearer <JWT token>`

### Getting the API token

First you have to get an API token from the chy.stat system. To get the API token details:
- login to a chy.stat application with admin rights
- go to section *System settings* :arrow_right: *General* :arrow_right: *API*
- open detail of existing or create a new API token
- copy the `Token ID` and `Token secret`

**Bevare! The `Token secret` is confidential.** You should be careful when handling this information.
If you know that the `Token secret` was compromised you should immediately delete the API token and issue a new one.

#### JWT Header

Header is a standard JWT header where is defined the token type and an algorithm used to create its signature. 

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

#### JWT Payload

Payload must contain these claims:
 
1. `iat` - Issued At. It's a [UNIX time](https://en.wikipedia.org/wiki/Unix_time) representing time when the token was created. 
It will be used for validating the token age. By default we reject tokens older than 10 minutes.

2. `sub` - Subject. It should contain the `Token ID` received from chy.stat (see [Getting the API token](#getting-the-api-token) for the details) 

```json
{
  "iat": 1504783221,
  "sub": "VXFE78JLQ9OHrvAKKg5vSw"
}
```

#### JWT Signature

The token has to be signed by the `Token secret` (see [Getting the API token](#getting-the-api-token) for the details).
Only the HS256 algorithm is supported for signing the token.
