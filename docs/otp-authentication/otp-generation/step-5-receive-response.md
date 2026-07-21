---
sidebar_position: 5
---

# Step 5 - Receive and Process OTP Response

## Goal

After sending the OTP Request to the Aadhaar Authentication Server, the server returns a response wrapped inside a common response structure.

The response contains:

* `client_id` – Identifies the AUA client.
* `res_code` – Indicates whether the request was successfully processed by the intermediary/service layer.
* `res_hash` – Hash value associated with the response data.
* `res_data` – Base64-encoded Authentication Response XML.
* `res_msg` – Message returned by the service provider.

The `res_data` value must be Base64-decoded to obtain the actual Authentication Response XML.

> **Important:** An HTTP `200 OK` response only indicates that the HTTP request was successfully received and processed at the transport/service level. It does not necessarily mean that OTP generation or Aadhaar authentication was successful.

---

## Response Flow

```text
OTP Request Sent
        │
        ▼
Aadhaar Authentication Server
        │
        ▼
Receive HTTP Response
        │
        ▼
Check HTTP Status Code
        │
        ▼
Read Common Response XML
        │
        ├── client_id
        ├── res_code
        ├── res_hash
        ├── res_data
        └── res_msg
        │
        ▼
Base64 Decode res_data
        │
        ▼
Get Authentication Response XML
        │
        ▼
Parse AuthRes
        │
        ▼
Check ret Attribute
        │
        ├── ret="y" → Success
        │
        └── ret="n" → Failure