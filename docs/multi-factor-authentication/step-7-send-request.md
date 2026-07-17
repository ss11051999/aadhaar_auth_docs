---
sidebar_postion : 7
---

# Step 7 - Send Authentication Request

## Goal

After the Authentication Request XML has been successfully generated and digitally signed, the next step is to send the request to the **Aadhaar Authentication Server**.

The signed XML is transmitted over a secure **HTTPS** connection using the **HTTP POST** method. The Authentication Server validates the request, verifies the digital signature, decrypts the authentication data, performs Multi-Factor Authentication, and returns an Authentication Response XML.

---

## Request Flow

```text id="mfa-step7-flow"
Signed Authentication
Request XML
        │
        ▼
Open HTTPS Connection
        │
        ▼
Send HTTP POST Request
        │
        ▼
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
Perform Multi-Factor Authentication
        │
        ▼
Generate Authentication Response
```

---

## HTTP Request

The Authentication Request XML should be sent using an **HTTP POST** request.

### HTTP Method

```http id="mfa-http-method"
POST
```

### Recommended HTTP Headers

```http id="mfa-http-headers"
Content-Type: application/xml
Accept: application/xml
```

The request body should contain the complete **digitally signed Authentication Request XML**.

---

## Request Body

The body of the HTTP request consists of the complete signed Authentication Request XML generated in the previous step.

```text id="mfa-request-body"
POST /authentication-endpoint

Request Body
------------------------------------

Signed Authentication Request XML

------------------------------------
```

---

## What Happens on the Authentication Server?

Once the Authentication Server receives the request, it performs several validation and processing steps.

It typically:

1. Validates the XML structure.
2. Verifies the digital signature.
3. Validates request parameters.
4. Decrypts the Session Key.
5. Decrypts the PID XML.
6. Verifies the HMAC.
7. Validates all provided authentication factors (such as OTP, Fingerprint, Iris, or Face).
8. Determines whether the resident has been successfully authenticated.
9. Generates an Authentication Response XML.

If any validation or authentication step fails, an error response is returned.

---

## Validation Checklist

Before sending the request, verify that:

* The Authentication Request XML is complete.
* The XML has been digitally signed.
* The request uses HTTPS.
* The HTTP method is POST.
* The required HTTP headers are included.
* The request body contains the signed Authentication Request XML.
* The endpoint URL is correct for the current environment (Sandbox or Production).

---

## Common Errors

| Error                          | Possible Cause                                  |
| ------------------------------ | ----------------------------------------------- |
| Connection failed              | Incorrect endpoint or network issue             |
| HTTP 400 Bad Request           | Invalid or malformed XML                        |
| HTTP 401 Unauthorized          | Invalid credentials or license key              |
| HTTP 403 Forbidden             | Access not permitted                            |
| HTTP 500 Internal Server Error | Temporary server-side issue                     |
| Request timeout                | Network delay or server did not respond in time |

---

## Best Practices

* Always use **HTTPS** to transmit authentication requests.
* Use the **POST** method for every request.
* Ensure the Authentication Request XML has been digitally signed before sending.
* Verify the endpoint URL for the correct environment.
* Generate a unique Transaction ID for every authentication request.
* Implement appropriate timeout and retry mechanisms for temporary network failures.
* Do not resend a request containing expired OTPs or outdated biometric data. Generate a new authentication request instead.

---

## Output of this Step

After the request is successfully sent:

* The Authentication Server receives the signed Authentication Request XML.
* The request is validated and processed.
* All supplied authentication factors are verified.
* An Authentication Response XML is generated.

Your application should now wait for the server response.

---

## Next Step

Continue to **Step 8 – Receive Authentication Response**, where the Authentication Response XML is received, parsed, and the final authentication result is processed.

---

## Notes

> The request transmission process is identical for **OTP**, **Biometric**, **Demographic**, **Face**, **eKYC**, and **Multi-Factor Authentication**. The only difference is the authentication data contained within the encrypted PID XML and the authentication factors specified in the `<Uses>` element.

> Always send the Authentication Request XML to the appropriate Aadhaar Authentication endpoint (Sandbox or Production) over a secure HTTPS connection.

> A successful HTTP request indicates only that the request reached the server. The actual authentication result must be determined by processing the Authentication Response XML returned by the Aadhaar Authentication Server.
