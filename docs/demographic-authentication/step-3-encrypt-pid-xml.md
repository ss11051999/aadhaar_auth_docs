---
sidebar_position: 3
---

# Step 3 - Encrypt PID XML

## Goal

After generating the PID XML, the next step is to **encrypt the PID XML** before it is transmitted to the Aadhaar Authentication Server.

The PID XML contains sensitive demographic information and **must never be sent in plain text**. Encrypting the PID XML ensures that only the Aadhaar Authentication Server can decrypt and process the resident's information.

---

## Why is PID XML Encrypted?

The PID XML contains confidential resident information such as:

* Name
* Date of Birth
* Gender
* Address
* Other demographic details

To protect this information during transmission, the PID XML is encrypted using a **randomly generated Session Key**.

The Session Key itself is then encrypted using the **UIDAI Public Certificate** and included in the Authentication Request XML.

---

## Encryption Workflow

```text id="demo-step3-flow"
Generate PID XML
        │
        ▼
Generate Random Session Key
        │
        ▼
Encrypt PID XML
        │
        ▼
Encrypt Session Key
        │
        ▼
Encrypted PID XML Ready
```

---

## Encryption Process

The encryption process consists of the following steps:

### 1. Generate a Session Key

Generate a new random Session Key.

> A new Session Key must be generated for **every authentication request**.

---

### 2. Encrypt the PID XML

Use the generated Session Key to encrypt the complete PID XML.

The output of this step is an encrypted PID block that will later be placed inside the `<Data>` element of the Authentication Request XML.

---

### 3. Encrypt the Session Key

Encrypt the Session Key using the **UIDAI Public Certificate**.

The encrypted Session Key will later be included inside the `<Skey>` element of the Authentication Request XML.

---

## Encryption Result

After encryption, your application should have:

* Encrypted PID XML
* Encrypted Session Key

These values are used in the subsequent authentication steps.

---

## Workflow Diagram

```text id="demo-step3-diagram"
               PID XML
                  │
                  ▼
      Generate Session Key
                  │
                  ▼
    Encrypt PID XML using
        Session Key
                  │
                  ▼
      Encrypted PID XML
                  │
                  ▼
 Encrypt Session Key using
 UIDAI Public Certificate
                  │
                  ▼
     Encrypted Session Key
```

---

## Validation Checklist

Before proceeding, verify that:

* A new Session Key has been generated.
* The PID XML has been encrypted successfully.
* The Session Key has been encrypted successfully.
* The UIDAI Public Certificate is valid.
* No plain-text PID XML is included in the request.

---

## Common Errors

| Error                     | Possible Cause                                  |
| ------------------------- | ----------------------------------------------- |
| PID encryption failed     | Invalid encryption implementation               |
| Invalid Session Key       | Session Key generation failed                   |
| Certificate not found     | UIDAI Public Certificate missing                |
| Invalid certificate       | Incorrect or expired certificate                |
| Encryption output invalid | Incorrect encryption algorithm or configuration |

---

## Best Practices

* Generate a new Session Key for every authentication request.
* Never reuse a Session Key.
* Always use the latest UIDAI Public Certificate.
* Never transmit PID XML in plain text.
* Do not store the Session Key after authentication is complete.
* Do not log the PID XML, Session Key, or encrypted authentication data.

---

## Output of this Step

At the end of this step, your application should have:

* Encrypted PID XML
* Encrypted Session Key

These encrypted values will be used in the next step to generate the **HMAC** for the authentication request.

---

## Next Step

Continue to **Step 4 – Generate HMAC**, where an HMAC is generated to ensure the integrity of the PID XML.

---

## Notes

> The encryption process is identical across **OTP**, **Biometric**, **Demographic**, and **Multi-Factor Authentication**. The only difference is the type of data contained within the PID XML.

> The Session Key should be generated for a single request only and must never be reused across multiple authentication requests.

> The UIDAI Public Certificate is used **only** to encrypt the Session Key. It is **not** used to encrypt the PID XML directly.
