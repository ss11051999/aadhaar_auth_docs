---
sidebar_position: 7
---

# Step 7 - Send Authentication Request

## Goal

After generating and digitally signing the Authentication Request XML, the final request must be sent securely to the **AUA Server** over an HTTPS connection.

In the Sub-AUA architecture, the Sub-AUA does not directly send the Authentication Request to the UIDAI Authentication Server.

The Sub-AUA sends the request to the AUA using the integration API provided by the AUA. The AUA then processes the request and handles communication with the UIDAI authentication infrastructure.

The request flow is:

```text
Sub-AUA
    │
    │ HTTPS POST
    │ client_id
    │ req_hash
    │ req_data
    ▼
AUA Server
    │
    │ Process / Validate Request
    ▼
UIDAI Authentication Infrastructure
    │
    ▼
Authentication Response
    │
    ▼
AUA Server
    │
    ▼
Sub-AUA
```

---

## Prerequisites

Before sending the request, ensure that:

* The Authentication Request XML has been generated successfully.
* The XML has been digitally signed.
* The request contains the encrypted PID block (`Data`).
* The encrypted session key (`Skey`) has been included.
* The HMAC has been generated and included.
* The `client_id` provided by the AUA is valid.
* The request hash (`req_hash`) has been generated correctly.
* The `req_hash` corresponds to the request data being sent in `req_data`.
* The AUA endpoint is correctly configured.
* HTTPS is being used for communication.

---

## Request Workflow

```text
Generate Authentication Request XML
                │
                ▼
Digitally Sign XML
                │
                ▼
Generate Request Hash
                │
                ▼
Create Sub-AUA Request
                │
                ├── client_id
                ├── req_hash
                └── req_data
                │
                ▼
Create HTTPS POST Request
                │
                ▼
Send Request to AUA
                │
                ▼
AUA Server
                │
                ▼
AUA Processes Request
                │
                ▼
AUA Handles UIDAI Communication
                │
                ▼
Receive Authentication Response
                │
                ▼
Return Response to Sub-AUA
```

---

## HTTP Method

The Sub-AUA sends the request to the AUA using the HTTP `POST` method.

```http
POST
```

---

## Request URL

Send the request to the Authentication endpoint provided by the AUA.

Example:

```text
https://aua.example.com/api/authentication
```

> Replace the example URL with the actual endpoint provided by your AUA.

---

## HTTP Headers

Typical request headers:

```http
Content-Type: application/xml
Accept: application/xml
```

Additional headers may be required depending on your AUA integration.

---

## Request Body

The Sub-AUA request is wrapped inside an `<xml>` root element.

The request contains the following fields:

| Field | Description |
|---|---|
| `client_id` | Identifier provided by the AUA for the Sub-AUA/client |
| `req_hash` | Hash generated for the request data |
| `req_data` | Digitally signed Authentication Request XML |

Example:

```xml
<?xml version='1.0' encoding='utf-8'?>
<xml>
    <client_id>AUA-CHP</client_id>

    <req_hash>
        BASE64_ENCODED_REQUEST_HASH
    </req_hash>

    <req_data>
        Signed Authentication Request XML
    </req_data>
</xml>
```

The `req_data` field contains the Authentication Request XML generated and digitally signed by the Sub-AUA.

> The exact format and encoding of `req_data` should follow the API contract provided by the AUA.

---

## Example OTP Authentication Request

For OTP-based authentication, the `req_data` contains the digitally signed Authentication Request XML with the OTP authentication method enabled.

Example:

```xml
<?xml version='1.0' encoding='utf-8'?>
<xml>
    <client_id>AUA-CHP</client_id>

    <req_hash>
        BASE64_ENCODED_REQUEST_HASH
    </req_hash>

    <req_data>
        <![CDATA[
            <?xml version="1.0" encoding="UTF-8"?>
            <Auth>
                ...
                <Uses otp="y"/>

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
        ]]>
    </req_data>
</xml>
```

> The above example is for documentation purposes. The actual `req_data` format should be implemented according to the API specification provided by the AUA.

---

## What Happens on the AUA Server?

After receiving the request, the AUA server performs the required validation and processing.

The AUA may:

1. Validate the `client_id`.
2. Validate the Sub-AUA credentials.
3. Verify the `req_hash`.
4. Validate the `req_data`.
5. Validate the Authentication Request XML.
6. Verify the digital signature.
7. Validate the encrypted authentication data.
8. Validate the OTP authentication request.
9. Forward the appropriate authentication request to the UIDAI authentication infrastructure.
10. Receive the Authentication Response.
11. Return the Authentication Response to the Sub-AUA.

---

## Request Flow Between Sub-AUA, AUA and UIDAI

There are two separate communication layers.

### Request 1 - Sub-AUA to AUA

```text
Sub-AUA
    │
    │ HTTPS POST
    │
    │ client_id
    │ req_hash
    │ req_data
    ▼
AUA Server
```

The Sub-AUA sends the authentication request to the AUA using the integration API provided by the AUA.

---

### Request 2 - AUA to UIDAI

```text
AUA
    │
    │ Authentication Request
    ▼
UIDAI Authentication Infrastructure
```

The AUA handles the UIDAI-specific authentication integration and communication with the authentication infrastructure.

---

## Complete Request Structure

```text
┌─────────────────────────────────┐
│ Authentication Request XML      │
│                                 │
│ - Auth                          │
│ - Uses otp="y"                  │
│ - Device                        │
│ - Skey                          │
│ - Data                          │
│ - Hmac                          │
│ - Signature                     │
└───────────────┬─────────────────┘
                │
                ▼
        Generate Request Hash
                │
                ▼
┌─────────────────────────────────┐
│         Sub-AUA Request         │
│                                 │
│ client_id                       │
│ req_hash                        │
│ req_data                        │
└───────────────┬─────────────────┘
                │
                │ HTTPS POST
                ▼
┌─────────────────────────────────┐
│           AUA Server            │
└───────────────┬─────────────────┘
                │
                │ Authentication Request
                ▼
┌─────────────────────────────────┐
│ UIDAI Authentication            │
│ Infrastructure                  │
└───────────────┬─────────────────┘
                │
                │ Authentication Response
                ▼
┌─────────────────────────────────┐
│           AUA Server            │
└───────────────┬─────────────────┘
                │
                │ Authentication Response
                ▼
┌─────────────────────────────────┐
│           Sub-AUA               │
└─────────────────────────────────┘
```

---

## Validation Checklist

Before sending the request, verify that:

* The Authentication Request XML is well-formed.
* The XML has been digitally signed.
* The `Uses` element is configured for OTP authentication.
* The `Skey`, `Data`, and `Hmac` elements are present where applicable.
* The `client_id` is valid.
* The `req_hash` has been generated correctly.
* The `req_hash` corresponds to the correct `req_data`.
* The `req_data` contains the correct signed Authentication Request XML.
* The Transaction ID is unique.
* The timestamp is current.
* HTTPS is being used.
* SSL/TLS certificate validation is enabled.
* The correct AUA endpoint is configured.

---

## Common Errors

| Error | Possible Cause |
|---|---|
| Invalid `client_id` | Sub-AUA/client identifier is invalid |
| Invalid `req_hash` | Hash does not match the request data |
| Invalid `req_data` | Request data is malformed or incomplete |
| HTTP 400 | Invalid or malformed request |
| HTTP 401 | Invalid credentials or authentication failure |
| HTTP 403 | Access denied |
| HTTP 404 | Incorrect AUA endpoint URL |
| HTTP 500 | Internal server error |
| Connection Timeout | Network issue or server unavailable |
| SSL/TLS Error | Invalid or untrusted SSL certificate |
| Invalid Signature | Digital signature verification failed |
| Invalid OTP Request | OTP authentication parameters are invalid |

---

## Best Practices

* Always use HTTPS to protect data in transit.
* Set a reasonable request timeout.
* Generate a unique Transaction ID for every authentication request.
* Generate the request hash from the correct request data.
* Ensure that `req_hash` and `req_data` always correspond to each other.
* Do not modify `req_data` after generating the request hash.
* Log only non-sensitive request information such as the Transaction ID and timestamp.
* Never log Aadhaar numbers, OTP values, PID XML, session keys, HMAC values, or the complete Authentication Request XML in production logs.
* Retry requests only for temporary network failures.
* Do not blindly retry authentication requests that have failed due to validation or authentication errors.

---

## Output of this Step

After successfully sending the request:

```text
Sub-AUA
    │
    │ client_id
    │ req_hash
    │ req_data
    ▼
AUA Server
```

The AUA receives the authentication request and begins processing it.

The AUA then handles the communication with the UIDAI authentication infrastructure.

After processing the request, the Authentication Response is returned to the Sub-AUA.

The response indicates whether the authentication request was successful or failed.

---

## Next Step

Continue to **Step 8 - Receive Authentication Response**.

The Sub-AUA will receive the Authentication Response from the AUA after the authentication request has been processed.

The Sub-AUA should parse the response and determine whether the authentication was successful or failed.

---

## Notes

> In a Sub-AUA architecture, the request sent by the Sub-AUA to the AUA is an integration layer between the Sub-AUA and AUA. The exact request wrapper, including fields such as `client_id`, `req_hash`, and `req_data`, is defined by the AUA's integration API.

> The `req_data` field should contain the authentication request data agreed upon between the Sub-AUA and AUA. If the AUA requires the complete digitally signed Authentication XML, the Sub-AUA should provide that XML in `req_data`.

> The Sub-AUA-to-AUA request and the AUA-to-UIDAI request are separate communication layers and should be documented separately.

> A successful HTTP response, such as **200 OK**, only indicates that the request was received and processed at the HTTP/API level. It does **not** guarantee that Aadhaar authentication was successful.

> Always parse the Authentication Response XML and verify the response attributes to determine the actual authentication result.

> The authentication flow described in this document applies to OTP-based authentication at the Sub-AUA integration layer. The exact AUA-to-UIDAI communication process depends on the AUA/ASA integration and the applicable UIDAI API specification.