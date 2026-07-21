---
sidebar_position: 8
---

# Step 8 - Receive and Process Authentication Response

## Goal

After the Authentication Request has been successfully processed by the **Sub-AUA / Authentication Service Provider**, the Sub-AUA returns a **Sub-AUA Response XML** to the requesting application.

The Sub-AUA response acts as the response received from the intermediary integration layer. It contains the response code, response message, and an encoded `res_data` field containing the actual **Aadhaar Authentication Response XML** returned by the upstream authentication system.

Your application should first validate the Sub-AUA response, decode the `res_data` value, parse the resulting Authentication Response XML, and then determine the actual authentication result.

> **Important:** An HTTP `200 OK` response only indicates that the HTTP request was successfully received and processed at the transport level. It does not necessarily mean that Aadhaar authentication was successful.

---

## Response Flow

```text
Authentication Request
        │
        ▼
Sub-AUA / Authentication Service Provider
        │
        ▼
Sub-AUA Response XML
        │
        ▼
Check HTTP Response Status
        │
        ▼
Read res_code and res_msg
        │
        ▼
Read Base64 Encoded res_data
        │
        ▼
Base64 Decode res_data
        │
        ▼
Authentication Response XML
        │
        ▼
Parse AuthRes
        │
        ▼
Check ret Attribute
        │
   ┌────┴────┐
   ▼         ▼
Success    Failure
ret="y"    ret="n"
   │         │
   ▼         ▼
Continue   Handle Error
Business   Process
```

---

## Sub-AUA Response

The Sub-AUA response is returned as an XML document.

A typical response structure is:

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>
    <res_code>10</res_code>
    <res_hash>ResponseHashValue</res_hash>
    <res_data>Base64EncodedAuthenticationResponse</res_data>
    <res_msg>Success from CSC AAG.</res_msg>
</xml>
```

The response contains the following important fields:

| Element | Description |
| ------- | ----------- |
| `client_id` | Identifies the AUA / Sub-AUA client associated with the request. |
| `res_code` | Response code returned by the Sub-AUA or intermediary service. |
| `res_hash` | Hash value associated with the response data for response integrity verification, if required by the integration. |
| `res_data` | Base64-encoded Authentication Response XML. |
| `res_msg` | Human-readable response message from the Sub-AUA or intermediary service. |

---

## Example Response Received from Sub-AUA

The following is an example based on the response received from the Sub-AUA:

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>
    <res_code>10</res_code>
    <res_hash>UbHZv3KToV6b8xjg/jHXdzXhajbNDKKKD2ZlHMOk9xbio/ZZAN8q3jYIY2ayg2uL1PgbkHWZF0QW1i4nCq58ysD6n6XblFFgybSP/dgSOoM=</res_hash>
    <res_data>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiIHN0YW5kYWxvbmU9Im5vIj8+PEF1dGhSZXMgY29kZT0iNTEwNDdmMWUwZmY1NDk0YmFjZDM1YTE3YjY2ZDBkZTkiIGluZm89IjA0ezAxMDAwODA0Z3pGQ3g2Y3Z3WnBGbnZYVmF4T1N4VmJDMURlU05LRDExMXVXaFJvZXJxMEpCb0FYb2cvdThpSU9Lc0NQNTE2bixBLGUzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUsMDEwMDAwMzAwMDAwMDIxMCwyLjAsMjAyNDA1MDIxMTQyMzIsMSwxLDAsMCwyLjUsZjE4ODcwODAwMDEyNzIzN2U0OTJmODdkNTQxZWJmODc4NTAyMTI3NjAwMGQwOWU1NGZkOThiNjQwNzZlMmRjZCwxMGM3MzFhOTIyZGVjY2RlODYwZjkyZjdjZGEwNGQ4MzE4Y2MwMzMyYzhiY2I5ZThjYTMwZDMzYzNmNTIzYWM1LDEwYzczMWE5MjJkZWNjZGU4NjBmOTJmN2NkYTA0ZDgzMThjYzAzMzJjOGJjYjllOGNhMzBkMzNjM2Y1MjNhYzUsMjMsTkEsTkEsTkEsTkEsTkEsTkEsTkEsTkEscmVnaXN0ZXJlZCxBQ1BMLldJTi4wMDEsMS4wLjQsU1RBUlRFSy5BQ1BMLEZNMjIwVSxMMCxOQX0iIHJldD0ieSIgdHM9IjIwMjQtMDUtMDJUMTE6NDI6MzkuMTg3KzA1OjMwIiB0dG49IjI0MDUwMjExNDIzNDAxNjk4MTg1OTEyOTBwbWciPjxTaWduYXR1cmUgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvMDkveG1sZHNpZyMiPjxTaWduZWRJbmZvPjxDYW5vbmljYWxpemF0aW9uTWV0aG9kIEFsZ29yaXRobT0iaHR0cDovL3d3dy53My5vcmcvVFIvMjAwMS9SRUMteG1sLWMxNG4tMjAwMTAzMTUiLz48U2lnbmF0dXJlTWV0aG9kIEFsZ29yaXRobT0iaHR0cDovL3d3dy53My5vcmcvMjAwMC8wOS94bWxkc2lnI3JzYS1zaGExIi8+PFJlZmVyZW5jZSBVUkk9IiI+PFRyYW5zZm9ybXM+PFRyYW5zZm9ybSBBbGdvcml0aG09Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvMDkveG1sZHNpZyNlbnZlbG9wZWQtc2lnbmF0dXJlIi8+PC9UcmFuc2Zvcm1zPjxEaWdlc3RNZXRob2QgQWxnb3JpdGhtPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGVuYyNzaGEyNTYiLz48RGlnZXN0VmFsdWU+a2VXdlFnQ25MSVhiVzZlcEg5NksvOEtXdXRhb2FnMGVOYjMybFF5ZEdtbz08L0RpZ2VzdFZhbHVlPjwvUmVmZXJlbmNlPjwvU2lnbmVkSW5mbz48U2lnbmF0dXJlVmFsdWU+...PC9TaWduYXR1cmVWYWx1ZT48L1NpZ25hdHVyZT48L0F1dGhSZXM+</res_data>
    <res_msg>Success from CSC AAG.</res_msg>
</xml>
```

> The `res_data` value above is Base64 encoded and has been shortened for documentation readability. In an actual response, the complete value must be decoded.

---

## Step 1 - Check HTTP Response

The first step is to verify the HTTP response status.

Example:

```text
POST Response Code :: 200
```

An HTTP `200 OK` response indicates that the HTTP request was successfully completed.

However, the actual authentication result must be determined by processing the Sub-AUA response and the decoded `AuthRes` XML.

---

## Step 2 - Read Sub-AUA Response Fields

After parsing the Sub-AUA response, read the following values:

```text
client_id
res_code
res_hash
res_data
res_msg
```

For the example response:

```text
client_id : AUA-CHP
res_code  : 10
res_msg   : Success from CSC AAG.
```

The `res_data` field contains the Base64-encoded Authentication Response XML.

---

## Step 3 - Decode res_data

The `res_data` value must be Base64 decoded.

Conceptually:

```text
Base64 Encoded res_data
        │
        ▼
Base64 Decode
        │
        ▼
Authentication Response XML
```

After decoding, the response becomes:

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

> The actual `info` attribute may contain sensitive or integration-specific information. Do not log or expose the complete value unless required for debugging and permitted by your security policy.

---

## Step 4 - Parse Authentication Response

After decoding `res_data`, parse the resulting XML.

The root element in the received response is:

```text
AuthRes
```

The important attributes observed in the response are:

| Attribute | Example | Description |
| --------- | ------- | ----------- |
| `code` | `51047f1e0ff5494bacd35a17b66d0de9` | Response code or identifier returned in the Authentication Response. |
| `info` | `04{...}` | Additional authentication information returned by the authentication service. |
| `ret` | `y` | Indicates the authentication result. |
| `ts` | `2024-05-02T11:42:39.187+05:30` | Authentication response timestamp. |
| `txn` | `2405021142340169818591290pmg` | Transaction ID associated with the authentication request. |

The decoded response also contains a digital signature:

```xml
<Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
    ...
</Signature>
```

The signature should be handled according to your integration's response validation and security requirements.

---

## Step 5 - Determine Authentication Result

The most important field for determining the authentication result is:

```text
ret
```

In the received response:

```text
Authentication Ret : y
```

This indicates that the authentication response was successful according to the returned `ret` value.

> Follow the exact value conventions defined by your Sub-AUA / AUA integration. In this response, the value is lowercase `y`, while some standard examples may use uppercase `Y`.

---

## Step 6 - Validate Transaction ID

The response contains the transaction ID:

```text
Authentication txn : 2405021142340169818591290pmg
```

Your application should compare this value with the Transaction ID generated and sent in the original Authentication Request.

```text
Original Request Transaction ID
                │
                ▼
        Compare with txn
                │
        ┌───────┴───────┐
        ▼               ▼
      Match          Mismatch
        │               │
        ▼               ▼
 Continue          Reject / Investigate
```

A transaction mismatch should be treated as an unexpected response and should not be accepted as a valid response for the original request.

---

## Step 7 - Process the Final Authentication Status

The parsed response from the integration log contains:

```text
Status:
[
    Authentication Info,
    y,
    2024-05-02T11:42:39.187+05:30,
    51047f1e0ff5494bacd35a17b66d0de9,
    2405021142340169818591290pmg,
    NoErr,
    NoDesc
]
```

The application should interpret the response according to the fields defined by the Sub-AUA integration.

For the provided response:

```text
Authentication Result : y
Error Code            : NoErr
Error Description     : NoDesc
```

This indicates that the authentication was successful and no error was reported by the integration layer.

---

## Complete Response Processing Flow

```text
Receive HTTP Response
        │
        ▼
Check HTTP Status Code
        │
        ▼
Parse Sub-AUA Response XML
        │
        ▼
Read client_id
        │
        ▼
Read res_code
        │
        ▼
Read res_msg
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
Read ret
        │
        ▼
Validate txn
        │
        ▼
Process Authentication Result
        │
   ┌────┴────┐
   ▼         ▼
Success    Failure
ret="y"    ret="n"
   │         │
   ▼         ▼
Continue   Read Error
Business   Handle Failure
Process
```

---

## Validation Checklist

After receiving the Sub-AUA response, verify that:

* The HTTP response status is valid.
* The Sub-AUA response XML is well-formed.
* The expected `client_id` is present.
* The `res_code` has been processed according to the Sub-AUA integration specification.
* The `res_msg` has been captured where required.
* The `res_data` field is present.
* The Base64 encoded `res_data` is successfully decoded.
* The decoded XML has `AuthRes` as the root element.
* The `ret` attribute is present.
* The response Transaction ID matches the original request.
* Any returned error code or description is handled correctly.
* Sensitive authentication information is not written to application logs.

---

## Common Response Scenarios

| Scenario | Meaning |
| -------- | ------- |
| HTTP `200` + `res_code=10` + `ret="y"` | Sub-AUA request processed successfully and authentication result is successful. |
| HTTP `200` + `res_code` indicates failure | HTTP transport succeeded, but the Sub-AUA reported an integration or processing failure. |
| HTTP `200` + `ret="n"` | Request reached the service, but authentication was unsuccessful. |
| Missing `res_data` | Authentication response data is unavailable or the Sub-AUA response is incomplete. |
| Invalid Base64 | The `res_data` value could not be decoded. |
| Invalid XML after decoding | The decoded response is malformed or incomplete. |
| Transaction mismatch | The response does not belong to the original authentication request. |
| Network timeout | The application did not receive a response within the configured timeout. |

---

## Best Practices

* Do not treat HTTP `200 OK` as proof of successful authentication.
* Process the Sub-AUA response before processing the decoded Authentication Response.
* Validate `res_code` and `res_msg` according to the Sub-AUA integration specification.
* Decode `res_data` only after validating that it is present and correctly formatted.
* Validate the decoded XML structure.
* Match the response `txn` with the original request transaction ID.
* Evaluate the `ret` value to determine the actual authentication result.
* Validate the response signature if required by your integration specification.
* Log only non-sensitive transaction metadata.
* Never log Aadhaar Numbers, OTPs, biometric data, PID XML, Session Keys, HMAC values, or complete sensitive `info` data.
* Handle network failures separately from authentication failures.
* Do not retry an authentication request blindly after a timeout without considering transaction duplication and integration-specific retry rules.

---

## Output of this Step

At the end of this step, your application should have:

* HTTP response status.
* Sub-AUA `client_id`.
* Sub-AUA `res_code`.
* Sub-AUA `res_msg`.
* Decoded Authentication Response XML.
* Authentication result from `ret`.
* Authentication Transaction ID.
* Response timestamp.
* Error code and description, if applicable.

The Multi-Factor Authentication response processing is now complete.

---

## Next Steps

### If Authentication is Successful

* Continue the requested business workflow.
* Grant access to the requested service, if applicable.
* Store the minimum required transaction information for auditing and reconciliation.

### If Authentication Fails

* Read and process the returned error information.
* Display an appropriate user-friendly message.
* Allow the resident to retry if permitted.
* Refer to the **Error Codes** documentation for troubleshooting.

---

## Notes

> The response received from the Sub-AUA contains an additional integration layer around the actual Authentication Response. Your application must process both layers: first the Sub-AUA response (`client_id`, `res_code`, `res_hash`, `res_data`, and `res_msg`), and then the decoded `AuthRes` response.

> In the provided response, `res_code` is `10`, `res_msg` is `Success from CSC AAG.`, and the decoded `AuthRes` contains `ret="y"`. Based on these returned values, the provided transaction indicates a successful authentication response.

> A successful HTTP status or Sub-AUA response code does not by itself guarantee successful Aadhaar authentication. Always process the decoded `AuthRes` and evaluate the authentication result.

> For security and privacy reasons, never log sensitive information such as Aadhaar Numbers, OTPs, biometric data, face images, PID XML, Session Keys, HMAC values, or complete authentication response payloads. Log only the minimum information necessary for auditing and troubleshooting.
