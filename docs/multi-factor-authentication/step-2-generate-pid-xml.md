---
sidebar_position: 2
---

# Step 2 - Generate PID XML

## Goal

After collecting all the required authentication factors, the next step is to generate the **PID (Personal Identity Data) XML**.

For Multi-Factor Authentication, the PID XML contains **all selected authentication factors** within a single XML document. These factors are later encrypted and securely transmitted to the Aadhaar Authentication Server.

The generated PID XML is not sent directly. It is encrypted before being included in the Authentication Request XML.

---

## What is PID XML?

The PID (Personal Identity Data) XML is a standardized XML document that carries the authentication data for Aadhaar Authentication.

In Multi-Factor Authentication, the PID XML may contain a combination of multiple authentication factors, such as:

* OTP
* Fingerprint
* Iris
* Face

The Authentication Server uses these authentication factors together to verify the resident's identity.

---

## Authentication Factors Included

Depending on your authentication requirements, the PID XML may include one or more of the following:

| Authentication Factor | Description                                                |
| --------------------- | ---------------------------------------------------------- |
| OTP                   | One-Time Password received by the resident                 |
| Fingerprint           | Fingerprint data captured from a Registered Device (RD)    |
| Iris                  | Iris image captured from a Registered Device (RD)          |
| Face                  | Face image captured using a supported device or RD Service |

> Include only the authentication factors required for your use case.

---

## PID XML Structure

A typical PID XML consists of:

* Root `<Pid>` element
* Timestamp
* Version
* OTP element (if applicable)
* Biometric element (Fingerprint/Iris)
* Face element (if applicable)

The exact structure depends on the selected authentication factors.

---

## Example PID XML (OTP + Fingerprint)

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Pid ts="2026-07-17T14:30:00" ver="2.0">

    <Pv otp="583921"/>

    <Bios>
        <Bio type="FMR">
            BASE64_ENCODED_FINGERPRINT_DATA
        </Bio>
    </Bios>

</Pid>
```

---

## Example PID XML (OTP + Fingerprint + Face)

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Pid ts="2026-07-17T14:30:00" ver="2.0">

    <Pv otp="583921"/>

    <Bios>
        <Bio type="FMR">
            BASE64_ENCODED_FINGERPRINT_DATA
        </Bio>
    </Bios>

    <Face>
        BASE64_ENCODED_FACE_IMAGE
    </Face>

</Pid>
```

> The XML examples above are simplified for understanding the structure. The actual XML elements and attributes may vary depending on the authentication method, RD Service output, and UIDAI specifications.

---

## Workflow

```text
Collect Authentication Factors
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

## Validation Checklist

Before proceeding, verify that:

* The PID XML is well-formed.
* All required authentication factors are included.
* The timestamp is correct.
* The PID XML version is correct.
* The XML complies with the required schema.
* No empty or invalid authentication elements are present.

---

## Common Errors

| Error                   | Possible Cause                        |
| ----------------------- | ------------------------------------- |
| Invalid PID XML         | Incorrect XML structure               |
| Missing OTP             | OTP not included when required        |
| Missing biometric data  | Fingerprint or iris data not captured |
| Missing face data       | Face image not included when required |
| Invalid timestamp       | Incorrect system date or time         |
| Unsupported PID version | Incorrect XML version                 |

---

## Best Practices

* Generate a new PID XML for every authentication request.
* Include only the authentication factors required for the selected authentication method.
* Validate the PID XML before proceeding.
* Ensure the timestamp is accurate.
* Do not store PID XML after the authentication process is complete.
* Never expose or log OTPs, biometric data, or face images.

---

## Output of this Step

At the end of this step, your application should have a valid **PID XML** containing all the selected authentication factors.

The generated PID XML is now ready for encryption.

---

## Next Step

Continue to **Step 3 – Encrypt PID XML**, where the generated PID XML is encrypted using a newly generated Session Key before being included in the Authentication Request XML.

---

## Notes

> Multi-Factor Authentication combines multiple authentication factors into a **single PID XML**. The Authentication Server evaluates all included factors during the authentication process.

> The PID XML contains highly sensitive resident information and must **never** be transmitted or stored in plain text.

> The PID XML generation process is similar to other authentication methods. The primary difference is that it includes multiple authentication factors instead of a single factor.

