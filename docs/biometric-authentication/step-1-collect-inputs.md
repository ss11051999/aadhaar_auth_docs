---
sidebar_position: 1
---

# Step 1 - Collect Input

## Goal

Before performing Aadhaar Biometric Authentication, collect all the required information from the resident, the biometric capture device, and your application.

The collected information will be used to generate the **PID XML**, which contains the resident's biometric data and is later encrypted before being sent for authentication.

---

## What is Biometric Authentication?

Biometric Authentication verifies a resident's identity using one or more biometric factors, such as:

* Fingerprint
* Iris
* Face Authentication (if supported)
* Fingerprint + Iris (Multi-factor Authentication)

Unlike OTP Authentication, the resident does **not** enter an OTP. Instead, biometric data is captured using a certified biometric device.

---

## Prerequisites

Before starting the authentication process, ensure that:

* The biometric device is connected and recognized by the system.
* The biometric device is registered and certified as required.
* The device is functioning correctly.
* The resident is available for biometric capture.
* Your application has valid Aadhaar Authentication credentials.
* The signing certificate is available.

---

## Required Inputs

| Field                                    | Required | Description                                                                             |
| ---------------------------------------- | -------- | --------------------------------------------------------------------------------------- |
| Aadhaar Number (UID) or Virtual ID (VID) | Yes      | The resident's 12-digit Aadhaar Number or 16-digit Virtual ID. Only one should be used. |
| Authentication Type                      | Yes      | Specifies the biometric authentication type (Fingerprint, Iris, Face, or Multi-factor). |
| Biometric Data                           | Yes      | Biometric information captured from the certified device.                               |
| Agency Code (AC)                         | Yes      | Authentication User Agency (AUA) Code issued by UIDAI.                                  |
| Sub-AUA Code (SA)                        | Yes      | Sub-AUA code assigned to your organization.                                             |
| License Key (LK)                         | Yes      | License Key issued by UIDAI.                                                            |
| Transaction ID (Txn)                     | Yes      | Unique identifier for the authentication request.                                       |
| Timestamp (TS)                           | Yes      | Current system timestamp.                                                               |
| API Version                              | Yes      | Aadhaar Authentication API version.                                                     |
| Signing Certificate                      | Yes      | Certificate used to digitally sign the Authentication Request XML.                      |

---

## Device Information

The biometric device generally provides additional information that may be included in the authentication request.

Examples include:

| Information         | Description                                                       |
| ------------------- | ----------------------------------------------------------------- |
| Device ID           | Unique identifier of the biometric device.                        |
| Device Model        | Model name or number.                                             |
| Device Manufacturer | Device manufacturer details.                                      |
| Device Certificate  | Certificate associated with the biometric device (if applicable). |
| Capture Timestamp   | Time at which the biometric sample was captured.                  |

> The exact information depends on the biometric device and your RD Service implementation.

---

## Input Validation

Before proceeding, validate the following:

* Aadhaar Number contains exactly **12 digits**, if used.
* Virtual ID contains exactly **16 digits**, if used.
* Either UID or VID is provided, but not both.
* Biometric data has been captured successfully.
* The captured biometric data is not empty.
* The biometric device is functioning correctly.
* Transaction ID is unique.
* Timestamp is current.
* The signing certificate is valid.

---

## Example Input

| Parameter           | Example             |
| ------------------- | ------------------- |
| UID                 | 999988887777        |
| Authentication Type | Fingerprint         |
| Device ID           | MANTRA-MFS100-001   |
| Transaction ID      | BIO202607161600001  |
| Timestamp           | 2026-07-16T16:00:00 |
| Version             | 2.5                 |

> The actual biometric data is captured directly from the biometric device and is **not entered manually**.

---

## Authentication Workflow

```text id="fnhv4z"
Resident Provides Aadhaar Number
            │
            ▼
Connect Certified Biometric Device
            │
            ▼
Capture Biometric Data
            │
            ▼
Validate Captured Data
            │
            ▼
Collect Authentication Inputs
            │
            ▼
Generate PID XML
```

---

## Output of this Step

After completing this step, your application should have:

* Resident identification (UID or VID)
* Captured biometric data
* Authentication credentials
* Transaction details
* Device information

These inputs are required to generate the **PID XML** in the next step.

The next step is:

**Step 2 – Generate PID XML**

---

## Best Practices

* Ensure the resident's finger, iris, or face is captured clearly.
* Verify that the biometric device is certified and functioning properly.
* Generate a new Transaction ID for every authentication request.
* Capture biometric data immediately before authentication to ensure freshness.
* Follow the biometric device manufacturer's recommendations for optimal capture quality.

---

## Notes

> Biometric data should always be captured directly from a certified biometric device. Applications should never allow manual entry or modification of biometric data.

> Do not store raw biometric data unless explicitly permitted by UIDAI guidelines and your organization's security policies.

> The captured biometric data is highly sensitive and should be processed securely throughout the authentication workflow.
