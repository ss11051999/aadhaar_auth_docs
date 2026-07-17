---
sidebar_position: 1
---

# Step 1 - Collect Input

## Goal

Before generating an Aadhaar OTP request, collect all the required information. This data will be used to construct the OTP Request XML.

---

## Required Inputs

| Field                                    | Required | Description                                                                                              |
| ---------------------------------------- | -------- | -------------------------------------------------------------------------------------------------------- |
| Aadhaar Number (UID) or Virtual ID (VID) | Yes      | The resident's 12-digit Aadhaar Number or 16-digit Virtual ID. Only one should be provided.              |
| Agency Code (AC)                         | Yes      | The Authentication User Agency (AUA) code assigned by UIDAI.                                             |
| Sub AUA Code (SA)                        | Yes      | The Sub-AUA code assigned to your organization.                                                          |
| License Key (LK)                         | Yes      | License key issued by UIDAI for request authentication.                                                  |
| Transaction ID (Txn)                     | Yes      | A unique transaction identifier for every OTP request. It should be unique across all requests.          |
| Timestamp (TS)                           | Yes      | Current request timestamp in the format specified by UIDAI (ISO 8601).                                   |
| OTP Channel                              | Optional | Specifies how the OTP should be delivered (SMS, Email, or both), depending on the supported API version. |
| Certificate                              | Yes      | Public certificate used for XML Digital Signature.                                                       |
| API Version                              | Yes      | Version of the Aadhaar OTP API being used.                                                               |

---

## Input Validation

Before generating the request, validate the following:

* Aadhaar Number must contain exactly **12 digits**, if used.
* Virtual ID must contain exactly **16 digits**, if used.
* Either **UID** or **VID** must be provided, but not both.
* Transaction ID should be unique for every request.
* Timestamp should represent the current system time.
* Agency Code, Sub-AUA Code, and License Key should match the values issued by UIDAI.
* Ensure the signing certificate is valid and has not expired.

---

## Example Input

| Parameter      | Example              |
| -------------- | -------------------- |
| UID            | 999988887777         |
| AC             | public               |
| SA             | public123            |
| LK             | MKQ8R2XXXXXXXXXXXXXX |
| Transaction ID | OTP202607161530001   |
| Timestamp      | 2026-07-16T15:30:00  |
| OTP Channel    | SMS                  |

---

## Output of this Step

After completing this step, the application should have all the required information to generate the **OTP Request XML**, which will be covered in the next step.

---

## Notes

> Every OTP request must use a **new Transaction ID**.

> Ensure the system clock is synchronized to avoid timestamp validation failures.

> Never hardcode production credentials directly in source code. Store them securely using environment variables or a secure secrets manager.
