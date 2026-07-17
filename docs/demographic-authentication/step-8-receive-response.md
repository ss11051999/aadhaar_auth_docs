---
sidebar_postion: 8
---

# Step 8 - Receive Authentication Response

## Goal

After the Authentication Request has been successfully processed by the Aadhaar Authentication Server, the final step is to receive and process the **Authentication Response XML**.

The response indicates whether the demographic authentication was successful or unsuccessful. It may also contain an error code if the request could not be authenticated or processed.

Your application should parse the response, verify the authentication result, and take the appropriate action.

---

## Authentication Response Flow

```text id="demo-step8-flow"
Authentication Server
        │
        ▼
Process Authentication Request
        │
        ▼
Validate Request
        │
        ▼
Verify Resident Information
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

The Authentication Server returns an XML response containing the authentication result and additional transaction details.

### Example – Successful Response

```xml id="demo-success-response"
<AuthRes
    ret="Y"
    code="100"
    txn="DEMO202607170001"
    ts="2026-07-17T12:15:20"
    info="..."
/>
```

### Example – Failed Response

```xml id="demo-failed-response"
<AuthRes
    ret="N"
    err="400"
    txn="DEMO202607170001"
    ts="2026-07-17T12:15:20"
/>
```

> The examples above are simplified for illustration purposes. The actual response may include additional attributes depending on the API version and authentication type.

---

## Important Response Attributes

| Attribute | Description                                                                  |
| --------- | ---------------------------------------------------------------------------- |
| `ret`     | Authentication result (`Y` = Success, `N` = Failure)                         |
| `err`     | Error code returned when authentication fails                                |
| `code`    | Response or status code, when applicable                                     |
| `txn`     | Transaction ID corresponding to the request                                  |
| `ts`      | Timestamp indicating when the response was generated                         |
| `info`    | Additional information returned by the Authentication Server (if applicable) |

---

## Processing the Response

Once the response is received, your application should:

1. Verify that a valid response was received.
2. Parse the Authentication Response XML.
3. Check the value of the `ret` attribute.
4. If `ret="Y"`, continue with the business process.
5. If `ret="N"`, read the `err` attribute to determine the reason for failure.
6. Record the transaction for auditing and troubleshooting.

---

## Response Workflow

```text id="demo-response-workflow"
Receive Response XML
        │
        ▼
Parse XML
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
Business   Display Message
Process    Handle Failure
```

---

## Validation Checklist

After receiving the response, verify that:

* The response XML is valid.
* The Transaction ID matches the original request.
* The `ret` attribute is present.
* The response timestamp is valid.
* Any error code is captured if authentication failed.

---

## Common Errors

| Response             | Meaning                                   |
| -------------------- | ----------------------------------------- |
| `ret="N"`            | Authentication failed                     |
| Missing response     | Network issue or timeout                  |
| Invalid XML          | Malformed or incomplete response          |
| Transaction mismatch | Transaction ID does not match the request |
| Unknown error code   | Refer to the Error Codes documentation    |

---

## Best Practices

* Always validate the response before processing it.
* Match the response Transaction ID with the original request.
* Handle both success and failure scenarios gracefully.
* Display user-friendly error messages instead of raw error codes.
* Log only non-sensitive transaction details.
* Never assume authentication is successful based only on an HTTP `200 OK` response.
* Always check the `ret` attribute in the Authentication Response XML.

---

## Output of this Step

At the end of this step, your application should have:

* Authentication result (Success or Failure)
* Transaction ID
* Response Timestamp
* Error Code (if applicable)
* Authentication response available for further processing

The authentication process is now complete.

---

## Next Steps

Depending on the authentication result:

### If Authentication is Successful

* Grant access to the requested service.
* Continue the application workflow.
* Record the successful transaction.

### If Authentication Fails

* Read the returned error code.
* Inform the resident of the failure.
* Allow the resident to retry if appropriate.
* Refer to the **Error Codes** section for troubleshooting guidance.

---

## Notes

> A successful HTTP response (such as **200 OK**) does **not** necessarily mean that authentication was successful. Always check the `ret` attribute in the Authentication Response XML.

> The response processing workflow is the same for **OTP**, **Biometric**, **Demographic**, **Face**, **eKYC**, and **Multi-Factor Authentication**. Only the authentication method used to generate the request differs.

> Keep transaction logs for auditing purposes, but never log sensitive resident information such as Aadhaar Number, PID XML, biometric data, or encryption keys.
