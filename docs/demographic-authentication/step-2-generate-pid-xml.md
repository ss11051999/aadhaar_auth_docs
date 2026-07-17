---
sidebar_position: 2
---

# Step 2 - Generate PID XML

## Goal

After collecting all the required demographic information, the next step is to generate the **PID (Personal Identity Data) XML**.

The PID XML contains the resident's demographic information in a structured XML format. This XML is later encrypted and included in the Authentication Request.

Unlike OTP Authentication, where the PID XML contains an OTP, or Biometric Authentication, where it contains biometric data, Demographic Authentication includes the resident's demographic details.

---

## What is PID XML?

PID (Personal Identity Data) XML is a standardized XML document used to carry the authentication information securely.

For Demographic Authentication, the PID XML contains the demographic details that need to be verified against the Aadhaar database.

The generated PID XML is **not sent directly** to the Authentication Server. It is first encrypted and then embedded inside the Authentication Request XML.

---

## Information Included in PID XML

Depending on your authentication requirements, the PID XML may contain:

* Name
* Date of Birth
* Age
* Gender
* Address
* Phone Number
* Email Address

Only include the demographic fields required for your authentication use case.

---

## PID XML Structure

A typical PID XML consists of:

* Root `<Pid>` element
* Timestamp
* Version
* Demographic (`Demo`) element
* Resident demographic information

---

## Example PID XML

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Pid ts="2026-07-17T11:30:00" ver="2.0">

    <Demo>

        <Pi
            name="Rahul Sharma"
            ms="E"/>

        <Pa
            house="12"
            street="MG Road"
            loc="Civil Lines"
            vtc="Raipur"
            dist="Raipur"
            state="CG"
            pc="492001"
            ms="E"/>

        <Pfa
            value="House No. 12, MG Road, Civil Lines, Raipur"
            ms="E"/>

    </Demo>

</Pid>
```

> The XML shown above is a sample for understanding the structure. The actual elements and attributes may vary depending on the authentication requirements and UIDAI specifications.

---

## Workflow

```text
Collect Demographic Information
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
* All mandatory demographic fields are present.
* The timestamp is generated correctly.
* The PID XML version is correct.
* The XML follows the required schema.
* No unwanted or empty elements are included.

---

## Common Errors

| Error                           | Possible Cause                |
| ------------------------------- | ----------------------------- |
| Invalid PID XML                 | Incorrect XML structure       |
| Missing demographic information | Required fields not provided  |
| Invalid timestamp               | Incorrect system date or time |
| Unsupported PID version         | Incorrect XML version         |
| Invalid XML format              | XML syntax error              |

---

## Best Practices

* Generate a fresh PID XML for every authentication request.
* Include only the demographic fields required for authentication.
* Validate the XML before proceeding.
* Ensure that the timestamp is current.
* Avoid storing the generated PID XML after the authentication process is complete.
* Keep resident demographic information confidential.

---

## Output of this Step

At the end of this step, your application should have a valid **PID XML** containing the resident's demographic information.

The generated PID XML is now ready for the next step:

**Step 3 – Encrypt PID XML**

---

## Notes

> The PID XML contains sensitive resident information and must **never** be transmitted in plain text.

> Before being included in the Authentication Request XML, the PID XML must be encrypted using a newly generated Session Key.

> The structure of the PID XML remains similar across different authentication methods. The primary difference is the type of authentication data included within the XML.
