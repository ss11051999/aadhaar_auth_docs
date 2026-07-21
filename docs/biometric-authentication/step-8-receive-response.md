---
sidebar_position: 8
---

# Step 8 - Receive and Process Authentication Response

## Goal

After the Biometric Authentication Request has been sent to the AUA, the AUA processes the request and returns an Authentication Response to the Sub-AUA.

The response contains two layers:

1. An outer AUA response containing `client_id`, `res_code`, `res_hash`, `res_data`, and `res_msg`.
2. A Base64-encoded Authentication Response XML inside the `res_data` field.

The Sub-AUA must process the outer response, decode `res_data`, and then process the actual `<AuthRes>` XML to determine the final biometric authentication result.

---

## Response Workflow

```text
Biometric Authentication Request
                │
                ▼
Send Request to AUA
                │
                ▼
AUA Processes Request
                │
                ▼
AUA Returns Response
                │
                ▼
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
Verify Response Signature
                │
                ▼
Check ret Attribute
                │
          ┌─────┴─────┐
          ▼           ▼
       ret="y"      ret="n"
          │           │
          ▼           ▼
      Success      Failure
```

---

## Step 1 - Receive HTTP Response

After sending the request, the Sub-AUA receives an HTTP response from the AUA.

Example:

```text
POST Response Code :: 200
```

An HTTP status code of `200` indicates that the HTTP request was successfully received and processed at the API level.

However, `HTTP 200` does **not** necessarily mean that biometric authentication was successful.

The actual authentication result must be determined by processing the decoded `<AuthRes>` XML.

---

## Step 2 - Parse the AUA Response

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
        BASE64_ENCODED_AUTHENTICATION_RESPONSE
    </res_data>

    <res_msg>
        Success from CSC AAG.
    </res_msg>

</xml>
```

The Sub-AUA must parse this outer XML response before processing the actual Authentication Response.

---

## AUA Response Fields

| Field | Description |
|---|---|
| `client_id` | Identifier of the Sub-AUA/client |
| `res_code` | Response code returned by the AUA |
| `res_hash` | Hash associated with the response data |
| `res_data` | Base64-encoded Authentication Response XML |
| `res_msg` | Response message returned by the AUA |

---

## Step 3 - Check `client_id`

The response contains the client identifier:

```xml
<client_id>AUA-CHP</client_id>
```

The Sub-AUA should verify that the returned `client_id` corresponds to the expected client identifier configured for the integration.

Example:

```text
client_id: AUA-CHP
```

---

## Step 4 - Check `res_code`

The AUA returns a response code.

Example:

```xml
<res_code>10</res_code>
```

The received response contains:

```text
res_code: 10
```

The AUA also returns a response message:

```text
res_msg: Success from CSC AAG.
```

The Sub-AUA should process `res_code` according to the response code definitions provided by the AUA integration documentation.

> The AUA response code and the final biometric authentication result are different concepts. The final authentication result must be determined from the decoded `<AuthRes>` XML.

---

## Step 5 - Read `res_data`

The `res_data` field contains the actual Authentication Response XML in Base64-encoded format.

Example:

```xml
<res_data>
    PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgi...
</res_data>
```

The Sub-AUA must decode the Base64 value before processing the Authentication Response.

---

## Step 6 - Decode `res_data`

After Base64 decoding the `res_data` field, the Sub-AUA obtains the actual Authentication Response XML.

Example decoded response:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>

<AuthRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    info="04{01000804gzFCx6cvwZpFnvXVaxOSxVbC1DeSNKD111uWhRoerq0JBoAXog/u8iIOKsCP516n,...}"
    ret="y"
    ts="2024-05-02T11:42:39.187+05:30"
    txn="2405021142340169818591290pmg">

    <Signature
        xmlns="http://www.w3.org/2000/09/xmldsig#">

        ...

    </Signature>

</AuthRes>
```

The root element of the decoded response is:

```xml
<AuthRes>
```

---

## Step 7 - Process the `AuthRes` Element

The `<AuthRes>` element contains the final authentication result and transaction information.

Important attributes include:

| Attribute | Example | Description |
|---|---|---|
| `ret` | `y` | Final authentication result |
| `code` | `51047f1e0ff5494bacd35a17b66d0de9` | Authentication response code |
| `info` | `04{...}` | Additional authentication information |
| `ts` | `2024-05-02T11:42:39.187+05:30` | Authentication response timestamp |
| `txn` | `2405021142340169818591290pmg` | Authentication transaction ID |

---

## Step 8 - Check Authentication Result

The most important attribute is:

```xml
ret="y"
```

The received response contains:

```text
Authentication Ret : y
```

A value of:

```text
y
```

indicates that the authentication was successful.

### Successful Authentication

```xml
<AuthRes
    ret="y"
    ...>
```

The biometric authentication was successful.

The application can continue with the requested business process.

### Failed Authentication

```xml
<AuthRes
    ret="n"
    ...>
```

The biometric authentication was unsuccessful.

The application should process the failure according to the available response information.

---

## Step 9 - Process Authentication Transaction ID

The Authentication Response contains the transaction ID.

Example:

```xml
txn="2405021142340169818591290pmg"
```

The application should compare this transaction ID with the transaction ID used in the original Authentication Request.

Example:

```text
Authentication txn :
2405021142340169818591290pmg
```

The transaction ID should be used to correlate the Authentication Request and Authentication Response.

---

## Step 10 - Process Authentication Timestamp

The Authentication Response contains a timestamp.

Example:

```xml
ts="2024-05-02T11:42:39.187+05:30"
```

The application can use this timestamp for transaction tracking and auditing.

Example:

```text
Authentication ts :
2024-05-02T11:42:39.187+05:30
```

---

## Step 11 - Process Authentication Code

The Authentication Response contains a `code` attribute.

Example:

```xml
code="51047f1e0ff5494bacd35a17b66d0de9"
```

Example log:

```text
Authentication Code :
51047f1e0ff5494bacd35a17b66d0de9
```

The application may store this value for transaction tracking or auditing purposes.

The exact interpretation of the value depends on the authentication response specification used by the integration.

---

## Step 12 - Process Authentication Information

The Authentication Response may contain an `info` attribute.

Example:

```xml
info="04{01000804gzFCx6cvwZpFnvXVaxOSxVbC1DeSNKD111uWhRoerq0JBoAXog/u8iIOKsCP516n,...}"
```

The `info` attribute contains additional information returned as part of the authentication response.

Example log:

```text
Authentication Info :
04{01000804gzFCx6cvwZpFnvXVaxOSxVbC1DeSNKD111uWhRoerq0JBoAXog/u8iIOKsCP516n,...}
```

Treat the information contained in this attribute as sensitive.

Do not expose raw authentication information to the end user.

---

## Step 13 - Verify the Authentication Response Signature

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

The Sub-AUA should verify the digital signature according to the applicable authentication specification and configured certificate.

Signature verification helps ensure that the Authentication Response has not been modified after it was generated.

---

## Complete Response Processing Flow

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
        ├────────────────┐
        ▼                ▼
    ret="y"          ret="n"
        │                │
        ▼                ▼
 Authentication      Authentication
   Successful           Failed
        │                │
        ▼                ▼
Continue Business    Handle Failure
     Process          / Retry if allowed
```

---

## Example Successful Response

The response received from the AUA:

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

After decoding `res_data`, the Authentication Response is:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>

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

The final processed result is:

```text
HTTP Status         : 200
Client ID           : AUA-CHP
AUA Response Code   : 10
AUA Response Message: Success from CSC AAG.

Authentication Ret  : y
Authentication Code : 51047f1e0ff5494bacd35a17b66d0de9
Authentication Txn  : 2405021142340169818591290pmg
Authentication Ts   : 2024-05-02T11:42:39.187+05:30
```

Therefore:

```text
Biometric Authentication Result: SUCCESS
```

---

## Important: HTTP Status vs Authentication Result

The following values represent different levels of the response.

### HTTP Response

```text
HTTP 200
```

Indicates that the HTTP request was successfully handled.

### AUA Response

```xml
<res_code>10</res_code>
```

Indicates the response status at the AUA integration layer.

### Final Authentication Result

```xml
<AuthRes ret="y">
```

Indicates that the biometric authentication was successful.

Therefore, the application should **not** consider biometric authentication successful based only on:

```text
HTTP 200
```

or:

```xml
<res_code>10</res_code>
```

The final authentication result should be determined after processing the decoded:

```xml
<AuthRes>
```

and checking:

```xml
ret="y"
```

---

## Validation Checklist

After receiving and processing the response, verify that:

* The HTTP response was received successfully.
* The HTTP status code is handled correctly.
* The response XML is well-formed.
* The `client_id` is valid.
* The `res_code` is processed according to the AUA integration specification.
* The `res_hash` is validated according to the AUA integration specification.
* The `res_data` field is present.
* The `res_data` value is successfully Base64 decoded.
* The decoded XML contains the `<AuthRes>` root element.
* The Authentication Response signature is verified where required.
* The `ret` attribute is present.
* The authentication transaction ID matches the original request.
* The response timestamp is valid.
* The final authentication result is determined from the `ret` attribute.

---

## Success Scenario

If:

```xml
<AuthRes ret="y">
```

then the biometric authentication was successful.

Your application can:

* Continue the requested business workflow.
* Grant access to the requested service, if applicable.
* Record the transaction ID.
* Store the required audit information.
* Display a success message to the user.

---

## Failure Scenario

If:

```xml
<AuthRes ret="n">
```

then the biometric authentication was unsuccessful.

Your application should:

* Process the returned authentication information.
* Determine the appropriate failure reason.
* Display a user-friendly message.
* Log the Transaction ID for troubleshooting.
* Allow the resident to retry if permitted by the authentication workflow.

---

## Best Practices

* Always check the HTTP response status.
* Always parse the outer AUA response.
* Process `res_code` according to the AUA integration specification.
* Validate `res_hash` according to the AUA integration specification.
* Base64 decode `res_data` before parsing the Authentication Response.
* Verify the Authentication Response digital signature where required.
* Always check the `ret` attribute in the decoded `<AuthRes>` XML.
* Match the response transaction ID with the original request transaction ID.
* Store only the minimum information required for auditing and transaction tracking.
* Never rely only on HTTP `200 OK` to determine authentication success.
* Never expose raw authentication information to the end user.
* Never log raw biometric data, PID XML, encryption keys, or other sensitive authentication information.

---

## Authentication Complete

The complete Biometric Authentication workflow is:

```text
Collect Biometric Data
        │
        ▼
Generate PID XML
        │
        ▼
Encrypt PID XML
        │
        ▼
Generate HMAC
        │
        ▼
Create Authentication Request XML
        │
        ▼
Digitally Sign Authentication Request
        │
        ▼
Create Final AUA Request XML
        │
        ▼
Send Request to AUA
        │
        ▼
Receive AUA Response
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
Check ret Attribute
        │
        ▼
Biometric Authentication Result
        │
   ┌────┴────┐
   ▼         ▼
ret="y"    ret="n"
   │         │
   ▼         ▼
Success    Failure
```

---

## Notes

> A successful HTTP response such as `200 OK` only indicates that the request was successfully handled at the HTTP level. It does not by itself indicate successful biometric authentication.

> The AUA response contains an outer XML wrapper. The actual Authentication Response is Base64 encoded inside the `res_data` element.

> The Sub-AUA must decode `res_data` and process the resulting `<AuthRes>` XML to determine the final authentication result.

> In the example response, `ret="y"` indicates that the biometric authentication was successful.

> The AUA response code `res_code` and the final authentication result `ret` should be treated as separate response values. Interpret `res_code` according to the AUA integration specification.

> Do not store raw biometric data, PID XML, decrypted biometric information, session keys, or other sensitive authentication data after the authentication process is complete.

> Retain only the minimum information required for audit, reconciliation, and regulatory compliance, in accordance with applicable UIDAI guidelines and your organization's data retention policy.