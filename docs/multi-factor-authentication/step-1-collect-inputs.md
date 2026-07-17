---
sidebar_position: 1
---

# Step 1 - Collect Required Inputs

## Goal

The first step in **Multi-Factor Authentication** is to collect all the information required to create a valid Authentication Request.

Unlike other authentication methods that use a single authentication factor, Multi-Factor Authentication combines **two or more authentication factors** to provide stronger identity verification.

The collected information will later be used to generate the PID XML and Authentication Request XML.

---

## What is Multi-Factor Authentication?

Multi-Factor Authentication verifies a resident's identity by combining multiple authentication methods in a single authentication request.

Instead of relying on just one factor, such as OTP or Fingerprint, multiple factors are verified together to provide a higher level of security.

Some common combinations include:

* OTP + Fingerprint
* OTP + Iris
* OTP + Face
* Fingerprint + Iris
* Fingerprint + Face
* OTP + Fingerprint + Face
* OTP + Fingerprint + Iris

The authentication factors used depend on your organization's requirements and the services enabled by UIDAI.

---

## Required Inputs

Before generating the Authentication Request, collect the following information.

### Resident Information

* Aadhaar Number (UID) or Virtual ID (VID)

---

### Authentication Factors

Collect the authentication data required for the selected authentication method.

Examples:

#### OTP

* Valid One-Time Password (OTP)

#### Fingerprint

* Fingerprint captured using a certified Registered Device (RD)

#### Iris

* Iris image captured using a certified Registered Device (RD)

#### Face

* Face image captured using a supported device or RD Service

> At least **two authentication factors** should be collected for Multi-Factor Authentication.

---

### Transaction Information

Every authentication request should include:

* Transaction ID
* Current Timestamp
* Authentication Type (Multi-Factor)

Each Transaction ID should be unique.

---

### Authentication Credentials

Ensure the following configuration is available:

* AUA Code
* ASA Code (if applicable)
* License Key
* API Version
* Terminal ID (if applicable)

These values are generally configured during the integration process.

---

### Certificate Information

The following certificates are required for secure communication:

* UIDAI Public Certificate
* Organization Signing Certificate
* Private Key associated with the signing certificate

These certificates are used in later steps for encryption and digital signing.

---

## Input Validation Checklist

Before proceeding, verify that:

* Aadhaar Number (UID) or Virtual ID (VID) is valid.
* All selected authentication factors have been successfully collected.
* OTP has not expired (if applicable).
* Biometric data has been captured successfully (if applicable).
* Face image has been captured successfully (if applicable).
* A unique Transaction ID has been generated.
* The system timestamp is correct.
* Authentication credentials are properly configured.

---

## Example Input

| Field                  | Example                 |
| ---------------------- | ----------------------- |
| Aadhaar Number         | 123412341234            |
| Authentication Factors | OTP + Fingerprint       |
| OTP                    | 583921                  |
| Fingerprint            | Captured from RD Device |
| Transaction ID         | MFA202607170001         |
| Timestamp              | 2026-07-17T14:30:00     |

> The values shown above are for illustration purposes only.

---

## Workflow

```text id="mfa-step1-flow"
Resident
      │
      ▼
Enter Aadhaar Number / VID
      │
      ▼
Select Authentication Factors
      │
      ▼
Collect OTP / Biometrics / Face
      │
      ▼
Generate Transaction ID
      │
      ▼
Generate Timestamp
      │
      ▼
Validate All Inputs
      │
      ▼
Ready for PID XML Generation
```

---

## Best Practices

* Use at least two authentication factors.
* Ensure OTP is valid and has not expired.
* Capture biometric data using certified Registered Devices (RD).
* Validate all inputs before generating the request.
* Use a unique Transaction ID for every authentication request.
* Synchronize the system clock to ensure accurate timestamps.
* Never log OTPs, biometric data, or face images.
* Verify that all required certificates and authentication credentials are available.

---

## Output of this Step

At the end of this step, your application should have:

* Aadhaar Number (UID) or Virtual ID (VID)
* All required authentication factors (OTP, Fingerprint, Iris, Face, etc.)
* Transaction ID
* Timestamp
* Authentication credentials
* Required certificates

These inputs will be used in the next step to generate the **PID XML** containing the selected authentication factors.

---

## Next Step

Continue to **Step 2 – Generate PID XML**, where the collected authentication factors are organized into a secure PID XML document for Multi-Factor Authentication.
