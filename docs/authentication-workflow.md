---
sidebar_position: 2
---

# Authentication Workflow

## Overview

The Aadhaar Authentication workflow is a secure process used to verify a resident's identity using their **Aadhaar Number (UID)** or **Virtual ID (VID)** and one or more authentication factors.

Regardless of the authentication method (OTP, Biometric, Demographic, Face, eKYC, or Multi-Factor), the overall workflow remains largely the same. The primary difference lies in the type of authentication data collected during the process.

This guide provides a high-level overview of the complete authentication lifecycle.

---

# Authentication Workflow

```text id="workflow1"
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
    Send Request to Authentication Server
                      │
                      ▼
 Receive Authentication Response XML
                      │
                      ▼
    Process Authentication Result
                      │
                      ▼
                     End
```

---

# Step 1 – Collect Required Inputs

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
* License Key
* Authentication credentials

At the end of this step, your application has all the information required to begin authentication.

---

# Step 2 – Generate Authentication Data

Generate the authentication data based on the selected authentication method.

Examples:

| Authentication Method | Authentication Data                                |
| --------------------- | -------------------------------------------------- |
| OTP                   | OTP XML / PID XML containing OTP                   |
| Biometric             | PID XML containing fingerprint, iris, or face data |
| Demographic           | Resident demographic information                   |
| Face                  | Face biometric data                                |
| eKYC                  | Authentication data with resident consent          |
| Multi-Factor          | Combination of multiple authentication factors     |

This authentication data becomes the input for the next step.

---

# Step 3 – Encrypt Sensitive Information

Sensitive authentication information must be encrypted before transmission.

Typically, this includes:

* PID XML
* Authentication data
* Session Key

Encryption protects resident information while it travels over the network.

---

# Step 4 – Generate HMAC

Generate an HMAC (Hash-based Message Authentication Code) using the authentication data.

The HMAC allows the Authentication Server to verify that the authentication data has not been modified after it was created.

This ensures data integrity.

---

# Step 5 – Create Authentication Request XML

Create the Authentication Request XML by combining:

* Authentication details
* Authentication type
* Metadata
* Encrypted Session Key
* Encrypted authentication data
* HMAC

This XML represents the complete authentication request.

---

# Step 6 – Digitally Sign the XML

Digitally sign the Authentication Request XML using your organization's signing certificate.

The digital signature provides:

* Authentication
* Integrity
* Non-repudiation

Any modification made after signing invalidates the signature.

---

# Step 7 – Send Authentication Request

Send the signed Authentication Request XML to the Aadhaar Authentication Server using a secure HTTPS POST request.

The Authentication Server then:

* Validates the request
* Verifies the digital signature
* Decrypts the authentication data
* Performs authentication
* Generates the Authentication Response

---

# Step 8 – Receive Authentication Response

The Authentication Server returns an Authentication Response XML.

The response contains information such as:

* Authentication result
* Transaction ID
* Timestamp
* Error Code (if authentication fails)

Your application should validate and parse the response before continuing.

---

# Step 9 – Process the Result

Finally, process the authentication result.

If authentication succeeds:

* Continue the business workflow.
* Grant access to the requested service.
* Record the successful transaction.

If authentication fails:

* Read the returned error code.
* Display an appropriate message.
* Allow the resident to retry if applicable.
* Record the failure for troubleshooting and auditing.

---

# Workflow Summary

| Step | Description                       |
| ---- | --------------------------------- |
| 1    | Collect Required Inputs           |
| 2    | Generate Authentication Data      |
| 3    | Encrypt Sensitive Information     |
| 4    | Generate HMAC                     |
| 5    | Create Authentication Request XML |
| 6    | Digitally Sign XML                |
| 7    | Send Authentication Request       |
| 8    | Receive Authentication Response   |
| 9    | Process Authentication Result     |

---

# Authentication Methods Covered

The workflow described in this guide applies to the following authentication methods:

* OTP Authentication
* Biometric Authentication
* Demographic Authentication
* Face Authentication
* eKYC Authentication
* Multi-Factor Authentication

Although the authentication factor changes, the overall request lifecycle remains consistent.

---

# Best Practices

When implementing the Aadhaar Authentication workflow:

* Generate a new Session Key for every request.
* Encrypt all sensitive authentication data before transmission.
* Digitally sign every Authentication Request XML.
* Use HTTPS for all communication.
* Validate every Authentication Response XML.
* Use a unique Transaction ID for each request.
* Do not store OTPs, biometric data, or PID XML.
* Log only non-sensitive information required for monitoring and auditing.

---

# Next Steps

Now that you understand the overall authentication workflow, you can explore the individual authentication guides for detailed implementation steps:

* OTP Authentication
* Biometric Authentication
* Demographic Authentication
* Face Authentication
* eKYC Authentication
* Multi-Factor Authentication

Each guide explains the required inputs, XML structures, encryption process, request generation, and response handling specific to that authentication method.
