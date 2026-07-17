---
sidebar_postion : 7
---

# Step 7 - Send Authentication Request

## Goal

After generating and digitally signing the Biometric Authentication Request XML, the next step is to send the request securely to the Aadhaar Authentication Server using an HTTPS `POST` request.

The server validates the request, decrypts the encrypted data, verifies the digital signature, and performs biometric authentication.

---

## Prerequisites

Before sending the request, ensure that:

* The Authentication Request XML has been generated successfully.
* The XML has been digitally signed.
* The encrypted PID block (`Data`) is included.
* The encrypted session key (`Skey`) is included.
* The HMAC (`Hmac`) is included.
* The Authentication endpoint (Sandbox or Production) is correctly configured.

---

## Request Workflow

```text id="ub2pnm"
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

```http id="s9wxrd"
POST
```

---

## Request URL

Send the request to the Authentication endpoint provided by your Aadhaar Authentication Service Provider (AUA/ASA).

Example:

```text id="84a0d9"
Sandbox:
https://sandbox.example.com/auth

Production:
https://production.example.com/auth
```

> Replace the example URLs above with the actual endpoint provided by your organization or service provider.

---

## HTTP Headers

Typical request headers:

```http id="e4mvzs"
Content-Type: application/xml
Accept: application/xml
```

Depending on your integration, additional HTTP headers may also be required.

---

## Request Body

The request body must contain the **digitally signed Authentication Request XML**.

Example:

```xml id="j0cmx4"
<Auth ...>

    <Uses bio="y"/>

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

After receiving the request, the Aadhaar Authentication Server performs the following operations:

1. Validates the XML structure.
2. Verifies the digital signature.
3. Decrypts the session key.
4. Decrypts the PID block.
5. Verifies the HMAC.
6. Validates the biometric data.
7. Matches the biometric data against the resident's Aadhaar record.
8. Generates an Authentication Response XML.

---

## Validation Checklist

Before sending the request, verify that:

* The Authentication Request XML is well-formed.
* The XML has been digitally signed.
* The `<Skey>`, `<Data>`, and `<Hmac>` elements are present.
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

* Always use HTTPS to protect data in transit.
* Set a reasonable request timeout.
* Log only non-sensitive request information such as the Transaction ID and timestamp.
* Never log biometric data, PID XML, session keys, HMAC values, or the complete Authentication Request XML in production.
* Retry requests only for temporary network failures. Do not retry requests that fail because of invalid biometric data or request validation errors.

---

## Output of this Step

If the request is successfully processed, the Aadhaar Authentication Server returns an **Authentication Response XML**.

The response indicates:

* Whether biometric authentication was successful.
* The transaction status.
* Any applicable error code or message.

The next step is:

**Step 8 – Receive Authentication Response**

---

## Notes

> A successful HTTP response (for example, **200 OK**) only confirms that the request was received and processed by the server. It does **not** indicate that biometric authentication was successful.

> Always examine the Authentication Response XML to determine the final authentication result.

> The request flow for **Biometric Authentication** is the same as **OTP Authentication** after the Authentication Request XML has been created. The primary difference is that the PID XML contains biometric data instead of an OTP.
