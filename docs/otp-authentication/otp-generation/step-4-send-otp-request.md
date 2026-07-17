---
sidebar_position: 4
---

# Step 4 - Send OTP Request

## Goal

After generating and digitally signing the OTP Request XML, the next step is to send it securely to the Aadhaar Authentication Server using an HTTPS `POST` request.

---

## Prerequisites

Before sending the request, ensure that:

* The OTP Request XML has been generated successfully.
* The XML has been digitally signed.
* The target server URL (Sandbox or Production) is configured.
* A secure HTTPS connection is being used.
* The required HTTP headers are configured.

---

## Request Flow

```text
Generate OTP Request XML
          │
          ▼
Digitally Sign XML
          │
          ▼
Create HTTPS POST Request
          │
          ▼
Attach Signed XML as Request Body
          │
          ▼
Send Request to Aadhaar Authentication Server
          │
          ▼
Wait for Server Response
```

---

## HTTP Method

```
POST
```

---

## Request URL

Use the endpoint provided by your Aadhaar Authentication Service Provider or UIDAI.

**Example (Sandbox):**

```text
https://sandbox.example.com/otp
```

**Example (Production):**

```text
https://production.example.com/otp
```

> Replace these example URLs with the actual endpoint provided by your organization or service provider.

---

## HTTP Headers

Example:

```http
Content-Type: application/xml
Accept: application/xml
```

If your integration requires additional custom headers, include them as specified by your integration guidelines.

---

## Request Body

The request body should contain the **digitally signed OTP Request XML**.

Example:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Otp
    uid="999988887777"
    ac="public"
    sa="public123"
    lk="MKQ8R2XXXXXXXXXXXXXX"
    txn="OTP202607161530001"
    ts="2026-07-16T15:30:00"
    ver="2.5"
    ch="00">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>

</Otp>
```

---

## Transport Security

Always ensure that:

* HTTPS is used.
* TLS is enabled according to your deployment requirements.
* SSL certificate validation is enabled.
* Requests are sent only to trusted Aadhaar Authentication endpoints.

---

## Request Checklist

Before sending the request, verify that:

* XML is well-formed.
* XML is digitally signed.
* HTTPS endpoint is correct.
* Request body contains the signed XML.
* Required HTTP headers are present.
* Transaction ID is unique.
* Timestamp is current.

---

## Common Errors

| Error              | Possible Cause                             |
| ------------------ | ------------------------------------------ |
| HTTP 400           | Invalid XML or malformed request           |
| HTTP 401           | Authentication failure                     |
| HTTP 403           | Invalid credentials or access denied       |
| HTTP 404           | Incorrect endpoint URL                     |
| HTTP 500           | Server-side error                          |
| Connection Timeout | Network connectivity or server unavailable |
| SSL/TLS Error      | Certificate validation failed              |

---

## Best Practices

* Set an appropriate request timeout.
* Log only non-sensitive request metadata (such as Transaction ID and Timestamp).
* Never log Aadhaar numbers or signed XML in production logs.
* Retry only when the failure is due to a transient network issue, following your organization's retry policy.
* Ensure every request uses a new Transaction ID.

---

## Output of this Step

If the request is accepted by the server, an **OTP Response XML** will be returned.

The response will indicate whether:

* The OTP was generated successfully.
* The request failed due to validation or authentication errors.
* Additional error information is available.

The response processing is covered in the next step:

**Step 5 – Receive Response**

---

## Notes

> Do not modify the signed XML before sending it.

> Always use the endpoint corresponding to your current environment (Sandbox or Production).

> Store request logs carefully and avoid recording sensitive Aadhaar-related information unless required by your organization's security and compliance policies.
