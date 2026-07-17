---
sidebar_position: 2
---

# Step 2 - Generate PID XML

## Goal

After collecting all the required authentication inputs, generate the **PID (Personal Identity Data) XML**.

For **OTP Authentication**, the PID XML contains the **OTP entered by the resident**. This XML will later be encrypted and included in the Aadhaar Authentication Request XML.

---

## What is PID XML?

PID (Personal Identity Data) is the sensitive portion of the authentication request. Depending on the authentication type, it may contain:

* OTP
* Fingerprint
* Iris
* Face Authentication
* Demographic information (where applicable)

In this guide, we are using **OTP Authentication**, so the PID XML contains only the OTP.

---

## PID XML Structure

Create the PID XML as shown below.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Pid ts="2026-07-16T15:45:00" ver="2.0">
    <Pv otp="654321"/>
</Pid>
```

---

## XML Elements

### `<Pid>`

The root element of the PID block.

| Attribute | Description                                          |
| --------- | ---------------------------------------------------- |
| `ts`      | Timestamp when the PID XML is generated.             |
| `ver`     | PID XML version supported by the authentication API. |

---

### `<Pv>`

Contains the OTP provided by the resident.

| Attribute | Description                                |
| --------- | ------------------------------------------ |
| `otp`     | One-Time Password entered by the resident. |

---

## Example PID XML

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Pid ts="2026-07-16T15:45:00" ver="2.0">
    <Pv otp="654321"/>
</Pid>
```

---

## Validation Checklist

Before proceeding to encryption, verify the following:

* The XML is well-formed.
* The `Pid` element is present.
* The timestamp is current.
* The PID version is correct.
* The OTP has been included correctly.
* The OTP has not expired.
* The XML encoding is UTF-8.

---

## Workflow

```text
Resident Enters OTP
        │
        ▼
Generate PID XML
        │
        ▼
Validate XML Structure
        │
        ▼
PID XML Ready
```

---

## Security Considerations

The PID XML contains sensitive authentication data.

Before moving to the next step:

* Do not transmit the PID XML in plain text.
* Do not store the PID XML in a database.
* Do not write the PID XML to application logs.
* Keep the PID XML only in memory until it is encrypted.

---

## Output of this Step

At the end of this step, your application should have a valid **PID XML** containing the resident's OTP.

This PID XML will be encrypted in the next step before being included in the Aadhaar Authentication Request.

The next step is:

**Step 3 – Encrypt PID XML**

---

## Notes

> The PID XML is the most sensitive part of the authentication request.

> Any modification to the PID XML after encryption will invalidate the authentication request.

> Always generate a fresh PID XML for every authentication request. Never reuse a previously generated PID block.
