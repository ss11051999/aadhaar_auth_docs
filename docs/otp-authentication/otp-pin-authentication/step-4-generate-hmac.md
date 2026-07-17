---
sidebar_position: 4
---

# Step 4 - Generate HMAC

## Goal

After encrypting the PID XML, generate the **HMAC (Hash-based Message Authentication Code)** for the PID data.

The HMAC is used to verify the integrity of the PID XML. It allows the Aadhaar Authentication Server to detect whether the PID data has been modified before or during transmission.

---

## Why Generate an HMAC?

The HMAC provides **data integrity**.

It helps ensure that:

* The PID XML has not been tampered with.
* The authentication data remains unchanged.
* The server can verify the integrity of the PID block.

> **Note:** The HMAC does **not** encrypt the PID XML. Encryption and HMAC serve different purposes.

---

## HMAC Generation Workflow

```text
PID XML
    │
    ▼
Generate SHA-256 Hash
    │
    ▼
Encrypt Hash using Session Key
    │
    ▼
Base64 Encode
    │
    ▼
HMAC Value
```

---

## Prerequisites

Before generating the HMAC, ensure that:

* The PID XML has been generated.
* The PID XML has been encrypted.
* The session key used for PID encryption is available.
* The original (plain) PID XML is still available in memory for hashing.

---

## Input

The input to this step is the **plain PID XML** generated in **Step 2**.

Example:

```xml
<Pid ts="2026-07-16T15:45:00" ver="2.0">
    <Pv otp="654321"/>
</Pid>
```

---

## HMAC Generation Process

The HMAC is generated using the following sequence:

1. Generate the SHA-256 hash of the plain PID XML.
2. Encrypt the hash using the same session key that was used to encrypt the PID XML.
3. Base64 encode the encrypted hash.
4. Store the generated value for inclusion in the Authentication Request XML.

---

## Example HMAC Value

Example:

```text
lU5w3u7W2Pqv7F5vXo4jP8mL9zQe1cAbYt2WxRkM9Nc=
```

> The above value is only an example and does not represent a real HMAC.

---

## Validation Checklist

Before continuing, verify that:

* The SHA-256 hash was generated successfully.
* The correct session key was used.
* The HMAC value is not empty.
* The output has been Base64 encoded (if required by your implementation).
* The HMAC corresponds to the same PID XML that was encrypted in Step 3.

---

## Common Errors

| Error                  | Possible Cause                              |
| ---------------------- | ------------------------------------------- |
| Empty HMAC             | PID XML was empty or hash generation failed |
| Invalid HMAC           | Incorrect session key used                  |
| Authentication failed  | HMAC does not match the encrypted PID block |
| Hash generation failed | Invalid input data or implementation issue  |

---

## Security Best Practices

* Generate the HMAC from the **original PID XML**, not from the encrypted PID block.
* Use the **same session key** that was used for PID encryption.
* Never modify the PID XML after generating the HMAC.
* Do not log the generated HMAC in production systems unless required for secure debugging.

---

## Output of this Step

At the end of this step, your application should have:

* ✅ Encrypted PID block
* ✅ Generated HMAC
* ✅ Session key (to be encrypted in a later step)

These values will be used while constructing the final Aadhaar Authentication Request XML.

The next step is:

**Step 5 – Create Authentication Request XML**

---

## Notes

> The HMAC protects the integrity of the PID XML, while encryption protects its confidentiality.

> Any change to the PID XML after generating the HMAC will result in authentication failure.

> Ensure that the HMAC, encrypted PID block, and session key all belong to the same authentication request.
