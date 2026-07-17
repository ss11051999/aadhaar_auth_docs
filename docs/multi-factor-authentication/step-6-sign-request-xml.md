---
sidebar_position: 6
---

# Step 6 - Digitally Sign Authentication Request XML

## Goal

After creating the Authentication Request XML, the next step is to **digitally sign the XML** before sending it to the Aadhaar Authentication Server.

A digital signature verifies the identity of the requesting organization and ensures that the Authentication Request XML has not been modified after it was created.

Once the XML is digitally signed, it is ready to be transmitted securely to the Authentication Server.

---

## Why is Digital Signature Required?

Digital signing provides three important security features:

* **Authentication** – Confirms that the request originated from a trusted organization.
* **Integrity** – Ensures that the Authentication Request XML has not been altered during transmission.
* **Non-Repudiation** – Prevents the sender from denying that the request was created and sent.

If the digital signature is invalid or missing, the Authentication Server will reject the request.

---

## Digital Signing Process

The Authentication Request XML is digitally signed using:

* Organization Signing Certificate
* Associated Private Key

The signing process generates an XML Digital Signature (`<Signature>` element), which becomes part of the Authentication Request XML.

> The private key must always remain secure and should never be shared or exposed.

---

## Digital Signing Workflow

```text id="mfa-step6-flow"
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

```xml id="mfa-sign-example"
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

> The `<Signature>` element is generated automatically by the XML Digital Signature implementation. It should never be created or modified manually.

---

## Validation Checklist

Before proceeding, verify that:

* The Authentication Request XML has been generated successfully.
* The correct signing certificate is being used.
* The corresponding private key is available.
* The XML has been digitally signed successfully.
* The `<Signature>` element is present.
* The XML has not been modified after it was signed.

---

## Common Errors

| Error                         | Possible Cause                                 |
| ----------------------------- | ---------------------------------------------- |
| Invalid Digital Signature     | Incorrect signing process or invalid signature |
| Missing Signature             | Authentication Request XML was not signed      |
| Certificate Expired           | Signing certificate is no longer valid         |
| Invalid Private Key           | Incorrect or mismatched private key            |
| Signature Verification Failed | XML was modified after signing                 |
| Certificate Not Trusted       | Invalid or unrecognized signing certificate    |

---

## Best Practices

* Digitally sign every Authentication Request XML before sending it.
* Protect the private key using secure storage.
* Use only valid and trusted signing certificates.
* Never modify the XML after it has been digitally signed.
* Verify the digital signature during testing before integrating with the Authentication Server.
* Renew or rotate certificates before they expire.
* Restrict access to signing certificates and private keys.

---

## Output of this Step

At the end of this step, your application should have:

* A complete Authentication Request XML.
* A valid XML Digital Signature.
* A signed Authentication Request XML ready for transmission.

This signed XML is now ready to be sent to the Aadhaar Authentication Server.

---

## Next Step

Continue to **Step 7 – Send Authentication Request**, where the signed Authentication Request XML is transmitted securely to the Aadhaar Authentication Server over an HTTPS connection.

---

## Notes

> Digital signing is mandatory for **all Aadhaar Authentication methods**, including **OTP**, **Biometric**, **Demographic**, **Face**, **eKYC**, and **Multi-Factor Authentication**.

> The digital signature protects the entire Authentication Request XML, including the encrypted PID XML, encrypted Session Key, and encrypted HMAC.

> Any modification to the Authentication Request XML after it has been digitally signed will invalidate the signature, and the Authentication Server will reject the request.
