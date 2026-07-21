---
sidebar_position: 2
---

# Authentication Workflow

## Overview

The Aadhaar Authentication workflow is a secure process used to verify a resident's identity using their **Aadhaar Number (UID)** or **Virtual ID (VID)** and one or more authentication factors.

In the **Sub-AUA integration model**, the Sub-AUA does not directly communicate with the Aadhaar Authentication Server. The **AUA (Authentication User Agency)** acts as the authorized integration layer between the Sub-AUA and the Aadhaar Authentication ecosystem.

The Sub-AUA prepares the required authentication request and submits it to the AUA. The AUA processes or forwards the request according to the applicable integration flow and returns the Authentication Response to the Sub-AUA.

Regardless of the authentication method (OTP, Biometric, Demographic, Face, eKYC, or Multi-Factor), the overall workflow remains largely the same. The primary difference lies in the type of authentication data collected and processed during the request.

This guide provides a high-level overview of the complete authentication lifecycle from a **Sub-AUA perspective**.

---

## Authentication Workflow

```text
                         Start
                           │
                           ▼
                Collect Required Inputs
                           │
                           ▼
             Generate Authentication Data
                (OTP / PID XML / Demo Data)
                           │
                           ▼
              Encrypt Sensitive Information
                           │
                           ▼
                     Generate HMAC
                           │
                           ▼
           Create Authentication Request XML
                           │
                           ▼
              Digitally Sign Request XML
                           │
                           ▼
            Send Request to AUA
                           │
                           ▼
                  AUA Processes Request
                           │
                           ▼
        AUA Sends Request to Authentication
                 Ecosystem / Server
                           │
                           ▼
          Authentication Response Generated
                           │
                           ▼
                Response Received by AUA
                           │
                           ▼
            AUA Returns Response to Sub-AUA
                           │
                           ▼
          Sub-AUA Processes Authentication Result
                           │
                           ▼
                          End
```

---

## Step 1 – Collect Required Inputs

The authentication process begins by collecting all required information.

Depending on the authentication type, the required inputs may include:

* Aadhaar Number (UID) or Virtual ID (VID)
* OTP
* Fingerprint
* Iris
* Face
* Demographic information
* Transaction ID
* Timestamp
* License or integration credentials
* Authentication credentials
* Other information required by the selected authentication method and AUA

The Sub-AUA should collect the required information in accordance with the applicable authentication requirements and the integration specifications provided by the AUA.

At the end of this step, the Sub-AUA has the information required to prepare the authentication request.

---

## Step 2 – Generate Authentication Data

Generate the authentication data based on the selected authentication method.

Examples:

| Authentication Method | Authentication Data |
| --------------------- | ------------------- |
| OTP                   | OTP-related authentication data / PID XML |
| Biometric             | PID XML containing fingerprint, iris, or face data |
| Demographic           | Resident demographic information |
| Face                  | Face biometric authentication data |
| eKYC                  | Authentication data with applicable resident consent |
| Multi-Factor          | Combination of multiple authentication factors |

The exact data and XML structure depend on the selected authentication method and the applicable integration specifications.

This authentication data becomes the input for the next step.

---

## Step 3 – Encrypt Sensitive Information

Sensitive authentication information must be protected before transmission.

Depending on the authentication method and applicable specifications, this may include:

* PID XML
* Authentication data
* Session Key
* Other sensitive authentication information

Encryption protects sensitive resident information during transmission and ensures that protected authentication data can only be processed by authorized components.

The Sub-AUA should follow the encryption mechanism and key management requirements specified for the applicable integration.

---

## Step 4 – Generate HMAC

Generate an HMAC (Hash-based Message Authentication Code) using the authentication data where required.

The HMAC allows the receiving authentication infrastructure to verify the integrity of the protected authentication data and detect unauthorized modification.

This helps ensure that the authentication data has not been altered during processing or transmission.

The exact HMAC generation mechanism should follow the applicable authentication specifications.

---

## Step 5 – Create Authentication Request XML

Create the Authentication Request XML using the required authentication information.

Depending on the authentication method, the request may contain:

* Authentication details
* Authentication type
* Metadata
* Encrypted Session Key
* Encrypted authentication data
* HMAC
* Transaction ID
* Timestamp
* Other required request attributes

The Authentication Request XML represents the complete authentication request prepared by the Sub-AUA.

The exact request structure depends on the authentication method and the interface defined by the AUA.

---

## Step 6 – Digitally Sign the XML

Digitally sign the Authentication Request XML using the applicable signing certificate and mechanism required by the integration.

The digital signature provides:

* Authentication of the sender
* Data integrity
* Assurance that the request has not been modified after signing
* Non-repudiation where applicable

Any modification to the signed content after signing may cause signature verification to fail.

The exact certificate, signing process, and responsibility for signing should follow the integration requirements defined by the AUA.

---

## Step 7 – Send Authentication Request to AUA

The Sub-AUA sends the prepared Authentication Request to the **AUA** using the interface and secure communication mechanism provided by the AUA.

The high-level flow is:

```text
Sub-AUA
    │
    │ Authentication Request
    ▼
AUA
    │
    │ Process / Forward Request
    ▼
Aadhaar Authentication Ecosystem
```

The AUA acts as the integration layer between the Sub-AUA and the Aadhaar Authentication ecosystem.

The AUA may perform applicable validations and processing before forwarding or submitting the request to the appropriate authentication infrastructure.

The exact request endpoint, authentication mechanism, request format, and transport requirements are provided by the associated AUA.

---

## Step 8 – AUA Processes the Authentication Request

After receiving the request from the Sub-AUA, the AUA processes the request according to the applicable integration requirements.

The high-level processing flow may include:

* Receiving the request from the Sub-AUA.
* Validating the request.
* Performing applicable authentication and security checks.
* Forwarding or submitting the authentication request to the appropriate authentication infrastructure.
* Receiving the Authentication Response.
* Returning the relevant response to the Sub-AUA.

The exact internal processing performed by the AUA may vary depending on the AUA's architecture and integration model.

---

## Step 9 – Receive Authentication Response from AUA

After the authentication request has been processed, the Authentication Response is returned through the AUA.

The response flow is:

```text
Aadhaar Authentication Ecosystem
              │
              │ Authentication Response
              ▼
             AUA
              │
              │ Authentication Response
              ▼
          Sub-AUA
```

The response may contain information such as:

* Authentication result
* Transaction ID
* Timestamp
* Response code
* Error code, if authentication fails
* Additional response information, if applicable

The Sub-AUA should validate and parse the response before continuing the business workflow.

---

## Step 10 – Process the Authentication Result

Finally, the Sub-AUA processes the authentication result received from the AUA.

If authentication succeeds:

* Continue the business workflow.
* Grant access to the requested service, where applicable.
* Record the successful transaction.
* Store only the minimum information required for audit and reconciliation.

If authentication fails:

* Read the returned error code.
* Display an appropriate user-friendly message.
* Allow the resident to retry if applicable.
* Record the transaction details required for troubleshooting and auditing.
* Refer to the Error Codes section for additional information.

The Sub-AUA should not assume that the authentication was successful based solely on the HTTP response status. The actual authentication result must be determined from the Authentication Response.

---

## Complete Request and Response Flow

The complete Sub-AUA authentication flow can be represented as follows:

```text
┌─────────────────────┐
│   Sub-AUA System    │
└──────────┬──────────┘
           │
           │ 1. Collect Inputs
           ▼
┌─────────────────────┐
│ Generate Auth Data  │
└──────────┬──────────┘
           │
           │ 2. Encrypt / HMAC
           ▼
┌─────────────────────┐
│ Create Auth Request │
└──────────┬──────────┘
           │
           │ 3. Sign Request
           ▼
┌─────────────────────┐
│         AUA         │
└──────────┬──────────┘
           │
           │ 4. Process / Forward
           ▼
┌─────────────────────────────────┐
│ Aadhaar Authentication          │
│ Ecosystem                       │
└──────────┬──────────────────────┘
           │
           │ 5. Authentication Response
           ▼
┌─────────────────────┐
│         AUA         │
└──────────┬──────────┘
           │
           │ 6. Return Response
           ▼
┌─────────────────────┐
│   Sub-AUA System    │
└──────────┬──────────┘
           │
           │ 7. Validate Response
           ▼
┌─────────────────────┐
│ Process Result      │
└─────────────────────┘
```

---

## Workflow Summary

| Step | Description |
| ---- | ----------- |
| 1 | Collect Required Inputs |
| 2 | Generate Authentication Data |
| 3 | Encrypt Sensitive Information |
| 4 | Generate HMAC |
| 5 | Create Authentication Request XML |
| 6 | Digitally Sign XML |
| 7 | Send Authentication Request to AUA |
| 8 | AUA Processes / Forwards Authentication Request |
| 9 | Receive Authentication Response from AUA |
| 10 | Process Authentication Result |

---

## Authentication Methods Covered

The workflow described in this guide applies to the following authentication methods:

* OTP Authentication
* Biometric Authentication
* Demographic Authentication
* Face Authentication
* eKYC Authentication
* Multi-Factor Authentication

Although the authentication factor changes, the overall request and response lifecycle remains consistent.

The main difference is the type of authentication data generated and the specific request structure required for each authentication method.

---

## Best Practices

When implementing the Aadhaar Authentication workflow as a Sub-AUA:

* Follow the integration specifications provided by the associated AUA.
* Generate a new Session Key for every request where applicable.
* Encrypt sensitive authentication data before transmission.
* Digitally sign the Authentication Request XML where required.
* Use HTTPS for all communication between the Sub-AUA and AUA.
* Validate every Authentication Response received from the AUA.
* Match the response Transaction ID with the original request.
* Do not rely solely on the HTTP status code to determine authentication success.
* Evaluate the actual authentication result returned in the response.
* Do not store OTPs, biometric data, or raw PID XML.
* Do not log Session Keys, HMAC values, or other sensitive authentication information.
* Log only non-sensitive information required for monitoring, auditing, and troubleshooting.
* Maintain appropriate transaction correlation between the Sub-AUA request and the received response.

---

## Next Steps

Now that you understand the overall authentication workflow and the role of the **Sub-AUA and AUA**, you can explore the individual authentication guides for detailed implementation steps:

* OTP Authentication
* Biometric Authentication
* Demographic Authentication
* Face Authentication
* eKYC Authentication
* Multi-Factor Authentication

Each guide explains the required inputs, XML structures, encryption process, request generation, communication with the AUA, and Authentication Response handling specific to that authentication method.

> **Important:** The exact API endpoints, request formats, response formats, authentication mechanisms, certificates, credentials, and operational procedures depend on the associated AUA and the applicable integration specifications. Always follow the current technical documentation and requirements provided by the AUA.
