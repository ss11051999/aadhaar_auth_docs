---
sidebar_position: 5
---

# Step 5 - Receive and Process OTP Response

## Goal

Parse and process the response returned by the **AUA Server** following an OTP request. The response contains an outer transport XML wrapper (`<xml>`) that houses a Base64-encoded inner OTP response XML (`<OtpRes>`).

Your application must:

1. Validate the transport-level HTTP response.
2. Parse the outer XML payload.
3. Extract and Base64-decode the inner payload (`res_data`).
4. Evaluate the inner `<OtpRes>` attributes to confirm if the OTP was generated successfully.

---

## Response Flow

```text
Receive HTTP Response (200 OK)
            │
            ▼
 Parse Outer Response XML
            │
            ▼
 Verify client_id & res_code
            │
            ▼
 Extract & Base64 Decode <res_data>
            │
            ▼
 Parse Inner XML (<OtpRes>)
            │
            ▼
 Check 'ret' Attribute
            │
      ┌─────┴─────┐
      ▼           ▼
   ret="y"     ret="n"
      │           │
      ▼           ▼
  OTP Sent     OTP Request
  to User        Failed
```

---

## 1. Outer Response Structure

The AUA server wraps the OTP outcome inside an outer transport XML envelope.

### Example Outer XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<xml>
    <client_id>AUA-CHP</client_id>
    <res_code>10</res_code>
    <res_hash>BASE64_ENCODED_RESPONSE_HASH</res_hash>
    <res_data>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgi...</res_data>
    <res_msg>Success from CSC AAG.</res_msg>
</xml>
```

| Field | Description |
| :--- | :--- |
| `client_id` | Identifier of the Sub-AUA/client that initiated the request. |
| `res_code` | Status code from the gateway/AUA layer (`10` indicates processing completed). |
| `res_hash` | Hash value generated for payload integrity verification. |
| `res_data` | **Base64-encoded** inner OTP Response XML (`<OtpRes>`). |
| `res_msg` | Descriptive message from the gateway. |

---

## 2. Decoded Inner Response (`<OtpRes>`)

Base64-decoding the content of `<res_data>` produces the UIDAI OTP response document (`<OtpRes>`).

### Example Decoded XML

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<OtpRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    err=""
    info="04{...}"
    ret="y"
    ts="2026-07-16T15:30:05.123+05:30"
    txn="OTP202607161530001">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>
</OtpRes>
```

### Key Attributes

| Attribute | Description | Action Required |
| :--- | :--- | :--- |
| `ret` | **OTP Generation Outcome** (`y` or `n`). | **Primary flag for OTP dispatch success.** |
| `txn` | Transaction ID. | Must match the `txn` submitted in Step 4. |
| `err` | Error code populated if `ret="n"`. | Read to determine cause of failure. |
| `code` | Unique response reference code. | Store for auditing and logging. |
| `ts` | Response generation timestamp. | Validate transaction timing. |

---

## 3. Outcome Verification Rules

> ⚠️ **HTTP 200 ≠ OTP Sent**
> - **`HTTP 200 OK`** confirms network connectivity to the AUA gateway.
> - **`<res_code> 10`** indicates gateway processing was completed.
> - **`<OtpRes ret="y">`** is the **only** confirmation that UIDAI dispatched the OTP to the resident's registered mobile number/email.

### Result Handling

* **Success (`ret="y"`)**: 
  - Prompt the user to enter the OTP received on their mobile/email.
  - Retain the transaction ID (`txn`) to link with the subsequent authentication step.
* **Failure (`ret="n"`)**: 
  - Extract the `err` code.
  - Display a user-friendly error message based on the error code.
  - Allow the user to retry after an appropriate wait interval.

---

## Response Validation Checklist

- [ ] **HTTP Status:** Verify HTTP `200 OK`.
- [ ] **Outer Envelope:** Confirm `client_id` matches credentials and `res_code` indicates gateway success.
- [ ] **Payload Extraction:** Successfully Base64-decode `<res_data>`.
- [ ] **Transaction Correlation:** Ensure inner `txn` matches the request `txn`.
- [ ] **Outcome Check:** Evaluate `ret="y"` vs `ret="n"`.
- [ ] **Logging Hygiene:** 
  - Do **NOT** log sensitive payload values or decoded XML content.
  - Log only metadata (`txn`, `ts`, `err`, `code`) for troubleshooting.