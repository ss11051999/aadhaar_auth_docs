---
sidebar_position: 1
---

# Introduction

Welcome to the **Aadhaar Authentication API Documentation**.

This documentation is designed to help developers understand the complete Aadhaar Authentication workflow, from collecting resident information to receiving and processing the Authentication Response.

The documentation explains the authentication process, required data, XML structures, security mechanisms, request and response flow, and error handling without focusing on any specific programming language.

In this integration model, a **Sub-AUA does not directly communicate with the Aadhaar Authentication Server**. The Sub-AUA operates through its associated **AUA (Authentication User Agency)**. The AUA acts as the authorized integration layer between the Sub-AUA application and the Aadhaar Authentication ecosystem.

The general communication flow is:

```text
Resident
    │
    ▼
Sub-AUA Application
    │
    │ Authentication Request
    ▼
AUA
    │
    │ Authentication Request
    ▼
Aadhaar Authentication Ecosystem
    │
    │ Authentication Response
    ▼
AUA
    │
    │ Authentication Response
    ▼
Sub-AUA Application
    │
    ▼
Resident
```

Whether you are integrating **OTP Authentication**, **Biometric Authentication**, **Demographic Authentication**, **Face Authentication**, **eKYC**, or **Multi-Factor Authentication**, this guide provides a clear, step-by-step explanation of the authentication lifecycle and the role of the Sub-AUA and AUA in the process.

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

## Role of Sub-AUA and AUA

The Aadhaar Authentication integration described in this documentation follows a **Sub-AUA to AUA integration model**.

### Sub-AUA

The **Sub-AUA** is the application or organization that consumes Aadhaar Authentication services through an associated AUA.

The Sub-AUA is responsible for:

* Collecting required resident information.
* Obtaining resident consent where applicable.
* Preparing the authentication data.
* Generating the required XML documents.
* Encrypting sensitive authentication information.
* Generating HMAC where required.
* Creating the Authentication Request XML.
* Digitally signing the Authentication Request XML where required.
* Sending the authentication request to the AUA.
* Receiving the Authentication Response from the AUA.
* Processing the authentication result.
* Handling success and failure scenarios.
* Maintaining required transaction and audit information.

The Sub-AUA does **not directly communicate with the Aadhaar Authentication Server**. The authentication request and response are handled through the associated AUA.

---

### AUA

The **AUA (Authentication User Agency)** is the authorized entity that provides the Aadhaar Authentication service to the Sub-AUA.

The AUA acts as the communication and integration layer between the Sub-AUA and the Aadhaar Authentication ecosystem.

The AUA is responsible for handling the authentication request received from the Sub-AUA and communicating with the Aadhaar Authentication ecosystem according to the applicable integration requirements.

The AUA may:

* Receive Authentication Requests from the Sub-AUA.
* Validate incoming requests.
* Process authentication requests according to the applicable integration flow.
* Forward requests to the appropriate authentication infrastructure.
* Receive Authentication Responses.
* Return the Authentication Response to the Sub-AUA.
* Provide transaction and error information required by the Sub-AUA.

The exact responsibilities and request/response interfaces may vary depending on the AUA integration model and the services provided to the Sub-AUA.

---

## Request and Response Flow

In a Sub-AUA integration, the Authentication Request and Authentication Response are exchanged through the AUA.

The general flow is:

```text
Sub-AUA Application
        │
        │ 1. Prepare Authentication Request
        ▼
Authentication Request XML
        │
        │ 2. Send Request to AUA
        ▼
AUA
        │
        │ 3. Process / Forward Authentication Request
        ▼
Aadhaar Authentication Ecosystem
        │
        │ 4. Process Authentication
        ▼
Authentication Response
        │
        │ 5. Response received by AUA
        ▼
AUA
        │
        │ 6. Return Authentication Response
        ▼
Sub-AUA Application
        │
        ▼
Process Authentication Result
```

Therefore, the Sub-AUA application should treat the AUA as the integration endpoint for sending authentication requests and receiving authentication responses.

---

## Purpose of this Documentation

The primary objective of this documentation is to provide developers with a clear understanding of the Aadhaar Authentication process from a **Sub-AUA integration perspective**.

This guide explains:

* How the Sub-AUA authentication workflow operates.
* The role of the Sub-AUA in the authentication process.
* The role of the AUA as the integration layer.
* How authentication requests are prepared by the Sub-AUA.
* How required XML documents are generated.
* How encryption, hashing, and digital signatures are applied.
* How Authentication Request XML is created.
* How Authentication Requests are submitted to the AUA.
* How Authentication Responses are received from the AUA.
* How Authentication Response XML is processed.
* How authentication results are determined.
* How to interpret authentication error codes.
* Common integration questions and best practices.

This documentation focuses on the authentication workflow and the **Sub-AUA to AUA communication model** rather than application-specific implementation.

---

## Supported Authentication Methods

This documentation covers the following authentication methods:

* OTP Authentication
* Biometric Authentication
* Demographic Authentication
* Face Authentication
* eKYC Authentication
* Multi-Factor Authentication

Each authentication method follows a similar high-level workflow while using different authentication factors.

The Sub-AUA prepares the required authentication information based on the selected authentication method and submits the request through the associated AUA.

---

## Authentication Workflow

The overall Aadhaar Authentication process consists of the following stages:

```text
Collect Required Inputs
        │
        ▼
Generate Authentication Data
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
Digitally Sign XML
        │
        ▼
Send Authentication Request to AUA
        │
        ▼
AUA Processes / Forwards Request
        │
        ▼
Aadhaar Authentication Processing
        │
        ▼
Authentication Response Returned to AUA
        │
        ▼
AUA Returns Response to Sub-AUA
        │
        ▼
Process Authentication Result
```

The exact authentication data generated depends on the selected authentication method.

---

## Request and Response Architecture

The following diagram represents the high-level architecture for a Sub-AUA integration:

```text
┌─────────────────────┐
│      Resident       │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│   Sub-AUA System    │
│                     │
│ Prepare Request     │
│ Encrypt Data        │
│ Generate HMAC       │
│ Sign Request        │
└──────────┬──────────┘
           │
           │ Authentication Request
           ▼
┌─────────────────────┐
│         AUA         │
│                     │
│ Integration Layer   │
└──────────┬──────────┘
           │
           │ Authentication Request
           ▼
┌─────────────────────────────────┐
│ Aadhaar Authentication         │
│ Ecosystem                      │
└──────────┬──────────────────────┘
           │
           │ Authentication Response
           ▼
┌─────────────────────┐
│         AUA         │
└──────────┬──────────┘
           │
           │ Authentication Response
           ▼
┌─────────────────────┐
│   Sub-AUA System    │
│                     │
│ Parse Response      │
│ Validate Result     │
│ Process Success     │
│ Handle Errors       │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│      Resident       │
└─────────────────────┘
```

The Sub-AUA should maintain transaction correlation between the request submitted to the AUA and the response received from the AUA.

---

## Who Should Read This Documentation?

This guide is intended for:

* Sub-AUA Backend Developers
* AUA Integration Developers
* API Integrators
* System Architects
* Solution Engineers
* Technical Consultants
* QA Engineers
* DevOps and Infrastructure Engineers
* Anyone integrating Aadhaar Authentication services through an AUA

No prior knowledge of Aadhaar Authentication is required. The documentation explains each step in a structured and beginner-friendly manner.

---

## What You Will Learn

After reading this documentation, you will understand:

* The complete Aadhaar Authentication process.
* The role of a Sub-AUA in the authentication workflow.
* The role of an AUA as the integration layer.
* How Authentication Requests are prepared by the Sub-AUA.
* The purpose of each XML document.
* How PID XML is generated.
* Why encryption and HMAC are required.
* How Authentication Request XML is created.
* Why digital signatures are required where applicable.
* How Authentication Requests are submitted to the AUA.
* How Authentication Responses are received from the AUA.
* How to interpret authentication responses.
* How to troubleshoot common authentication errors.
* How to handle transaction correlation and response processing.

---

## Documentation Structure

The documentation is organized into the following sections:

* **Introduction**
* **Sub-AUA and AUA Integration**
* **Supported Authentication Methods**
* **OTP Authentication**
* **Biometric Authentication**
* **Demographic Authentication**
* **Face Authentication**
* **eKYC Authentication**
* **Multi-Factor Authentication**
* **Authentication Request & Response**
* **Error Codes**
* **Frequently Asked Questions (FAQ)**

Each authentication guide provides a step-by-step explanation with diagrams, XML examples, request and response flows, and implementation notes.

---

## Security Considerations

Authentication requests contain sensitive resident information. When integrating with Aadhaar Authentication services through an AUA, always follow applicable security requirements and organizational policies.

Recommended security practices include:

* Always use HTTPS for communication between the Sub-AUA and AUA.
* Encrypt sensitive authentication data before transmission where required.
* Digitally sign Authentication Request XML where required.
* Generate a new Session Key for every authentication request where applicable.
* Do not store OTPs or biometric data.
* Avoid logging sensitive information such as PID XML, Session Keys, HMAC values, or biometric information.
* Validate every Authentication Response received from the AUA before processing it.
* Verify the Transaction ID and correlate it with the original authentication request.
* Log only the minimum non-sensitive information required for monitoring and auditing.

Following these practices helps ensure secure and reliable authentication integration.

---

## Before You Begin

Before integrating Aadhaar Authentication services as a Sub-AUA, ensure that you have:

* An active and authorized relationship with an AUA.
* Access to the required authentication environment, such as Sandbox or Production, as provided by the AUA.
* Required credentials, keys, certificates, and configuration details provided by the AUA.
* Details of the AUA API endpoint for Authentication Requests.
* Understanding of the request format expected by the AUA.
* Understanding of the Authentication Response format returned by the AUA.
* A clear understanding of the authentication method you plan to use.
* Familiarity with XML-based request and response structures.
* Required network connectivity and security configuration.

The exact credentials, endpoints, certificates, request formats, and response formats depend on the AUA integration and should be obtained from the respective AUA.

---

## Getting Started

If you are new to Aadhaar Authentication as a Sub-AUA, it is recommended to read the documentation in the following order:

1. Introduction
2. Understand the Sub-AUA and AUA Integration Model
3. Review the Supported Authentication Methods
4. Choose the authentication method you want to implement.
5. Follow each step of the selected authentication workflow.
6. Review the Authentication Request preparation process.
7. Understand how the Authentication Request is submitted to the AUA.
8. Review how the Authentication Response is received from the AUA.
9. Validate and process the Authentication Response.
10. Refer to the Error Codes section when troubleshooting.
11. Explore the FAQ for common integration questions.

By following this guide, you will gain a complete understanding of the Aadhaar Authentication process from a **Sub-AUA perspective**, including how Authentication Requests are submitted through the AUA and how Authentication Responses are received and processed.

> **Important:** This documentation describes the general integration workflow. The exact API endpoints, request formats, authentication mechanisms, credentials, certificates, response formats, and operational procedures are subject to the specifications and integration requirements provided by the associated AUA.
