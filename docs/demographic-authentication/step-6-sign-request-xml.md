---
sidebar_position: 6
---

# Step 6 - Digitally Sign Authentication Request XML

## Goal

After creating the Authentication Request XML, the next step is to **digitally sign the XML** before sending it to the Aadhaar Authentication Server.

A digital signature verifies the identity of the sender and ensures that the request has not been modified after it was created.

The signed Authentication Request XML is the final request that is transmitted to the Authentication Server.

---

## Why is Digital Signature Required?

The Aadhaar Authentication Server uses the digital signature to verify:

* The authenticity of the requesting organization.
* That the Authentication Request XML has not been modified during transmission.
* That the request originated from a trusted source.

If the digital signature is invalid or missing, the Authentication Server rejects the request.

---

## Digital Signing Process

The Authentication Request XML is signed using:

* Organization Signing Certificate
* Associated Private Key

The resulting XML contains an XML Digital Signature (`<Signature>`) element.

> The private key should always remain secure and must never be shared.

---

## Digital Signing Workflow

```text id="demo-step6-flow"
Authentication Request XML
            │
            ▼
Load Signing Certificate
            │
            ▼
Load Private Key
            │
            ▼
Generate XML Digital Signature
            │
            ▼
Attach <Signature> Element
            │
            ▼
Signed Authentication Request XML
```

---

## Example (Simplified)

```xml id="demo-sign-example"
<Auth>
    ...
    <Uses />
    <Meta />
    <Skey />
    <Data />
    <Hmac />

    <Signature>
        ...
    </Signature>

</Auth>
```

> The `<Signature>` element is generated automatically by your XML Digital Signature implementation. Do not create or modify it manually.

---

## Validation Checklist

Before proceeding, verify that:

* The Authentication Request XML is complete.
* The correct signing certificate is being used.
* The correct private key is available.
* The XML has been signed successfully.
* The `<Signature>` element is present.
* The XML has **not** been modified after signing.

---

## Common Errors

| Error                         | Possible Cause                      |
| ----------------------------- | ----------------------------------- |
| Invalid Digital Signature     | Incorrect signing process           |
| Missing Signature             | XML was not signed                  |
| Certificate Expired           | Signing certificate has expired     |
| Invalid Private Key           | Incorrect or mismatched private key |
| Signature Verification Failed | XML was modified after signing      |

---

## Best Practices

* Sign every Authentication Request XML before sending it.
* Protect the private key from unauthorized access.
* Use only valid and trusted signing certificates.
* Never edit the XML after it has been signed.
* Verify the digital signature during testing before integrating with the Authentication Server.
* Rotate certificates before they expire.

---

## Output of this Step

At the end of this step, your application should have:

* A complete Authentication Request XML.
* A valid XML Digital Signature.
* A signed Authentication Request XML ready for transmission.

---

## Next Step

Continue to **Step 7 – Send Authentication Request**, where the signed Authentication Request XML is sent to the Aadhaar Authentication Server over a secure HTTPS connection.

---

## Notes

> Digital signing is mandatory for all Aadhaar Authentication requests, regardless of the authentication method (OTP, Biometric, Demographic, Face, eKYC, or Multi-Factor).

> The digital signature ensures authenticity, integrity, and non-repudiation of the Authentication Request XML.

> Any modification to the XML after it has been digitally signed will invalidate the signature, and the Authentication Server will reject the request.
