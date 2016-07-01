---
title: POST /auth_requests
taxonomy:
    category: docs
---

<a name="auth_requests-post"></a>
### Submit a device authentication request.
```
POST /auth_requests
```


#### Description
The device passes its vendor-defined identification data and tenant token
to obtain a valid JSON Web Token to be used in subsequent Mender API communications.
The id data is encrypted using the tenant's public key.

The Identity Service consults the id data with the Device Admission Service,
and either accepts the request, issuing a JWT (if the identity has been
accepted by the user), or rejects it as unauthorized.

This method simultaneously acts as a bootstraping one - upon the first
auth request, the device is enrolled in the system, and we start
tracking its state via generated Device ID.

The returned JWT is
  * signed with the identity service's private key
  * encrypted with the device's public key

JWTs are issued with the following standard fields:
  * 'exp' - expiry date
  * 'sub' - subject (Mender-generated device ID)
  * 'jit' - token's unique identifier

Each request is signed with the device's private key. The 'X-MEN-Signature' custom header carries the signature computed as:

   BASE64(SIGN(privkey, SHA256(body)))

An increasing 'seq_no' is used to prevent replay attacks. Upon the very first request
the client can provide any non-zero uint64, which will be recorded by the server for subsequent requests.


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Header**|**X-MEN-Signature**  <br>*optional*|Base64 version of body signed with device's private key.|string||
|**Body**|**auth_request**  <br>*required*|Auth request|[AuthRequestNew](#authrequestnew)||


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Authentication successful - a new JWT is issued and returned (encrypted with device's public key).|number(binary)|
|**400**|Missing/malformed request params or body.|[Error](#error)|
|**401**|The device cannot be granted authentication (e.g. not accepted by the user yet, or explicitly rejected).|No Content|
|**500**|Unexpected error|[Error](#error)|
