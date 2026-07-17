---
sidebar_postion: 8
---

# Step 8 - Receive Authentication Response

## Goal

After the Authentication Request has been successfully processed by the Aadhaar Authentication Server, the final step is to receive and process the **Authentication Response XML**.

The response indicates whether the **Multi-Factor Authentication** was successful or unsuccessful. If the authentication fails, the response also contains an error code explaining the reason for the failure.

Your application should parse the response, verify the authentication result, and continue the business process accordingly.

---

## Authentication Response Flow

```text id="mfa-step8-flow"
Authentication Server
        │
        ▼
Validate Request
        │
        ▼
Verify Digital Signature
        │
        ▼
Decrypt Authentication Data
        │
        ▼
Verify Multiple Authentication Factors
        │
        ▼
Generate Authentication Response
        │
        ▼
Return Response XML
        │
        ▼
Parse Response
        │
        ▼
Process Authentication Result
```

---

## Authentication Response XML

After processing the authentication request, the Authentication Server returns an XML response containing the authentication result.

### Example – Successful Response

```xml id="mfa-success-response"
<AuthRes
    ret="Y"
    code="100"
    txn="MFA202607170001"
    ts="2026-07-17T15:10:25"
    info="..."
/>
```

---

### Example – Failed Response

```xml id="mfa-failed-response"
<AuthRes
    ret="N"
    err="400"
    txn="MFA202607170001"
    ts="2026-07-17T15:10:25"
/>
```

> The examples above are simplified for documentation purposes. The actual response may include additional attributes depending on the Aadhaar Authentication API version and the authentication method used.

---

## Important Response Attributes

| Attribute | Description                                                                  |
| --------- | ---------------------------------------------------------------------------- |
| `ret`     | Authentication result (`Y` = Success, `N` = Failure)                         |
| `err`     | Error code returned when authentication fails                                |
| `code`    | Response or status code, when applicable                                     |
| `txn`     | Transaction ID corresponding to the authentication request                   |
| `ts`      | Timestamp indicating when the response was generated                         |
| `info`    | Additional information returned by the Authentication Server (if applicable) |

---

## Processing the Response

After receiving the Authentication Response XML, your application should:

1. Verify that a valid response has been received.
2. Parse the Authentication Response XML.
3. Verify that the Transaction ID matches the original request.
4. Check the value of the `ret` attribute.
5. If `ret="Y"`, continue with the application or business workflow.
6. If `ret="N"`, read the `err` attribute to determine the cause of the failure.
7. Record the transaction for auditing, monitoring, and troubleshooting purposes.

---

## Response Workflow

```text id="mfa-response-workflow"
Receive Response XML
        │
        ▼
Parse XML
        │
        ▼
Validate Transaction ID
        │
        ▼
Check ret Attribute
        │
   ┌────┴────┐
   ▼         ▼
Success    Failure
(ret="Y")  (ret="N")
   │         │
   ▼         ▼
Continue   Read Error Code
Business   Display Appropriate Message
Process    Retry if Applicable
```

---

## Validation Checklist

After receiving the response, verify that:

* The Authentication Response XML is valid.
* The Transaction ID matches the original request.
* The `ret` attribute is present.
* The response timestamp is valid.
* Any returned error code has been captured for logging or troubleshooting.
* The authentication result has been processed correctly.

---

## Common Errors

| Response             | Meaning                                      |
| -------------------- | -------------------------------------------- |
| `ret="N"`            | Multi-Factor Authentication failed           |
| Missing response     | Network issue or request timeout             |
| Invalid XML          | Malformed or incomplete response             |
| Transaction mismatch | Response does not match the original request |
| Unknown error code   | Refer to the **Error Codes** section         |

---

## Best Practices

* Always validate the Authentication Response XML before processing it.
* Match the response Transaction ID with the original request.
* Handle both successful and failed authentication scenarios gracefully.
* Display user-friendly messages instead of raw error codes.
* Log only non-sensitive transaction details.
* Never assume authentication was successful based solely on an HTTP `200 OK` response.
* Always evaluate the `ret` attribute to determine the actual authentication result.

---

## Output of this Step

At the end of this step, your application should have:

* Authentication result (Success or Failure)
* Transaction ID
* Response Timestamp
* Error Code (if applicable)
* Authentication response ready for business processing

The Multi-Factor Authentication process is now complete.

---

## Next Steps

### If Authentication is Successful

* Grant access to the requested service.
* Continue the application workflow.
* Record the successful authentication transaction.

### If Authentication Fails

* Read the returned error code.
* Inform the resident about the authentication failure.
* Allow the resident to retry if appropriate.
* Refer to the **Error Codes** documentation for troubleshooting guidance.

---

## Notes

> A successful HTTP response (such as **200 OK**) does **not** indicate that Multi-Factor Authentication was successful. Always check the `ret` attribute in the Authentication Response XML.

> During Multi-Factor Authentication, **all configured authentication factors** (such as OTP, Fingerprint, Iris, or Face) are evaluated together. If any required factor fails verification, the authentication request may be rejected.

> For security and privacy reasons, never log sensitive information such as Aadhaar Numbers, OTPs, biometric data, face images, PID XML, Session Keys, or HMAC values. Log only the information necessary for auditing and troubleshooting.
