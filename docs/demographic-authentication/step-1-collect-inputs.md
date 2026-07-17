---
sidebar_position: 1
---

# Step 1 - Collect Required Inputs

## Goal

The first step in Demographic Authentication is to collect all the information required to create a valid Authentication Request.

Unlike OTP or Biometric Authentication, Demographic Authentication verifies the resident's identity using demographic information associated with their Aadhaar record.

The collected information will later be used to generate the Authentication Request XML.

---

## What is Demographic Authentication?

Demographic Authentication verifies a resident's identity by matching demographic information provided in the authentication request with the information available in the Aadhaar database.

Depending on the authentication requirements, one or more demographic attributes may be used for verification.

Examples include:

* Name
* Date of Birth
* Age
* Gender
* Address
* Phone Number
* Email Address

The exact demographic fields required depend on your organization's authentication policy and UIDAI guidelines.

---

## Required Inputs

Before generating the Authentication Request, collect the following information.

### Resident Information

* Aadhaar Number (UID) or Virtual ID (VID)
* Resident's Name
* Date of Birth or Age (if applicable)
* Gender (if applicable)
* Address (if applicable)
* Phone Number (if applicable)
* Email Address (if applicable)

> Collect only the demographic information required for your authentication use case.

---

### Transaction Information

Every authentication request should include:

* Transaction ID
* Current Timestamp
* Authentication Type (Demographic)

The Transaction ID should be unique for every request.

---

### Authentication Credentials

Ensure the following configuration is available before proceeding:

* AUA Code
* ASA Code (if applicable)
* License Key
* API Version
* Terminal ID (if applicable)

These values are generally configured during the integration process.

---

### Certificate Information

To securely process the request, the following certificates are required:

* UIDAI Public Certificate (used for encryption)
* Organization Signing Certificate
* Private Key associated with the signing certificate

These certificates are used in later steps for encryption and digital signing.

---

## Input Validation Checklist

Before proceeding, verify that:

* Aadhaar Number (UID) or Virtual ID (VID) is valid.
* All mandatory demographic fields are available.
* The demographic information is entered accurately.
* A unique Transaction ID has been generated.
* The system timestamp is correct.
* Authentication credentials are properly configured.

---

## Example Input

| Field          | Example             |
| -------------- | ------------------- |
| Aadhaar Number | 123412341234        |
| Name           | Rahul Sharma        |
| Date of Birth  | 15-08-1992          |
| Gender         | Male                |
| Transaction ID | DEMO202607170001    |
| Timestamp      | 2026-07-17T10:30:00 |

> The values shown above are for illustration purposes only.

---

## Workflow

```text id="demo-step1-flow"
Resident
      │
      ▼
Enter Aadhaar Number / VID
      │
      ▼
Collect Required Demographic Information
      │
      ▼
Generate Transaction ID
      │
      ▼
Generate Timestamp
      │
      ▼
Validate Input
      │
      ▼
Ready for Authentication Request
```

---

## Best Practices

* Validate all input before generating the request.
* Collect only the demographic fields required for authentication.
* Use a unique Transaction ID for every request.
* Synchronize the system clock to ensure accurate timestamps.
* Never log or expose sensitive resident information unnecessarily.
* Ensure all mandatory configuration values are available before proceeding.

---

## Output of this Step

At the end of this step, your application should have:

* Aadhaar Number (UID) or Virtual ID (VID)
* Required demographic information
* Transaction ID
* Timestamp
* Authentication credentials
* Required certificates

These inputs are used in the next step to create the **PID XML** containing the demographic information.

---

## Next Step

Continue to **Step 2 – Generate PID XML**, where the collected demographic information is structured into a PID XML for secure authentication.
