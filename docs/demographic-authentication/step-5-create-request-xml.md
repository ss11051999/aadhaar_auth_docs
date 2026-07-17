---
sidebar_position: 5
---

# Step 5 - Create Authentication Request XML

## Goal

After generating the encrypted PID XML, encrypted Session Key, and encrypted HMAC, the next step is to create the **Authentication Request XML**.

The Authentication Request XML is the final request document that is sent to the Aadhaar Authentication Server. It combines all the information required for demographic authentication into a single XML structure.

At this stage, the XML is **not yet digitally signed**. Digital signing is performed in the next step.

---

## What is Authentication Request XML?

The Authentication Request XML is the primary request document used by the Aadhaar Authentication API.

It contains:

* Authentication request information
* Resident identifier (Aadhaar Number or Virtual ID)
* Authentication type
* Metadata
* Encrypted Session Key
* Encrypted PID XML
* Encrypted HMAC

Once generated, this XML is digitally signed before being transmitted to the Authentication Server.

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

```xml id="demo-auth-xml"
<?xml version="1.0" encoding="UTF-8"?>

<Auth
    uid="123412341234"
    tid=""
    ac="public"
    sa="public"
    ver="2.5"
    txn="DEMO202607170001"
    lk="LicenseKey">

    <Uses
        otp="n"
        bio="n"
        demo="y"
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

> The XML shown above is a simplified example intended to explain the structure. Actual attribute values will depend on your environment, API version, and authentication configuration.

---

## Authentication Request Workflow

```text id="demo-step5-flow"
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

| Element | Description                                            |
| ------- | ------------------------------------------------------ |
| `Auth`  | Root element containing authentication details.        |
| `Uses`  | Specifies which authentication factors are being used. |
| `Meta`  | Contains device and request metadata.                  |
| `Skey`  | Encrypted Session Key.                                 |
| `Data`  | Encrypted PID XML.                                     |
| `Hmac`  | Encrypted HMAC used for integrity verification.        |

---

## Validation Checklist

Before proceeding, verify that:

* The Aadhaar Number (UID) or Virtual ID (VID) is correct.
* The Transaction ID is unique.
* The API version is correct.
* The `<Uses>` element accurately indicates Demographic Authentication.
* The encrypted Session Key has been added.
* The encrypted PID XML has been added.
* The encrypted HMAC has been added.
* The XML is well-formed and complies with the required schema.

---

## Common Errors

| Error                      | Possible Cause                     |
| -------------------------- | ---------------------------------- |
| Invalid Authentication XML | Incorrect XML structure            |
| Missing `<Uses>` element   | Authentication type not specified  |
| Missing `<Skey>`           | Encrypted Session Key not included |
| Missing `<Data>`           | Encrypted PID XML not included     |
| Missing `<Hmac>`           | Encrypted HMAC not included        |
| Invalid Transaction ID     | Transaction ID missing or invalid  |
| Unsupported API version    | Incorrect API version configured   |

---

## Best Practices

* Create a new Authentication Request XML for every authentication request.
* Use a unique Transaction ID.
* Ensure the `<Uses>` element correctly represents the selected authentication method.
* Validate the XML before signing.
* Never include plain-text PID XML inside the request.
* Never modify the Authentication Request XML after it has been digitally signed.

---

## Output of this Step

At the end of this step, your application should have a complete **Authentication Request XML** containing:

* Authentication details
* Encrypted Session Key
* Encrypted PID XML
* Encrypted HMAC

The request is now ready for digital signing.

---

## Next Step

Continue to **Step 6 – Digitally Sign Authentication Request XML**, where the Authentication Request XML is digitally signed using your organization's signing certificate before being sent to the Aadhaar Authentication Server.

---

## Notes

> The overall structure of the Authentication Request XML is the same across all authentication methods. The primary difference lies in the values specified in the `<Uses>` element and the contents of the encrypted PID XML.

> Ensure that the Authentication Request XML is validated before signing, as any modifications after signing will invalidate the digital signature.
