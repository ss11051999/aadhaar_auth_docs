---
sidebar_position: 8
---

# Step 8 - Receive and Process Authentication Response

## Goal

Parse and process the **Sub-AUA Response XML** returned by the AUA server following a Multi-Factor Authentication attempt. The response contains an outer transport XML wrapper (`<xml>`) housing a Base64-encoded inner Authentication Response XML (`<AuthRes>`).

Your application must:

1. Validate transport-level HTTP status.
2. Parse the outer Sub-AUA XML wrapper.
3. Extract and Base64-decode the inner payload (`res_data`).
4. Evaluate inner `<AuthRes>` attributes to determine the ultimate authentication outcome.

---

## Response Processing Flow

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
 Parse Inner XML (<AuthRes>)
            │
            ▼
 Validate Transaction ID (txn)
            │
            ▼
 Check 'ret' Attribute
            │
      ┌─────┴─────┐
      ▼           ▼
   ret="y"     ret="n"
      │           │
      ▼           ▼
 Authentication  Auth Failed /
   Successful    Handle Error
```

---

## 1. Outer Response Structure

The AUA server wraps the authentication result inside an outer transport envelope.

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

**OR**

```xml
<?xml version="1.0" encoding="utf-8"?> 
<xml>
	<client_id>AUA-CHP</client_id>
	<res_code>10</res_code>
	<res_hash>UbHZv3KToV6b8xjg/jHXdzXhajbNDKKKD2ZlHMOk9xbio/ZZAN8q3jYIY2ayg2uL1PgbkHWZF0QW1i4nCq58ysD6n6XblFFgybSP/dgSOoM=</res_hash>
	<res_data>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiIHN0YW5kYWxvbmU9Im5vIj8+PEF1dGhSZXMgY29kZT0iNTEwNDdmMWUwZmY1NDk0YmFjZDM1YTE3YjY2ZDBkZTkiIGluZm89IjA0ezAxMDAwODA0Z3pGQ3g2Y3Z3WnBGbnZYVmF4T1N4VmJDMURlU05LRDExMXVXaFJvZXJxMEpCb0FYb2cvdThpSU9Lc0NQNTE2bixBLGUzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUsMDEwMDAwMzAwMDAwMDIxMCwyLjAsMjAyNDA1MDIxMTQyMzIsMSwxLDAsMCwyLjUsZjE4ODcwODAwMDEyNzIzN2U0OTJmODdkNTQxZWJmODc4NTAyMTI3NjAwMGQwOWU1NGZkOThiNjQwNzZlMmRjZCwxMGM3MzFhOTIyZGVjY2RlODYwZjkyZjdjZGEwNGQ4MzE4Y2MwMzMyYzhiY2I5ZThjYTMwZDMzYzNmNTIzYWM1LDEwYzczMWE5MjJkZWNjZGU4NjBmOTJmN2NkYTA0ZDgzMThjYzAzMzJjOGJjYjllOGNhMzBkMzNjM2Y1MjNhYzUsMjMsTkEsTkEsTkEsTkEsTkEsTkEsTkEsTkEsTkEscmVnaXN0ZXJlZCxBQ1BMLldJTi4wMDEsMS4wLjQsU1RBUlRFSy5BQ1BMLEZNMjIwVSxMMCxOQX0iIHJldD0ieSIgdHM9IjIwMjQtMDUtMDJUMTE6NDI6MzkuMTg3KzA1OjMwIiB0eG49IjI0MDUwMjExNDIzNDAxNjk4MTg1OTEyOTBwbWciPjxTaWduYXR1cmUgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvMDkveG1sZHNpZyMiPjxTaWduZWRJbmZvPjxDYW5vbmljYWxpemF0aW9uTWV0aG9kIEFsZ29yaXRobT0iaHR0cDovL3d3dy53My5vcmcvVFIvMjAwMS9SRUMteG1sLWMxNG4tMjAwMTAzMTUiLz48U2lnbmF0dXJlTWV0aG9kIEFsZ29yaXRobT0iaHR0cDovL3d3dy53My5vcmcvMjAwMC8wOS94bWxkc2lnI3JzYS1zaGExIi8+PFJlZmVyZW5jZSBVUkk9IiI+PFRyYW5zZm9ybXM+PFRyYW5zZm9ybSBBbGdvcml0aG09Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvMDkveG1sZHNpZyNlbnZlbG9wZWQtc2lnbmF0dXJlIi8+PC9UcmFuc2Zvcm1zPjxEaWdlc3RNZXRob2QgQWxnb3JpdGhtPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGVuYyNzaGEyNTYiLz48RGlnZXN0VmFsdWU+a2VXdlFnQ25MSVhiVzZlcEg5NksvOEtXdXRhb2FnMGVOYjMybFF5ZEdtbz08L0RpZ2VzdFZhbHVlPjwvUmVmZXJlbmNlPjwvU2lnbmVkSW5mbz48U2lnbmF0dXJlVmFsdWU+YzNWcDdqWGlmMldaa05vL0lBejhMNUw5bzdPUnV5dXNHQXhuTk1JOUY1eWM5bzVlSlhuSFJoRmh4K0hncGJLcGZraytYb3lvZFBGOQp3em5kWGRnVU5PeVBjZGdmM3VqbktpcU1yVzhrZlUvYVpGTkZuRGlBWGFKTE8vMUplL0pCZzEvU2xCcHJCNUZMYXE4N0xuZ1pxM0NiCjJVbU0zVVFPdysraEgzaWJSWlZ4a25sUEk1TUN0NjRCcThWd1FsUTgxc1VqVVNIR003TDgrZG1uY3pSNnlBNng4TFR4OVdINmlTRWMKOS9EYWgyczhHanJVSjJRUTJmZWl3alcwQ2UwSCtpSjVNVWUvWWZzTW1ua1VQUExXZmxibVg1RE5iY3BSMHl1MURsOU1MYnM4WVFMRApHek91SzBUYzdIaDYvbzhjTmpwUDZKbjFuTHVFTXgwdDM2ZkZXQT09PC9TaWduYXR1cmVWYWx1ZT48L1NpZ25hdHVyZT48L0F1dGhSZXM+</res_data>
	<res_msg>Success from CSC AAG.</res_msg>
</xml>
```

| Field | Description |
| :--- | :--- |
| `client_id` | Identifier of the Sub-AUA/client that initiated the request. |
| `res_code` | Status code from gateway layer (`10` indicates processing completed). |
| `res_hash` | Hash value for response integrity verification. |
| `res_data` | **Base64-encoded** inner Authentication Response XML (`<AuthRes>`). |
| `res_msg` | Descriptive gateway status message. |

---

## 2. Decoded Inner Response (`<AuthRes>`)

Base64-decoding the content of `<res_data>` produces the UIDAI Authentication response document (`<AuthRes>`).

### Example Decoded XML

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<AuthRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    err=""
    info="04{...}"
    ret="y"
    ts="2026-07-22T11:42:39.187+05:30"
    txn="2607221142340169818591290pmg">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>
</AuthRes>
```

### Key Attributes

| Attribute | Description | Action Required |
| :--- | :--- | :--- |
| `ret` | **Authentication Outcome** (`y` or `n`). | **Primary flag for identity validation success.** |
| `txn` | Transaction ID. | Must strictly match the `txn` submitted in Step 7. |
| `err` | Error code populated if `ret="n"`. | Read to determine specific failure cause. |
| `code` | Unique response reference code. | Retain for auditing and log correlation. |
| `ts` | Response timestamp. | Validate transaction timing window. |

---

## 3. Outcome Verification Rules

> ⚠️ **HTTP 200 ≠ Authentication Success**
> - **`HTTP 200 OK`** confirms transport/network connectivity.
> - **`<res_code> 10`** confirms gateway processing completed.
> - **`<AuthRes ret="y">`** is the **only** valid confirmation that UIDAI successfully verified the identity request.

### Result Handling

* **Success (`ret="y"`)**: 
  - Proceed with the application workflow (e.g., grant access, issue credential).
  - Store non-sensitive metadata (`txn`, `ts`, `code`) for audit records.
* **Failure (`ret="n"`)**: 
  - Read the `err` code to determine failure cause (e.g., biometric mismatch, expired OTP, invalid PIN).
  - Present a user-friendly error message.
  - Do **not** automatically retry without user intervention or factor correction.

---

## Pre-Flight & Processing Checklist

- [ ] **HTTP Status:** Verify HTTP `200 OK`.
- [ ] **Outer Envelope:** Confirm `client_id` matches credentials and `res_code` indicates gateway success.
- [ ] **Payload Extraction:** Successfully Base64-decode `<res_data>`.
- [ ] **Transaction Correlation:** Ensure inner `txn` exactly matches the request `txn`.
- [ ] **Outcome Check:** Evaluate `ret="y"` vs `ret="n"`.
- [ ] **Logging Hygiene:** 
  - Do **NOT** log sensitive payload values, PID XML, session keys (`Skey`), HMACs, biometric data, or complete raw `info` strings.
  - Log only metadata (`txn`, `ts`, `err`, `code`) for operational troubleshooting.