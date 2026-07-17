---
sidebar_position: 7
---

# Step 7 - Send Authentication Request

## Goal

After generating and digitally signing the Authentication Request XML, the final request must be sent securely to the Aadhaar Authentication Server over an HTTPS connection.

The server validates the request, decrypts the encrypted data, verifies the digital signature, and performs Aadhaar authentication.

---

## Prerequisites

Before sending the request, ensure that:

* The Authentication Request XML has been generated successfully.
* The XML has been digitally signed.
* The request contains the encrypted PID block (`Data`).
* The encrypted session key (`Skey`) has been included.
* The HMAC has been generated and included.
* The target endpoint (Sandbox or Production) is correctly configured.

---

## Request Workflow

```text id="v2s0pd"
Generate Authentication Request XML
                │
                ▼
Digitally Sign XML
                │
                ▼
Create HTTPS POST Request
                │
                ▼
Attach Signed XML
                │
                ▼
Send Request
                │
                ▼
Aadhaar Authentication Server
                │
                ▼
Receive Authentication Response
```

---

## HTTP Method

```http
POST
```

---

## Request URL

Send the request to the Authentication endpoint provided by your Aadhaar Authentication Service Provider (AUA/ASA).

Example:

```text
Sandbox:
https://sandbox.example.com/auth

Production:
https://production.example.com/auth
```

> Replace the example URLs above with the actual endpoints provided for your environment.

---

## HTTP Headers

Typical request headers:

```http
Content-Type: application/xml
Accept: application/xml
```

Additional headers may be required depending on your integration.

---

## Request Body

The request body must contain the **digitally signed Authentication Request XML**.

Example:

```xml
<Auth ...>

    <Uses otp="y"/>

    <Meta .../>

    <Skey ci="20260701">
        EncryptedSessionKey
    </Skey>

    <Data type="X">
        EncryptedPIDBlock
    </Data>

    <Hmac>
        GeneratedHmacValue
    </Hmac>

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>

</Auth>
```

---

## What Happens on the Server?

After receiving your request, the Aadhaar Authentication Server performs the following operations:

1. Validates the XML structure.
2. Verifies the digital signature.
3. Decrypts the session key.
4. Decrypts the PID block.
5. Verifies the HMAC.
6. Validates the OTP.
7. Performs Aadhaar authentication.
8. Generates an Authentication Response.

---

## Validation Checklist

Before sending the request, verify that:

* The XML is well-formed.
* The request has been digitally signed.
* The `Skey`, `Data`, and `Hmac` elements are present.
* The Transaction ID is unique.
* The timestamp is current.
* HTTPS is being used.
* SSL/TLS certificate validation is enabled.

---

## Common Errors

| Error              | Possible Cause                                   |
| ------------------ | ------------------------------------------------ |
| HTTP 400           | Invalid or malformed XML                         |
| HTTP 401           | Authentication failed due to invalid credentials |
| HTTP 403           | Access denied                                    |
| HTTP 404           | Incorrect endpoint URL                           |
| HTTP 500           | Internal server error                            |
| Connection Timeout | Network issue or server unavailable              |
| SSL/TLS Error      | Invalid or untrusted SSL certificate             |

---

## Best Practices

* Always use HTTPS.
* Set a reasonable request timeout.
* Log only the Transaction ID and non-sensitive metadata.
* Never log the Aadhaar Number, OTP, PID XML, Session Key, or Authentication Request XML in production.
* Implement retry logic only for temporary network failures. Do not retry requests that failed due to validation or authentication errors without correcting the issue.

---

## Output of this Step

If the request is successfully processed, the Aadhaar Authentication Server returns an **Authentication Response XML**.

The response indicates:

* Whether authentication was successful.
* The transaction status.
* Any applicable error code or message.

The next step is:

**Step 8 – Receive Authentication Response**

---

## Notes

> A successful HTTP response (for example, **200 OK**) only means that the server received and processed the request. It does **not** guarantee that Aadhaar authentication was successful.

> Always parse the Authentication Response XML and verify the response attributes to determine the actual authentication result.
