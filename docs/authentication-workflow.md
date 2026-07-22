---
sidebar_position: 2
---

# Authentication Workflow

## Overview

The Aadhaar Authentication workflow allows a resident's identity to be verified using one or more authentication factors such as **OTP, biometric, demographic information, or face authentication**.

In the **Sub-AUA integration model**, the Sub-AUA communicates with the **AUA (Authentication User Agency)**. The AUA handles communication with the Aadhaar Authentication ecosystem through the applicable integration flow.
 

The Sub-AUA is responsible for preparing the authentication request and processing the response. The exact API endpoints, request formats, and security requirements depend on the integration specifications provided by the associated AUA/KUA.

---

## Authentication Workflow Steps

The authentication process consists of the following steps:

```text
1. Collect Required Inputs
          │
          ▼
2. Generate Authentication Data
          │
          ▼
3. Encrypt Sensitive Data
          │
          ▼
4. Generate HMAC
          │
          ▼
5. Create Authentication Request
          │
          ▼
6. Digitally Sign Request
          │
          ▼
7. Send Request to AUA / KUA
          │
          ▼
8. AUA / KUA → ASA → UIDAI CIDR
          │
          ▼
9. Receive Response
          │
          ▼
10. Process Authentication Result
```

### Step 1 – Collect Required Inputs

The Sub-AUA collects the information required for the selected authentication method. Depending on the authentication type, this may include:

- Aadhaar Number (UID) or Virtual ID (VID)
- OTP
- Fingerprint
- Iris
- Face data
- Demographic information
- Transaction details
- Other information required by the AUA/KUA

*Note: Resident consent should be obtained where applicable.*

### Step 2 – Generate Authentication Data

Generate the authentication data according to the selected authentication method.

| Authentication Method | Authentication Data |
| :--- | :--- |
| **OTP** | OTP-related authentication data / PID XML |
| **Biometric** | PID XML containing biometric data |
| **Demographic** | Required demographic information |
| **Face** | Face authentication data |
| **eKYC** | Required authentication data and consent |
| **Multi-Factor** | Combination of applicable authentication factors |

*The exact data and XML structure depend on the authentication method and the AUA/KUA integration specifications.*

### Step 3 – Encrypt Sensitive Data

Encrypt sensitive authentication information according to the applicable integration requirements. This may include:

- PID XML
- Authentication data
- Session Key
- Other sensitive information

The encryption mechanism and key management process must follow the specifications provided by the AUA/KUA.

### Step 4 – Generate HMAC

Generate the required HMAC (Hash-based Message Authentication Code) using the applicable authentication data.

HMAC helps ensure the integrity of the authentication data and allows the receiving system to detect unauthorized modifications. The exact HMAC generation process depends on the applicable authentication specifications.

### Step 5 – Create Authentication Request

Create the Authentication Request XML using the required authentication information. Depending on the authentication method, the request may contain:

- Authentication details
- Authentication type
- Metadata
- Encrypted Session Key
- Encrypted authentication data
- HMAC
- Transaction ID
- Timestamp
- Other required attributes

The exact request structure depends on the authentication method and the interface provided by the AUA/KUA.

### Step 6 – Digitally Sign the Request

Digitally sign the Authentication Request XML using the applicable certificate and signing mechanism. The digital signature provides:

- Sender authentication
- Data integrity
- Protection against modification
- Non-repudiation where applicable

The signing process and certificate requirements must follow the AUA/KUA integration specifications.

### Step 7 – Send Request to AUA / KUA

The Sub-AUA sends the prepared Authentication Request to the AUA/KUA using the API and communication mechanism provided by the AUA/KUA.

```text
Sub-AUA
    │
    │ Authentication Request
    ▼
AUA / KUA
```

The AUA/KUA receives and processes the request according to the applicable integration flow.

### Step 8 – Authentication Request Processing

The request moves through the authentication ecosystem:

```text
Sub-AUA ──► AUA / KUA ──► ASA ──► UIDAI CIDR
```

The request is processed by the applicable components, and UIDAI CIDR generates the authentication result. The response then follows the reverse path:

```text
UIDAI CIDR ──► ASA ──► AUA / KUA ──► Sub-AUA
```

### Step 9 – Receive Authentication Response

The Sub-AUA receives the Authentication Response through the AUA/KUA. The response may contain:

- Authentication result
- Transaction ID
- Timestamp
- Response code
- Error code, if authentication fails
- Other applicable response information

The Sub-AUA should validate and parse the response before processing the result.

> **Important:** Do not determine authentication success only from the HTTP status code. Always check the actual authentication result returned in the Authentication Response.

### Step 10 – Process Authentication Result

The Sub-AUA processes the authentication result.

#### If Authentication Succeeds:
- Continue the business workflow.
- Provide the requested service, where applicable.
- Record the transaction using non-sensitive information.

#### If Authentication Fails:
- Read the returned error code.
- Display an appropriate message to the resident.
- Allow retry where applicable.
- Record required transaction information for troubleshooting and auditing.

---

## Workflow Summary

| Step | Description |
| :---: | :--- |
| **1** | Collect Required Inputs |
| **2** | Generate Authentication Data |
| **3** | Encrypt Sensitive Data |
| **4** | Generate HMAC |
| **5** | Create Authentication Request |
| **6** | Digitally Sign Request |
| **7** | Send Request to AUA/KUA |
| **8** | Request processed through AUA/KUA → ASA → UIDAI CIDR |
| **9** | Receive Authentication Response |
| **10** | Process Authentication Result |

---

## Authentication Methods

The workflow applies to authentication methods such as:

- OTP Authentication
- Biometric Authentication
- Demographic Authentication
- Face Authentication
- eKYC Authentication
- Multi-Factor Authentication

The overall request and response flow remains similar, while the authentication data and request structure may differ.

---

## Best Practices

When implementing the authentication workflow:

- **Follow Specifications:** Follow the integration specifications provided by the AUA/KUA.
- **Secure Transport:** Use HTTPS for communication.
- **Data Encryption:** Encrypt sensitive authentication data as required.
- **Session Keys:** Generate a new Session Key for each request where applicable.
- **Integrity Checks:** Generate HMAC as required.
- **Digital Signatures:** Digitally sign requests where required.
- **Response Validation:** Validate every Authentication Response and match the response Transaction ID with the original request.
- **Do Not Rely Only on HTTP Status:** Do not rely only on HTTP status codes for authentication results.
- **Sensitive Data Handling:**
  - Do **NOT** store OTPs, biometric data, or raw PID XML.
  - Do **NOT** log sensitive information such as Session Keys or HMAC values.
- **Auditing:** Maintain transaction correlation for auditing and troubleshooting.

> **Important:** The exact API endpoints, request and response formats, credentials, certificates, security mechanisms, and operational procedures depend on the associated AUA/KUA and its integration specifications.