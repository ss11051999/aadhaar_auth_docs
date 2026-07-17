---
sidebar_position: 3
---

# Step 3 - Encrypt PID XML

## Goal

The PID XML contains sensitive resident information (in this case, the OTP). Before it can be included in the Aadhaar Authentication Request, it **must be encrypted**.

The encrypted PID block ensures that the OTP remains confidential while it is transmitted to the Aadhaar Authentication Server.

---

## Why Encrypt the PID XML?

The PID XML contains sensitive authentication data and must never be transmitted in plain text.

Encrypting the PID XML provides:

* Confidentiality of the resident's OTP.
* Protection against unauthorized access during transmission.
* Compliance with the Aadhaar Authentication specifications.

---

## Encryption Workflow

```text
Generate PID XML
        │
        ▼
Generate Random Session Key
        │
        ▼
Encrypt PID XML using Session Key
        │
        ▼
Encrypted PID Block
```

---

## Prerequisites

Before encrypting the PID XML, ensure that:

* A valid PID XML has been generated.
* A new session key has been generated for the request.
* The encryption algorithm and key size comply with the Aadhaar Authentication specifications.

---

## Input

The input to this step is the PID XML generated in the previous step.

Example:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Pid ts="2026-07-16T15:45:00" ver="2.0">
    <Pv otp="654321"/>
</Pid>
```

---

## Encryption Process

The encryption process is as follows:

```text
PID XML
    │
    ▼
AES Encryption
    │
    ▼
Encrypted Binary Data
    │
    ▼
Base64 Encode
    │
    ▼
Encrypted PID Block
```

---

## Example Encrypted PID

The encrypted output is binary data and is generally Base64 encoded before being placed inside the Authentication Request XML.

Example:

```text
eJwQK4Fh7Q1mP2sQz1vA5Kx7X0K8qj3n...
```

> The encrypted value shown above is for illustration only.

---

## Validation Checklist

Before proceeding, verify that:

* The PID XML was encrypted successfully.
* A new session key was used.
* The encrypted output is not empty.
* The encrypted PID has been Base64 encoded (if required by your implementation).
* The original PID XML is no longer needed.

---

## Security Best Practices

* Generate a **new session key for every authentication request**.
* Never reuse a session key.
* Never store the session key in logs or databases.
* Never transmit the PID XML without encryption.
* Remove the plain PID XML from memory once encryption is complete, where practical.

---

## Common Errors

| Error                           | Possible Cause                                                  |
| ------------------------------- | --------------------------------------------------------------- |
| Encryption failed               | Invalid encryption configuration                                |
| Empty encrypted output          | PID XML was empty or encryption process failed                  |
| Invalid session key             | Incorrect or improperly generated session key                   |
| Authentication request rejected | PID block not encrypted according to the required specification |

---

## Output of this Step

At the end of this step, your application should have:

* An encrypted PID block.
* The session key used for encryption (to be encrypted in a later step).
* The original PID XML should no longer be used.

The encrypted PID block will be included in the Aadhaar Authentication Request XML after the remaining security components are generated.

The next step is:

**Step 4 – Generate HMAC**

---

## Notes

> The encrypted PID block protects the resident's sensitive authentication data during transmission.

> Every authentication request must use a freshly generated session key and a newly encrypted PID block.

> Never include the plain PID XML in the final Authentication Request XML.
