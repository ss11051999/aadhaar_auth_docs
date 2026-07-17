---
sidebar_position: 5
---

# Step 5 - Create Authentication Request XML

## Goal

After generating the encrypted PID XML, encrypted Session Key, and encrypted HMAC, the next step is to create the **Authentication Request XML**.

The Authentication Request XML is the final request document that combines all the required authentication information into a single XML structure. This XML is then digitally signed before being transmitted to the Aadhaar Authentication Server.

At this stage, the XML has **not yet been digitally signed**. Digital signing is performed in the next step.

---

## What is Authentication Request XML?

The Authentication Request XML is the primary request document used by the Aadhaar Authentication API.

For Multi-Factor Authentication, it contains:

* Resident identifier (Aadhaar Number or Virtual ID)
* Authentication type
* Authentication factors being used
* Device metadata
* Encrypted Session Key
* Encrypted PID XML
* Encrypted HMAC

This XML is the complete authentication request that is sent to the Aadhaar Authentication Server after it is digitally signed.

---

## Authentication Request Structure

A typical Authentication Request XML consists of the following elements:

* `<Auth>` – Root element
* `<Uses>` – Specifies the authentication factors being used
* `<Meta>` – Device and request metadata
* `<Skey>` – Encrypted Session Key
* `<Data>` – Encrypted PID XML
* `<Hmac>` – Encrypted HMAC

---

## Example Authentication Request XML

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Auth
    uid="123412341234"
    tid=""
    ac="public"
    sa="public"
    ver="2.5"
    txn="MFA202607170001"
    lk="LicenseKey">

    <Uses
        otp="y"
        bio="y"
        demo="n"
        pin="n"/>

    <Meta
        dc=""
        dpId=""
        rdsId=""
        rdsVer=""
        mi=""/>

    <Skey ci="20260701">
        EncryptedSessionKey
    </Skey>

    <Data type="X">
        EncryptedPIDXML
    </Data>

    <Hmac>
        EncryptedHMAC
    </Hmac>

</Auth>
```

> The XML shown above is a simplified example intended to explain the overall structure. The actual values and attributes may vary depending on your authentication configuration and UIDAI specifications.

---

## Understanding the `<Uses>` Element

The `<Uses>` element specifies which authentication factors are included in the request.

For example:

| Authentication Factors   | `<Uses>` Configuration |
| ------------------------ | ---------------------- |
| OTP + Fingerprint        | `otp="y" bio="y"`      |
| OTP + Iris               | `otp="y" bio="y"`      |
| Fingerprint + Face       | `bio="y"`              |
| OTP + Fingerprint + Face | `otp="y" bio="y"`      |

The values in this element must accurately represent the authentication factors included in the PID XML.

---

## Authentication Request Workflow

```text
Encrypted Session Key
            │
            │
Encrypted PID XML
            │
            │
Encrypted HMAC
            │
            ▼
Create Authentication
Request XML
            │
            ▼
Authentication Request XML Ready
```

---

## Elements Included in the Request

| Element | Description                                                       |
| ------- | ----------------------------------------------------------------- |
| `Auth`  | Root element containing authentication details.                   |
| `Uses`  | Specifies the authentication factors used in the request.         |
| `Meta`  | Contains device and request metadata.                             |
| `Skey`  | Encrypted Session Key.                                            |
| `Data`  | Encrypted PID XML containing all selected authentication factors. |
| `Hmac`  | Encrypted HMAC generated from the PID XML.                        |

---

## Validation Checklist

Before proceeding, verify that:

* Aadhaar Number (UID) or Virtual ID (VID) is correct.
* The Transaction ID is unique.
* The API version is correct.
* The `<Uses>` element accurately reflects all authentication factors.
* The encrypted Session Key has been included.
* The encrypted PID XML has been included.
* The encrypted HMAC has been included.
* The XML is well-formed and complies with the required schema.

---

## Common Errors

| Error                          | Possible Cause                                  |
| ------------------------------ | ----------------------------------------------- |
| Invalid Authentication XML     | Incorrect XML structure                         |
| Invalid `<Uses>` configuration | Authentication factors do not match the PID XML |
| Missing `<Skey>`               | Encrypted Session Key not included              |
| Missing `<Data>`               | Encrypted PID XML not included                  |
| Missing `<Hmac>`               | Encrypted HMAC not included                     |
| Invalid Transaction ID         | Transaction ID missing or invalid               |
| Unsupported API version        | Incorrect API version configured                |

---

## Best Practices

* Create a new Authentication Request XML for every authentication request.
* Ensure the `<Uses>` element matches the authentication factors included in the PID XML.
* Use a unique Transaction ID.
* Validate the XML before signing.
* Never include plain-text PID XML in the request.
* Never modify the Authentication Request XML after it has been digitally signed.

---

## Output of this Step

At the end of this step, your application should have a complete **Authentication Request XML** containing:

* Authentication details
* Encrypted Session Key
* Encrypted PID XML
* Encrypted HMAC
* Multi-factor authentication configuration

The request is now ready to be digitally signed.

---

## Next Step

Continue to **Step 6 – Digitally Sign Authentication Request XML**, where the Authentication Request XML is digitally signed using your organization's signing certificate before being sent to the Aadhaar Authentication Server.

---

## Notes

> For Multi-Factor Authentication, the Authentication Request XML follows the same structure as other Aadhaar authentication methods. The key difference is that the `<Uses>` element enables multiple authentication factors, and the encrypted PID XML contains all selected authentication data.

> Ensure that the authentication factors specified in the `<Uses>` element exactly match the contents of the PID XML. Any mismatch may result in the authentication request being rejected by the Authentication Server.
