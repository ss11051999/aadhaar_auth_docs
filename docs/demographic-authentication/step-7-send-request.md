---
sidebar_postion : 7
---

# Step 7 - Send Authentication Request

## Goal

After the Authentication Request XML has been successfully generated and digitally signed, the next step is to send the request to the **Aadhaar Authentication Server**.

The request is transmitted over a secure **HTTPS** connection using the HTTP **POST** method. The Authentication Server validates the request, verifies the digital signature, decrypts the authentication data, performs the requested authentication, and returns an Authentication Response XML.

---

## Request Flow

```text id="demo-step7-flow"
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
Perform Demographic Authentication
        │
        ▼
Generate Authentication Response
```

---

## HTTP Request

The Authentication Request XML should be sent using an **HTTP POST** request.

### HTTP Method

```http id="demo-http-method"
POST
```

### Recommended HTTP Headers

```http id="demo-http-headers"
Content-Type: application/xml
Accept: application/xml
```

The request body should contain the complete **digitally signed Authentication Request XML**.

---

## Request Body

The body of the HTTP request consists of the complete signed Authentication Request XML generated in the previous step.

```text id="demo-request-body"
POST /authentication-endpoint

Request Body
------------------------------------

Signed Authentication Request XML

------------------------------------
```

---

## What Happens on the Authentication Server?

Once the request is received, the Authentication Server performs several validation steps.

It typically:

1. Validates the XML structure.
2. Verifies the digital signature.
3. Validates the request parameters.
4. Decrypts the Session Key.
5. Decrypts the PID XML.
6. Validates the HMAC.
7. Performs demographic authentication.
8. Generates an Authentication Response XML.

If any validation step fails, an error response is returned.

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
| HTTP 400 Bad Request           | Invalid XML or malformed request                |
| HTTP 401 Unauthorized          | Invalid credentials or license key              |
| HTTP 403 Forbidden             | Access not permitted                            |
| HTTP 500 Internal Server Error | Temporary server-side issue                     |
| Request timeout                | Network delay or server did not respond in time |

---

## Best Practices

* Always send requests over **HTTPS**.
* Use the **POST** method for every authentication request.
* Ensure the XML is digitally signed before sending.
* Verify the endpoint URL for the correct environment.
* Use a unique Transaction ID for each request.
* Implement appropriate timeout and retry mechanisms for temporary network failures.
* Do not resend the same request if it contains an expired OTP or outdated authentication data. Generate a new request instead.

---

## Output of this Step

After the request is successfully sent:

* The Authentication Server receives the signed Authentication Request XML.
* The request is validated and processed.
* An Authentication Response XML is generated.

Your application should now wait for the server response.

---

## Next Step

Continue to **Step 8 – Receive Authentication Response**, where the Authentication Response XML is received, parsed, and the authentication result is processed.

---

## Notes

> The process of sending the Authentication Request is the same for **OTP**, **Biometric**, **Demographic**, **Face**, **eKYC**, and **Multi-Factor Authentication**. Only the authentication data contained within the request differs.

> Always use the appropriate Authentication Server endpoint for your environment (Sandbox or Production), and ensure that all communication takes place over a secure HTTPS connection.
