---
sidebar_position: 1
---

# Step 1 - Collect Input

## Goal

Before performing Aadhaar OTP Authentication, collect all the required information from the resident and your application. This information will be used to generate the **Authentication Request XML**.

Unlike **OTP Generation**, this step also requires the **OTP received by the resident**, as it will be included in the authentication request.

---

## Prerequisites

Before starting the authentication process, ensure that:

* The resident has successfully requested an OTP.
* The resident has received the OTP on the registered mobile number or email.
* The OTP is still valid and has not expired.
* The OTP has not already been used.
* Your application has all the credentials issued by UIDAI.

---

## Required Inputs

| Field                                    | Required | Description                                                                             |
| ---------------------------------------- | -------- | --------------------------------------------------------------------------------------- |
| Aadhaar Number (UID) or Virtual ID (VID) | Yes      | The resident's 12-digit Aadhaar Number or 16-digit Virtual ID. Only one should be used. |
| OTP                                      | Yes      | The One-Time Password entered by the resident.                                          |
| Agency Code (AC)                         | Yes      | Authentication User Agency (AUA) Code issued by UIDAI.                                  |
| Sub-AUA Code (SA)                        | Yes      | Sub-AUA code assigned to your organization.                                             |
| License Key (LK)                         | Yes      | License key issued by UIDAI.                                                            |
| Transaction ID (Txn)                     | Yes      | Unique transaction identifier. It should be unique for every authentication request.    |
| Timestamp (TS)                           | Yes      | Current timestamp in the format specified by UIDAI.                                     |
| API Version                              | Yes      | Aadhaar Authentication API version.                                                     |
| Signing Certificate                      | Yes      | Certificate used for digitally signing the request.                                     |

---

## Input Validation

Before proceeding, validate the following:

* Aadhaar Number must contain exactly **12 digits**, if used.
* Virtual ID must contain exactly **16 digits**, if used.
* Either **UID** or **VID** should be provided, but not both.
* OTP should not be empty.
* OTP should be within its validity period.
* Transaction ID must be unique.
* Timestamp should represent the current system time.
* Agency Code, Sub-AUA Code, and License Key should be valid.
* The signing certificate should be valid and not expired.

---

## Example Input

| Parameter      | Example              |
| -------------- | -------------------- |
| UID            | 999988887777         |
| OTP            | 654321               |
| AC             | public               |
| SA             | public123            |
| LK             | MKQ8R2XXXXXXXXXXXXXX |
| Transaction ID | AUTH202607161545001  |
| Timestamp      | 2026-07-16T15:45:00  |
| Version        | 2.5                  |

---

## Authentication Workflow

```text
Resident Requests OTP
          │
          ▼
Resident Receives OTP
          │
          ▼
Resident Enters OTP
          │
          ▼
Collect Authentication Inputs
          │
          ▼
Generate PID XML
```

---

## Output of this Step

After completing this step, your application should have all the required information to generate the **PID XML** for Aadhaar OTP Authentication.

The next step is:

**Step 2 – Generate PID XML**

---

## Notes

> Ensure the OTP is collected securely and never displayed or logged after submission.

> Every authentication request should use a new Transaction ID.

> Do not cache or permanently store the OTP. It is intended for one-time use only.
