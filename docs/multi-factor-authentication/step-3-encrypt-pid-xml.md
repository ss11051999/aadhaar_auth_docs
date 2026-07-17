---
sidebar_position: 3
---

# Step 3 - Encrypt PID XML

## Goal

After generating the PID XML, the next step is to **encrypt the PID XML** before it is transmitted to the Aadhaar Authentication Server.

The PID XML contains multiple authentication factors, such as OTP, fingerprint, iris, or face data, which are highly sensitive. To protect this information during transmission, the PID XML must be encrypted.

The encrypted PID XML is later included in the Authentication Request XML.

---

## Why is PID XML Encrypted?

The PID XML contains confidential resident authentication data, including:

* OTP (if used)
* Fingerprint data
* Iris data
* Face data
* Other authentication information

Encrypting the PID XML ensures that:

* Authentication data remains confidential.
* Only the Aadhaar Authentication Server can decrypt and process the information.
* Sensitive resident data is protected while in transit.

---

## Encryption Workflow

```text id="mfa-step3-flow"
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

The encryption process consists of the following steps.

### 1. Generate a Session Key

Generate a new random Session Key for the current authentication request.

> A new Session Key must be generated for **every** authentication request.

---

### 2. Encrypt the PID XML

Use the generated Session Key to encrypt the complete PID XML.

The encrypted output is later included in the `<Data>` element of the Authentication Request XML.

---

### 3. Encrypt the Session Key

Encrypt the Session Key using the **UIDAI Public Certificate**.

The encrypted Session Key is later placed inside the `<Skey>` element of the Authentication Request XML.

---

## Encryption Result

After encryption, your application should have:

* Encrypted PID XML
* Encrypted Session Key

These encrypted values are required for generating the Authentication Request XML.

---

## Workflow Diagram

```text id="mfa-step3-diagram"
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
* The encrypted PID XML is ready for inclusion in the Authentication Request XML.
* No plain-text PID XML is included in the request.

---

## Common Errors

| Error                         | Possible Cause                              |
| ----------------------------- | ------------------------------------------- |
| PID XML encryption failed     | Encryption process failed                   |
| Invalid Session Key           | Session Key generation failed               |
| Session Key encryption failed | Incorrect certificate or encryption process |
| Certificate not found         | UIDAI Public Certificate missing            |
| Invalid certificate           | Expired or incorrect public certificate     |
| Invalid encrypted data        | Incorrect encryption implementation         |

---

## Best Practices

* Generate a new Session Key for every authentication request.
* Never reuse a Session Key across multiple requests.
* Always use the latest UIDAI Public Certificate.
* Never transmit PID XML in plain text.
* Do not store the Session Key after authentication is complete.
* Never log PID XML, OTPs, biometric data, face images, Session Keys, or encrypted authentication data.

---

## Output of this Step

At the end of this step, your application should have:

* Encrypted PID XML
* Encrypted Session Key

These encrypted values will be used in the next step to generate the **HMAC**.

---

## Next Step

Continue to **Step 4 – Generate HMAC**, where an HMAC is generated from the original PID XML and encrypted using the same Session Key to ensure the integrity of the authentication data.

---

## Notes

> The encryption process for Multi-Factor Authentication is identical to OTP, Biometric, Demographic, Face, and eKYC Authentication. The only difference is that the PID XML contains multiple authentication factors instead of a single factor.

> The Session Key is used only for the current authentication request and should never be reused.

> The UIDAI Public Certificate is used to encrypt the Session Key, while the Session Key is used to encrypt the PID XML.
