---
sidebar_position: 1
---

# Introduction

Welcome to the **Aadhaar Authentication API Documentation**.

This documentation explains the Aadhaar Authentication process from a **Sub-AUA perspective**. It covers the overall API flow, authentication request preparation, security requirements, and response processing.

The **Sub-AUA does not directly communicate with UIDAI's CIDR**. Authentication requests are routed through the associated **AUA/KUA** and **ASA**.

---

## What is Aadhaar Authentication?

Aadhaar Authentication is a process of verifying the identity of a resident using their **Aadhaar Number (UID)** or **Virtual ID (VID)** along with one or more authentication factors.

Depending on the authentication method, identity can be verified using:

* One-Time Password (OTP)
* Fingerprint
* Iris
* Face
* Demographic Information
* Multiple authentication factors

The Sub-AUA application collects the required information and prepares the authentication request according to the applicable authentication specifications.

The request is then submitted to the **AUA**, which handles communication with the Aadhaar Authentication ecosystem.

After the authentication request is processed, the AUA receives the Authentication Response and passes the relevant response information back to the Sub-AUA application.

The Authentication Response indicates whether the authentication was successful or unsuccessful and may contain additional response information or error codes.

---

## Aadhaar Authentication Architecture

The high-level authentication flow is:

```text
┌──────────────
│   Resident   │
└──────┬───────┘
       │
       │ Provides Aadhaar / VID
       │ and authentication factor
       ▼
┌──────────────┐
│   Sub-AUA    │
│              │
│ Prepare      │
│ Auth Request │
└──────┬───────┘
       │
       │ Authentication Request
       ▼
┌──────────────┐
│   AUA / KUA  │
│              │
│ Authorized   │
│ Integration  │
└──────┬───────┘
       │
       │ AUA / ASA Protocol
       ▼
┌──────────────┐
│     ASA      │
│              │
│ Secure       │
│ Communication│
└──────┬───────┘
       │
       │ Secure Communication
       ▼
┌──────────────┐
│  UIDAI CIDR  │
│              │
│ Authentication│
│ Processing   │
└──────┬───────┘
       │
       │ Authentication Response
       ▼
┌──────────────┐
│     ASA      │
└──────┬───────┘
       │
       │ Response
       ▼
┌──────────────┐
│   AUA / KUA  │
└──────┬───────┘
       │
       │ Response
       ▼
┌──────────────┐
│   Sub-AUA    │
└──────┬───────┘
       │
       │ Result
       ▼
┌──────────────┐
│   Resident   │
└──────────────┘
```

Whether you are integrating **OTP Authentication**, **Biometric Authentication**, **Demographic Authentication**, **Face Authentication**, **eKYC**, or **Multi-Factor Authentication**, this guide provides a clear, step-by-step explanation of the authentication lifecycle and the role of the Sub-AUA and AUA in the process.

---

## Role of Each Component

### Resident

The **Resident** is the individual whose identity is being authenticated.

Depending on the authentication method, the resident may provide:

* Aadhaar Number (UID) or Virtual ID (VID)
* OTP
* Fingerprint
* Iris
* Face
* Demographic information

---

### Sub-AUA

The **Sub-AUA** is the application or organization consuming Aadhaar Authentication services through an associated AUA.

The Sub-AUA typically:

* Collects the required resident information and consent.
* Collects the required authentication factor.
* Prepares the authentication data.
* Generates the required XML and security elements.
* Creates the Authentication Request.
* Sends the request to the AUA/KUA.
* Receives the Authentication Response.
* Processes the authentication result.

The Sub-AUA communicates with the AUA/KUA, not directly with UIDAI CIDR.

---

### AUA / KUA

The **AUA (Authentication User Agency) / KUA (KYC User Agency)** acts as the authorized integration layer for authentication or eKYC services.

The AUA/KUA receives requests from the Sub-AUA and handles communication with the downstream authentication infrastructure according to the applicable integration process.

The AUA/KUA returns the authentication or eKYC response to the Sub-AUA.

---

### ASA

The **ASA (Authentication Service Agency)** provides the secure communication channel between the AUA/KUA and UIDAI's authentication infrastructure.

The ASA forwards authentication requests to UIDAI CIDR and returns the corresponding response to the AUA/KUA.

---

### UIDAI CIDR

The **UIDAI CIDR (Central Identities Data Repository)** is the UIDAI system where the authentication request is processed.

It verifies the submitted authentication information and generates the authentication response.

The response indicates whether the authentication was successful or unsuccessful and may include relevant response or error information.

---

## Authentication API Flow

The general API flow consists of the following steps:

```
1. Resident provides authentication information
              │
              ▼
2. Sub-AUA collects required data and consent
              │
              ▼
3. Sub-AUA prepares authentication data
              │
              ▼
4. Sub-AUA creates the Authentication Request
              │
              ▼
5. Sub-AUA sends request to AUA / KUA
              │
              ▼
6. AUA / KUA communicates through ASA
              │
              ▼
7. ASA sends request to UIDAI CIDR
              │
              ▼
8. UIDAI CIDR processes authentication
              │
              ▼
9. UIDAI CIDR returns Authentication Response
              │
              ▼
10. Response travels back through ASA
              │
              ▼
11. AUA / KUA returns response to Sub-AUA
              │
              ▼
12. Sub-AUA processes authentication result
```

---

## Authentication Methods

Depending on the service and integration, authentication may involve:

* OTP Authentication
* Biometric Authentication
* Demographic Authentication
* Face Authentication
* eKYC
* Multi-Factor Authentication

---

## Security

Authentication requests contain sensitive information. The integration must follow the applicable UIDAI and AUA security requirements.

Important practices include:

* Use secure HTTPS communication.
* Protect sensitive authentication information.
* Apply required encryption and hashing mechanisms.
* Use digital signatures where required.
* Do not store OTPs or biometric data unnecessarily.
* Do not log sensitive authentication information.
* Validate and securely process Authentication Responses.
* Maintain transaction correlation for request and response processing.

The exact security requirements, certificates, keys, endpoints, and request formats depend on the applicable UIDAI specifications and the integration requirements provided by the associated AUA/KUA.

---

## Before You Begin

Before starting the integration, ensure that you have:

* An authorized relationship with an AUA/KUA.
* Access to the required environment.
* Required credentials and certificates.
* AUA/KUA API endpoint details.
* Request and response specifications provided by the AUA/KUA.
* Required security and encryption configuration.
* Knowledge of the authentication method you want to implement.

---

## Documentation Structure

The documentation is organized into the following sections:

1. Introduction
2. Authentication Workflow
3. OTP Authentication
4. Biometric Authentication
5. Demographic Authentication
6. Face Authentication
7. eKYC Authentication
8. Multi-Factor Authentication
9. Authentication Request & Response
10. Error Codes
11. FAQ

Each section explains the relevant authentication process, request structure, security requirements, and response handling.

> **Note:** This documentation describes the general Sub-AUA authentication architecture and API flow. The exact API endpoints, request formats, response formats, credentials, certificates, and security requirements depend on the applicable UIDAI specifications and the associated AUA/KUA integration.

