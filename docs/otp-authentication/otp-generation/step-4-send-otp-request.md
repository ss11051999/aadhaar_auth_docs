---
sidebar_position: 4
---

# Step 4 - Send OTP Request

## Goal

Transmit the digitally signed OTP Request XML from the **Sub-AUA** to the **AUA Server** over a secure HTTPS connection. The AUA server processes the request, performs initial validations, and handles communication with the UIDAI infrastructure.

---

## Architecture & Communication Flow

```text
Sub-AUA (Client) ──[HTTPS POST]──► AUA Server ──► UIDAI Infrastructure
```

```text
┌─────────────────────────────────────────┐
│        1. Signed OTP Request XML        │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│     2. Generate Hash (req_hash)         │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│  3. Wrap Payload into Sub-AUA Request   │
│   - client_id                           │
│   - req_hash                            │
│   - req_data (Signed OTP XML)           │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│   4. Send HTTPS POST to AUA Server      │
└─────────────────────────────────────────┘
```

---

## Request Structure

The Sub-AUA wraps its signed OTP request into the outer XML payload expected by the AUA endpoint.

### HTTP Details
- **Method:** `POST`
- **URL:** `https://aua.example.com/api/otp` *(replace with configured endpoint)*
- **Headers:**
  ```http
  Content-Type: application/xml
  Accept: application/xml
  ```

### Request Payload Example

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>
    <req_hash>BASE64_ENCODED_REQUEST_HASH</req_hash>
    <req_data>
        <![CDATA[
            <?xml version="1.0" encoding="UTF-8"?>
            <Otp
                uid="[Aadhaar Redacted]"
                ac="public"
                sa="public123"
                lk="AUA_LICENSE_KEY"
                txn="OTP202607161530001"
                ts="2026-07-16T15:30:00"
                ver="2.5"
                ch="00">

                <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
                    ...
                </Signature>

            </Otp>
        ]]>
    </req_data>
</xml>
```

| Field | Description |
| :--- | :--- |
| `client_id` | Identifier assigned to the Sub-AUA by the AUA. |
| `req_hash` | Hash of `req_data` to ensure integrity during transit. |
| `req_data` | The digitally signed OTP Request XML payload (`<Otp>`). |

---

## AUA Server Processing

Upon receiving the payload, the AUA Server executes the following steps:

1. Validates `client_id` and Sub-AUA access rights.
2. Verifies `req_hash` against `req_data`.
3. Validates digital signature and XML syntax in `req_data`.
4. Routes request to **UIDAI Authentication Infrastructure**.
5. Collects the UIDAI OTP response and forwards it back to the Sub-AUA.

---

## Pre-Flight Checklist

- [ ] XML is well-formed and digitally signed.
- [ ] `req_hash` matches current `req_data`.
- [ ] Valid `client_id` is included.
- [ ] Transaction ID (`txn`) is unique and timestamp (`ts`) is current.
- [ ] HTTPS transport with strict SSL/TLS certificate validation is enforced.

---

## Common Errors

| Error | Likely Cause |
| :--- | :--- |
| **Invalid `client_id`** | Sub-AUA identifier is incorrect or unauthorized. |
| **Invalid `req_hash`** | Hash mismatch (payload was modified after hash generation). |
| **Invalid `req_data` / Signature** | Malformed XML or signature verification failed. |
| **HTTP 400 / 401 / 403** | Bad request format, unauthorized, or access forbidden. |
| **HTTP 404 / 500 / Timeout** | Incorrect AUA URL, server error, or connection timeout. |

---

## Best Practices

* **Secure Transport:** Always use HTTPS; never transmit requests over unencrypted HTTP.
* **Integrity Enforcement:** Do not modify `req_data` after computing `req_hash`.
* **Unique Transaction IDs:** Generate a new `txn` for every attempt.
* **Avoid Unsafe Retries:** Do not automatically retry OTP requests on logic errors, as this may trigger duplicate OTP generation.
* **Logging Hygiene:** Never log raw OTP values, signed XML, or resident identification numbers in production logs.