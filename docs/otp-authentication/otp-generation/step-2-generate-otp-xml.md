---
sidebar_position: 2
---

# Step 2 - Generate OTP Request XML

## Goal

After collecting all the required input parameters, the next step is to construct the **OTP Request XML**. This XML contains the request details that will be digitally signed and sent to the Aadhaar Authentication Server.

---

## XML Structure

The root element of the request is **`<Otp>`**.

A basic OTP Request XML is shown below.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Otp
    uid="999988887777"
    ac="public"
    sa="public123"
    lk="MKQ8R2XXXXXXXXXXXXXX"
    txn="OTP202607161530001"
    ts="2026-07-16T15:30:00"
    ver="2.5"
    ch="00">
</Otp>
```

---

## XML Attributes

| Attribute | Description                                                                                                          | Required |
| --------- | -------------------------------------------------------------------------------------------------------------------- | -------- |
| `uid`     | 12-digit Aadhaar Number of the resident. If VID is used, use the appropriate attribute as per the API specification. | Yes      |
| `ac`      | Authentication User Agency (AUA) Code issued by UIDAI.                                                               | Yes      |
| `sa`      | Sub-AUA Code assigned to your organization.                                                                          | Yes      |
| `lk`      | License Key provided by UIDAI.                                                                                       | Yes      |
| `txn`     | Unique transaction identifier for the request.                                                                       | Yes      |
| `ts`      | Current timestamp in ISO 8601 format.                                                                                | Yes      |
| `ver`     | Aadhaar OTP API version.                                                                                             | Yes      |
| `ch`      | OTP delivery channel.                                                                                                | Yes      |

---

## OTP Delivery Channel

The `ch` attribute specifies how the OTP should be delivered.

| Value | Description |
| ----- | ----------- |
| `00`  | SMS + Email |
| `01`  | SMS Only    |
| `02`  | Email Only  |

> Use only the values supported by your UIDAI integration and API version.

---

## XML Generation Checklist

Before moving to the next step, verify that:

* XML declaration is present.
* All mandatory attributes are included.
* No attribute contains a null or empty value.
* Transaction ID is unique.
* Timestamp is generated at the time of request.
* XML is well-formed.
* XML encoding is UTF-8.

---

## Example Generated XML

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Otp
    uid="999988887777"
    ac="public"
    sa="public123"
    lk="MKQ8R2XXXXXXXXXXXXXX"
    txn="OTP202607161530001"
    ts="2026-07-16T15:30:00"
    ver="2.5"
    ch="00">
</Otp>
```

---

## Output of this Step

At the end of this step, the application should have a valid **OTP Request XML** ready for digital signing.

The generated XML will be used in the next step:

**Step 3 – Digitally Sign XML**

---

## Common Mistakes

* Missing mandatory attributes.
* Invalid timestamp format.
* Duplicate transaction ID.
* Incorrect Aadhaar Number or VID.
* Invalid License Key.
* Incorrect API version.
* Using an unsupported OTP delivery channel.

---

## Notes

> Do not digitally sign the XML in this step.

> Do not send the request yet.

> Ensure that the generated XML exactly matches the UIDAI OTP Request schema before proceeding to digital signing.
