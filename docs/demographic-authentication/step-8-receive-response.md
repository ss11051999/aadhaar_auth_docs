---
sidebar_position: 8
---

# Step 8 - Receive Authentication Response

## Goal

After the Authentication Request has been successfully sent to the AUA, the AUA processes the request and returns an Authentication Response.

The Sub-AUA receives the response from the AUA over an HTTPS connection.

The response contains:

* `client_id` - Identifier of the Sub-AUA/client.
* `res_code` - Response status code returned by the AUA.
* `res_hash` - Hash associated with the response data.
* `res_data` - Base64-encoded Authentication Response XML.
* `res_msg` - Response message from the AUA.

The Sub-AUA must first process the AUA response and then decode the `res_data` field to obtain the actual Authentication Response XML.

---

## Authentication Response Flow

```text
AUA Server
        │
        ▼
Process Authentication Request
        │
        ▼
UIDAI Authentication Infrastructure
        │
        ▼
Generate Authentication Response
        │
        ▼
AUA Receives Response
        │
        ▼
Create AUA Response
        │
        ├── client_id
        ├── res_code
        ├── res_hash
        ├── res_data
        └── res_msg
        │
        ▼
Return Response to Sub-AUA
        │
        ▼
Sub-AUA Receives HTTP Response
        │
        ▼
Check HTTP Status Code
        │
        ▼
Parse AUA Response XML
        │
        ▼
Read res_code
        │
        ▼
Decode Base64 res_data
        │
        ▼
Parse AuthRes XML
        │
        ▼
Check ret="y" or ret="n"
        │
        ▼
Process Final Authentication Result
```

---

## HTTP Response

The AUA returns an HTTP response to the Sub-AUA.

Example:

```text
POST Response Code :: 200
```

A successful HTTP status code such as `200` indicates that the HTTP request was successfully received and processed at the API level.

It does **not** necessarily indicate that Aadhaar authentication was successful.

The actual authentication result must be determined by processing the decoded `AuthRes` XML.

---

## AUA Response XML

The AUA returns a response wrapped inside an `<xml>` element.

Example:

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>

    <res_code>10</res_code>

    <res_hash>
        BASE64_ENCODED_RESPONSE_HASH
    </res_hash>

    <res_data>
        BASE64_ENCODED_AUTHENTICATION_RESPONSE_XML
    </res_data>

    <res_msg>
        Success from CSC AAG.
    </res_msg>
</xml>
```

---

## Response Fields

| Field | Description |
|---|---|
| `client_id` | Identifier of the Sub-AUA/client |
| `res_code` | Response code returned by the AUA |
| `res_hash` | Hash associated with the response data |
| `res_data` | Base64-encoded Authentication Response XML |
| `res_msg` | Response message returned by the AUA |

---

## Example Response

The actual response received in the integration log is:

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>

    <res_code>10</res_code>

    <res_hash>
        UbHZv3KToV6b8xjg/jHXdzXhajbNDKKKD2ZlHMOk9xbio/ZZAN8q3jYIY2ayg2uL1PgbkHWZF0QW1i4nCq58ysD6n6XblFFgybSP/dgSOwM=
    </res_hash>

    <res_data>
        BASE64_ENCODED_AUTHENTICATION_RESPONSE
    </res_data>

    <res_msg>
        Success from CSC AAG.
    </res_msg>
</xml>
```

The `res_data` value is Base64 encoded.

Therefore, the Sub-AUA must decode `res_data` before processing the actual Authentication Response.

---

## Step 1 - Check HTTP Response Code

First, check the HTTP response status.

Example:

```text
POST Response Code :: 200
```

A response code of `200` indicates that the HTTP request was successfully processed.

However, do not consider the authentication successful based only on this status.

The actual authentication result is available inside the decoded `res_data`.

---

## Step 2 - Check AUA Response Code

Next, read the `res_code` value.

Example:

```xml
<res_code>10</res_code>
```

In the received response:

```text
res_code: 10
```

The AUA has returned:

```text
res_msg: Success from CSC AAG.
```

Therefore, the Sub-AUA can continue processing the response data.

> The exact meaning of `res_code` is defined by the AUA integration API. Refer to the AUA-provided response code documentation for the complete list of possible values.

---

## Step 3 - Read Response Data

The `res_data` field contains the Authentication Response XML in Base64-encoded format.

Example:

```xml
<res_data>
    PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgi...
</res_data>
```

The Sub-AUA must Base64 decode this value.

---

## Step 4 - Decode `res_data`

After Base64 decoding the `res_data`, the actual Authentication Response XML is obtained.

The decoded response in the example is:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<AuthRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    info="04{01000804gzFCx6cvwZpFnvXVaxOSxVbC1DeSNKD111uWhRoerq0JBoAXog/u8iIOKsCP516n,A,e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855,0100003000000210,2.0,20240502114232,1,1,0,0,2.5,f188708000127237e492f87d541ebf8785021276000d09e54fd98b64076e2dcd,10c731a922deccde860f92f7cda04d8318cc0332c8bcb9e8ca30d33c3f523ac5,10c731a922deccde860f92f7cda04d8318cc0332c8bcb9e8ca30d33c3f523ac5,23,NA,NA,NA,NA,NA,NA,NA,NA,NA,registered,ACPL.WIN.001,1.0.4,STARTEK.ACPL,FM220U,L0,NA}"
    ret="y"
    ts="2024-05-02T11:42:39.187+05:30"
    txn="2405021142340169818591290pmg">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>

</AuthRes>
```

The root element of the decoded response is:

```xml
<AuthRes>
```

---

## Step 5 - Process the `AuthRes` Element

The decoded `<AuthRes>` element contains the final authentication result.

The important attributes are:

| Attribute | Example | Description |
|---|---|---|
| `ret` | `y` | Final authentication result |
| `code` | `51047f1e0ff5494bacd35a17b66d0de9` | Response code or authentication result code |
| `info` | `04{...}` | Additional authentication information |
| `ts` | `2024-05-02T11:42:39.187+05:30` | Authentication response timestamp |
| `txn` | `2405021142340169818591290pmg` | Authentication transaction ID |

---

## Authentication Result

The most important attribute is:

```xml
ret="y"
```

The returned value is:

```text
Authentication Ret : y
```

This indicates that the authentication request was successful.

The application should check the `ret` attribute to determine the final authentication result.

### Successful Authentication

```xml
<AuthRes ret="y" ...>
```

The authentication was successful.

The application can continue with the requested business process.

### Failed Authentication

```xml
<AuthRes ret="n" ...>
```

The authentication was not successful.

The application should process the failure according to the returned response information.

---

## Authentication Transaction ID

The decoded response contains the transaction ID:

```xml
txn="2405021142340169818591290pmg"
```

The application should compare this transaction ID with the transaction ID used in the original Authentication Request.

Example log:

```text
Authentication txn : 2405021142340169818591290pmg
```

The transaction ID should be used to correlate the request and response.

---

## Authentication Timestamp

The response contains the authentication timestamp:

```xml
ts="2024-05-02T11:42:39.187+05:30"
```

Example log:

```text
Authentication ts : 2024-05-02T11:42:39.187+05:30
```

The timestamp indicates when the authentication response was generated.

---

## Authentication Code

The response contains a `code` attribute:

```xml
code="51047f1e0ff5494bacd35a17b66d0de9"
```

Example log:

```text
Authentication Code :
51047f1e0ff5494bacd35a17b66d0de9
```

The application should store this value if required for transaction tracking or auditing.

The exact meaning of this value depends on the authentication response specification used by the integration.

---

## Authentication Information

The response also contains an `info` attribute:

```xml
info="04{01000804gzFCx6cvwZpFnvXVaxOSxVbC1DeSNKD111uWhRoerq0JBoAXog/u8iIOKsCP516n,...}"
```

The `info` attribute contains additional authentication information returned by the authentication infrastructure.

Example log:

```text
Authentication Info :
04{01000804gzFCx6cvwZpFnvXVaxOSxVbC1DeSNKD111uWhRoerq0JBoAXog/u8iIOKsCP516n,...}
```

The information inside this attribute may contain authentication and device-related information.

> Do not expose the complete `info` value to end users. Treat authentication information as sensitive and follow the security and privacy requirements of your organization.

---

## Step 6 - Verify the Digital Signature

The decoded `<AuthRes>` response contains a digital signature.

Example:

```xml
<Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
    <SignedInfo>
        ...
    </SignedInfo>

    <SignatureValue>
        ...
    </SignatureValue>
</Signature>
```

The response signature should be verified according to the applicable authentication specification and certificate configuration.

The signature verification helps ensure that the Authentication Response has not been modified after being generated.

---

## Complete Response Processing

The complete response processing flow is:

```text
Receive HTTP Response
        │
        ▼
Check HTTP Status Code
        │
        ▼
Parse AUA Response XML
        │
        ▼
Read client_id
        │
        ▼
Read res_code
        │
        ▼
Read res_hash
        │
        ▼
Read res_data
        │
        ▼
Base64 Decode res_data
        │
        ▼
Parse AuthRes XML
        │
        ▼
Verify AuthRes Signature
        │
        ▼
Read ret Attribute
        │
   ┌────┴────┐
   ▼         ▼
ret="y"    ret="n"
   │         │
   ▼         ▼
Success    Failure
   │         │
   ▼         ▼
Continue   Handle Error
Business   / Retry
Process    if applicable
```

---

## Example Successful Response Processing

The received AUA response:

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>
    <res_code>10</res_code>
    <res_hash>BASE64_ENCODED_RESPONSE_HASH</res_hash>
    <res_data>BASE64_ENCODED_AUTHRES_XML</res_data>
    <res_msg>Success from CSC AAG.</res_msg>
</xml>
```

After decoding `res_data`:

```xml
<AuthRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    ret="y"
    ts="2024-05-02T11:42:39.187+05:30"
    txn="2405021142340169818591290pmg">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>

</AuthRes>
```

The final result is:

```text
HTTP Status       : 200
AUA Response Code : 10
AUA Response Msg  : Success from CSC AAG.
Authentication Ret : y
Authentication Code: 51047f1e0ff5494bacd35a17b66d0de9
Authentication Txn : 2405021142340169818591290pmg
Authentication Ts  : 2024-05-02T11:42:39.187+05:30
```

Therefore:

```text
Authentication Result: SUCCESS
```

---

## Validation Checklist

After receiving the response, verify that:

* The HTTP response was received successfully.
* The HTTP status code is handled correctly.
* The response XML is well-formed.
* The `client_id` is valid.
* The `res_code` is processed according to the AUA API specification.
* The `res_hash` is validated according to the AUA integration specification.
* The `res_data` field is present.
* The `res_data` value is successfully Base64 decoded.
* The decoded XML contains the `<AuthRes>` root element.
* The Authentication Response signature is verified where required.
* The `ret` attribute is present.
* The response transaction ID matches the original authentication transaction.
* The response timestamp is valid.
* The final authentication result is determined from `ret`.

---

## Important Difference Between HTTP Status and Authentication Result

It is important to distinguish between the HTTP response status and the actual authentication result.

### HTTP Status

```text
HTTP 200
```

This means the API request was successfully handled at the HTTP level.

### AUA Response

```xml
<res_code>10</res_code>
```

This indicates the response status at the AUA integration layer.

### Authentication Result

```xml
<AuthRes ret="y">
```

This indicates that the actual authentication was successful.

Therefore, the application should not consider the authentication successful based only on:

```text
HTTP 200
```

or:

```xml
<res_code>10</res_code>
```

The final authentication result should be determined by processing:

```xml
<AuthRes ret="y">
```

---

## Best Practices

* Always check the HTTP status code.
* Always parse the AUA response XML.
* Validate `res_code` according to the AUA integration specification.
* Validate `res_hash` according to the AUA integration specification.
* Base64 decode the `res_data` field before parsing the Authentication Response.
* Verify the Authentication Response digital signature where required.
* Always check the `ret` attribute in the decoded `<AuthRes>` XML.
* Match the response transaction ID with the original request transaction ID.
* Store transaction details for auditing and troubleshooting.
* Do not expose raw authentication response data to end users.
* Never log Aadhaar numbers, PID XML, biometric data, encryption keys, OTP values, or other sensitive authentication information.
* Do not consider HTTP `200` alone as a successful authentication.
* Do not consider the AUA `res_code` alone as the final authentication result.

---

## Output of this Step

At the end of this step, the Sub-AUA should have the following information:

```text
AUA Response
    │
    ├── client_id
    ├── res_code
    ├── res_hash
    ├── res_msg
    │
    ▼
Decode res_data
    │
    ▼
Authentication Response
    │
    ├── ret
    ├── code
    ├── info
    ├── ts
    └── txn
```

For the example response:

```text
HTTP Status        : 200
client_id          : AUA-CHP
res_code           : 10
res_msg            : Success from CSC AAG.
Authentication Ret : y
Authentication Code: 51047f1e0ff5494bacd35a17b66d0de9
Authentication Txn : 2405021142340169818591290pmg
Authentication Ts  : 2024-05-02T11:42:39.187+05:30
```

Final result:

```text
Authentication Successful
```

The authentication process is now complete.

---

## Next Steps

### If Authentication is Successful

If:

```xml
ret="y"
```

then:

* Consider the authentication successful.
* Continue with the requested business workflow.
* Record the transaction ID.
* Store the required audit information.
* Grant access to the requested service if applicable.

### If Authentication Fails

If:

```xml
ret="n"
```

then:

* Consider the authentication unsuccessful.
* Read the available response information.
* Determine the appropriate reason for failure.
* Inform the user with a suitable message.
* Allow the user to retry if permitted.
* Refer to the **Error Codes** documentation for troubleshooting.

---

## Notes

> The response received by the Sub-AUA contains an outer response wrapper generated by the AUA. The actual Authentication Response XML is returned inside the Base64-encoded `res_data` field.

> The Sub-AUA should first process the outer `<xml>` response and then decode `res_data` to obtain the `<AuthRes>` XML.

> A successful HTTP response, such as **200 OK**, does not necessarily mean that Aadhaar authentication was successful.

> Similarly, an AUA response such as `res_code=10` should be interpreted according to the AUA integration specification. The final authentication result must be determined by processing the decoded `<AuthRes>` response.

> In the example response, the decoded Authentication Response contains `ret="y"`, which indicates a successful authentication result.

> The response processing flow is applicable to **OTP**, **Biometric**, **Demographic**, **Face**, **eKYC**, and **Multi-Factor Authentication**. The main difference is the authentication data and factors used in the original Authentication Request.

> Keep transaction logs for auditing purposes, but never log sensitive resident information such as Aadhaar Number, PID XML, biometric data, OTP values, encryption keys, or complete authentication response data unless explicitly required and permitted by your security and compliance policies.