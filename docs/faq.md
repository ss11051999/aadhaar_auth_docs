---
sidebar_position: 12
---

# Frequently Asked Questions (FAQ)

This section answers the most common questions about integrating and using the Aadhaar Authentication APIs.

---

## 1. What is Aadhaar Authentication?

Aadhaar Authentication is a process of verifying a resident's identity using their Aadhaar Number (UID) or Virtual ID (VID) along with one or more authentication factors such as OTP, Biometrics, Demographic information, or Face Authentication.

---

## 2. What authentication methods are supported?

The Aadhaar Authentication APIs support the following authentication methods:

* OTP Authentication
* Biometric Authentication
* Demographic Authentication
* Face Authentication
* eKYC Authentication
* Multi-Factor Authentication

The available authentication methods depend on the permissions granted to your organization.

---

## 3. What is the difference between Authentication and eKYC?

**Authentication** only verifies whether the resident's identity is valid.

**eKYC** verifies the resident's identity and, with the resident's consent, returns KYC information such as name, address, photograph, gender, and date of birth.

---

## 4. Can I use Virtual ID (VID) instead of Aadhaar Number?

Yes.

You can use either:

* Aadhaar Number (UID)
* Virtual ID (VID)

Only one identifier should be included in an authentication request.

---

## 5. What is PID XML?

PID (Personal Identity Data) XML is a secure XML document that contains the authentication data, such as:

* OTP
* Fingerprint
* Iris
* Face data
* Demographic information (when applicable)

The PID XML is encrypted before being sent to the Authentication Server.

---

## 6. Why is PID XML encrypted?

PID XML contains sensitive resident information.

To protect this information during transmission, it must be encrypted before being included in the Authentication Request XML.

---

## 7. What is a Session Key?

A Session Key is a randomly generated encryption key used to encrypt the PID XML and HMAC.

A new Session Key must be generated for every authentication request.

---

## 8. What is HMAC?

HMAC (Hash-based Message Authentication Code) ensures the integrity of the PID XML.

It allows the Authentication Server to verify that the PID XML has not been modified.

---

## 9. Why is the Authentication Request XML digitally signed?

The Authentication Request XML is digitally signed to:

* Verify the identity of the sender.
* Ensure data integrity.
* Prevent unauthorized modification of the request.

---

## 10. Is the entire Authentication Request XML encrypted?

No.

Only sensitive information inside the request is encrypted, such as:

* PID XML
* Session Key
* HMAC

The Authentication Request XML itself is digitally signed.

---

## 11. Can I reuse the same Session Key?

No.

A new Session Key should be generated for every authentication request.

---

## 12. Can I reuse an OTP?

No.

Each OTP is valid for a limited time and is intended for a single authentication attempt.

If the OTP expires, generate a new OTP.

---

## 13. Can I store PID XML?

No.

PID XML contains sensitive authentication data and should not be stored.

It should exist only in memory until encryption is complete.

---

## 14. Can I store biometric data?

No.

Raw biometric data should not be stored unless specifically permitted by UIDAI guidelines and your organization's policies.

---

## 15. Can I log Authentication Request XML?

Avoid logging the complete Authentication Request XML.

If logging is required for troubleshooting, ensure that all sensitive information is removed or masked.

---

## 16. Which HTTP method should be used?

The Aadhaar Authentication API uses the **HTTP POST** method.

---

## 17. What Content-Type should be used?

Use the following HTTP headers:

```http id="faqhdr"
Content-Type: application/xml
Accept: application/xml
```

---

## 18. How do I know if authentication was successful?

Check the `ret` attribute in the Authentication Response XML.

* `ret="Y"` → Authentication Successful
* `ret="N"` → Authentication Failed

---

## 19. Where can I find the reason for authentication failure?

If authentication fails, check the `err` attribute in the Authentication Response XML.

Refer to the **Error Codes** section for a detailed explanation of each error code and the recommended resolution.

---

## 20. What should I do if authentication fails?

The appropriate action depends on the returned error code.

Examples:

* Generate a new OTP.
* Capture biometrics again.
* Verify the request XML.
* Verify encryption and digital signature.
* Correct invalid input data before retrying.

---

## 21. Should I rely only on the HTTP status code?

No.

An HTTP status code such as **200 OK** only indicates that the server processed the request.

Always parse the Authentication Response XML and verify the `ret` attribute.

---

## 22. Can I retry an authentication request?

Yes, but only when appropriate.

You may retry for:

* Temporary server issues
* Network timeouts
* Temporary service unavailability

Do **not** retry the same request if:

* The OTP has expired.
* The request has expired.
* The request XML is invalid.
* The Session Key is no longer valid.

Instead, generate a new authentication request.

---

## 23. What information should be logged?

Recommended:

* Transaction ID
* Request Timestamp
* Response Timestamp
* Authentication Result
* Error Code

Do **not** log:

* Aadhaar Number
* Virtual ID
* OTP
* Biometric Data
* PID XML
* Session Key
* HMAC
* Complete Authentication Request XML

---

## 24. Is HTTPS mandatory?

Yes.

All authentication requests must be sent over a secure HTTPS connection to protect sensitive information during transmission.

---

## 25. Can I use the same implementation for Sandbox and Production?

Yes.

The authentication workflow remains the same.

Typically, only the following differ:

* Endpoint URL
* Certificates
* License Keys
* Environment-specific credentials

---

## 26. What is a Transaction ID?

A Transaction ID is a unique identifier generated by your application for each authentication request.

It is used to correlate requests and responses and to assist with auditing and troubleshooting.

---

## 27. What happens if the digital signature is invalid?

The Authentication Server rejects the request.

Verify:

* The signing certificate.
* The private key.
* The XML Digital Signature implementation.
* That the XML was not modified after signing.

---

## 28. Can I modify the Authentication Request XML after signing?

No.

Any modification made after the XML has been digitally signed will invalidate the signature and cause the request to fail.

---

## 29. Is a Registered Device (RD) required?

A Registered Device is required for:

* Fingerprint Authentication
* Iris Authentication
* Face Authentication (where applicable)

It is **not** required for OTP Authentication.

---

## 30. Where can I get additional help?

If you experience issues during integration:

1. Verify your Authentication Request XML.
2. Validate the PID XML, encryption, HMAC, and digital signature.
3. Check the returned error code.
4. Verify your environment configuration (Sandbox or Production).
5. Contact your AUA/ASA administrator or integration support team if the issue persists.

---

## Still Have Questions?

If your question is not covered in this FAQ, review the relevant authentication guide, request/response examples, and error code documentation included in this documentation set before contacting support.
