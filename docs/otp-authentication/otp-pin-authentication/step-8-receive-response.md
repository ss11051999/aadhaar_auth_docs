---
sidebar_position: 8
---

# Step 8 - Receive and Process Authentication Response

## Goal

After the Authentication Request is successfully sent to the Aadhaar Authentication Server, the server processes the request and returns a response.

The response received from the server is wrapped inside an outer response XML. The actual Authentication Response XML is provided inside the `res_data` element in **Base64-encoded** format.

Your application must:

1. Receive the HTTP response.
2. Check the HTTP response status.
3. Parse the outer response XML.
4. Read the `client_id`.
5. Check the `res_code`.
6. Read the `res_data`.
7. Base64-decode the `res_data`.
8. Parse the decoded `AuthRes` XML.
9. Read the `ret` attribute to determine authentication success or failure.
10. Process the authentication result.

---

## Response Flow

```text
Send Authentication Request
        │
        ▼
Aadhaar Authentication Server
        │
        ▼
Receive HTTP Response
        │
        ▼
Check HTTP Status Code
        │
        ▼
Parse Outer Response XML
        │
        ▼
Read res_code
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
        ├───────────────┐
        ▼               ▼
    ret = "y"       ret = "n"
        │               │
        ▼               ▼
Authentication      Authentication
Successful          Failed
```

---

## Step 1 - Receive HTTP Response

After sending the Authentication Request, the server returns an HTTP response.

Example:

```text
POST Response Code :: 200
```

An HTTP status code of `200` indicates that the HTTP request was successfully received and processed at the transport level.

> **Important:** HTTP `200 OK` does not necessarily mean that Aadhaar authentication was successful. You must parse the response and check the authentication result inside the decoded `AuthRes` XML.

---

## Step 2 - Outer Response XML

The response received from the server is wrapped inside an outer XML structure.

Example:

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>
    <res_code>10</res_code>
    <res_hash>BASE64_ENCODED_RESPONSE_HASH</res_hash>
    <res_data>BASE64_ENCODED_AUTHENTICATION_RESPONSE</res_data>
    <res_msg>Success from CSC AAG.</res_msg>
</xml>
```

The outer response contains the following elements:

| Element | Description |
| ------- | ----------- |
| `client_id` | Identifier of the AUA/client that initiated the request. |
| `res_code` | Response code returned by the intermediary or authentication service. |
| `res_hash` | Hash value associated with the response data. |
| `res_data` | Base64-encoded Authentication Response XML. |
| `res_msg` | Message describing the response status. |

---

## Step 3 - Check `client_id`

The `client_id` identifies the client or AUA associated with the authentication request.

Example:

```xml
<client_id>AUA-CHP</client_id>
```

Your application should verify that the returned `client_id` is the expected client identifier configured for your integration.

---

## Step 4 - Check `res_code`

The `res_code` indicates the processing status of the outer response.

Example:

```xml
<res_code>10</res_code>
```

The response may also contain a message:

```xml
<res_msg>Success from CSC AAG.</res_msg>
```

In the provided response:

```text
res_code: 10
res_msg: Success from CSC AAG.
```

This indicates that the request was successfully processed by the intermediary service.

> The exact meaning of `res_code` values depends on the integration and service provider. Refer to your AUA/ASA or intermediary integration documentation for the complete list of response codes.

---

## Step 5 - Read `res_data`

The `res_data` element contains the actual Authentication Response XML in Base64-encoded format.

Example:

```xml
<res_data>
    BASE64_ENCODED_AUTHENTICATION_RESPONSE
</res_data>
```

The value should be extracted and Base64-decoded.

Conceptually:

```text
res_data
    │
    ▼
Base64 Decode
    │
    ▼
Authentication Response XML
```

---

## Step 6 - Decode `res_data`

After Base64 decoding the `res_data` value, the resulting XML is the actual Authentication Response.

Example:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>

<AuthRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    info="04{...}"
    ret="y"
    ts="2024-05-02T11:42:39.187+05:30"
    txn="2405021142340169818591290pmg">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>

</AuthRes>
```

The root element of the decoded response is:

```text
AuthRes
```

---

## Step 7 - Process `AuthRes`

After decoding `res_data`, parse the resulting XML and read the `AuthRes` attributes.

The important attributes in the response are:

| Attribute | Description |
| --------- | ----------- |
| `ret` | Indicates the final authentication result. `y` indicates success and `n` indicates failure. |
| `code` | Response or authentication code returned by the authentication service. |
| `info` | Contains additional authentication information returned by the authentication service. |
| `ts` | Timestamp associated with the authentication response. |
| `txn` | Transaction ID associated with the authentication request. |

---

## Example Decoded Authentication Response

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>

<AuthRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    info="04{...}"
    ret="y"
    ts="2024-05-02T11:42:39.187+05:30"
    txn="2405021142340169818591290pmg">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>

</AuthRes>
```

---

## Step 8 - Check Authentication Result

The most important attribute for determining the final authentication result is:

```xml
ret="y"
```

If:

```text
ret = "y"
```

the authentication was successful.

If:

```text
ret = "n"
```

the authentication was unsuccessful.

### Successful Authentication

```text
ret = "y"
```

Your application can then:

* Mark the authentication as successful.
* Continue with the requested business process.
* Record the transaction ID.
* Store the authentication status for audit purposes.

### Failed Authentication

```text
ret = "n"
```

Your application should:

* Mark the authentication as failed.
* Read the applicable error or response information.
* Display an appropriate user-friendly message.
* Allow the resident to retry if the authentication method permits it.
* Record the transaction ID for troubleshooting and auditing.

---

## Authentication Result from the Provided Log

The decoded response contains:

```xml
<AuthRes
    ...
    ret="y"
    ts="2024-05-02T11:42:39.187+05:30"
    txn="2405021142340169818591290pmg">
```

The application log also shows:

```text
Authentication Ret : y
Authentication ts : 2024-05-02T11:42:39.187+05:30
Authentication txn : 2405021142340169818591290pmg
```

Therefore, based on the `ret` value:

```text
Authentication Result : SUCCESS
```

The authentication transaction was successfully authenticated.

---

## Complete Response Processing

```text
HTTP Response
    │
    ├── HTTP Status = 200
    │
    ▼
Parse Outer XML
    │
    ├── client_id
    ├── res_code
    ├── res_hash
    ├── res_data
    └── res_msg
    │
    ▼
Check res_code
    │
    ▼
Extract res_data
    │
    ▼
Base64 Decode res_data
    │
    ▼
Parse AuthRes XML
    │
    ├── code
    ├── info
    ├── ret
    ├── ts
    └── txn
    │
    ▼
Check ret
    │
    ├───────────────┐
    ▼               ▼
  ret = "y"       ret = "n"
    │               │
    ▼               ▼
Success           Failure
    │               │
    ▼               ▼
Continue          Handle Error
Business          / Retry
Process
```

---

## Response Validation Checklist

After receiving the response, verify that:

* The HTTP response was received successfully.
* The outer response XML is well-formed.
* The `client_id` is the expected client identifier.
* The `res_code` has been processed correctly.
* The `res_data` element is present.
* The Base64 decoding of `res_data` succeeds.
* The decoded XML contains the `AuthRes` root element.
* The `txn` value is correlated with the original authentication transaction.
* The `ret` attribute is checked.
* The authentication result is determined from `ret`.
* The response is handled securely.

---

## Important: HTTP 200 vs Authentication Success

Do not treat the following as the same:

```text
HTTP 200 OK
```

and:

```text
Authentication Successful
```

`HTTP 200 OK` only indicates that the HTTP communication was successful.

The actual authentication result must be determined from the decoded `AuthRes` XML.

For example:

```text
HTTP Status
    │
    ▼
200 OK
    │
    ▼
Parse Response
    │
    ▼
Decode res_data
    │
    ▼
Read AuthRes
    │
    ▼
Check ret
    │
    ├── y → Authentication Successful
    │
    └── n → Authentication Failed
```

---

## Best Practices

* Always process the complete response before determining the authentication result.
* Never consider HTTP `200 OK` alone as authentication success.
* Always Base64-decode the `res_data` element before parsing the Authentication Response.
* Validate the decoded XML before processing it.
* Correlate the response with the original transaction using the `txn` value.
* Log only the minimum information required for auditing and troubleshooting.
* Do not log complete `res_data` values in production logs.
* Do not store sensitive authentication data unnecessarily.
* Handle invalid Base64 data and malformed XML gracefully.
* Use secure XML parsing techniques to prevent XML-based attacks.

---

## Authentication Complete

If the decoded Authentication Response contains:

```xml
ret="y"
```

the authentication process is considered successful.

The complete authentication workflow is:

```text
Collect Authentication Input
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
Digitally Sign Authentication XML
        │
        ▼
Create Final AUA Request
        │
        ▼
Send Authentication Request
        │
        ▼
Receive HTTP Response
        │
        ▼
Parse Outer Response XML
        │
        ▼
Extract res_data
        │
        ▼
Base64 Decode res_data
        │
        ▼
Parse AuthRes
        │
        ▼
Check ret
        │
        ▼
Authentication Successful ✅
```

---

## Notes

> A successful HTTP response does not necessarily mean that Aadhaar authentication was successful. Always decode and process the `res_data` element and check the `ret` attribute in the resulting `AuthRes` XML.

> The `res_data` element contains the Authentication Response XML in Base64-encoded form and must be decoded before the actual authentication result can be processed.

> In the provided example, the decoded `AuthRes` contains `ret="y"`, indicating that the authentication was successful.

> Do not store raw biometric data, OTP values, PID XML, decrypted PID data, session keys, or other sensitive authentication information after the authentication process is complete.

> Retain only the minimum transaction information required for auditing, reconciliation, monitoring, and regulatory compliance according to your organization's data retention policy.
