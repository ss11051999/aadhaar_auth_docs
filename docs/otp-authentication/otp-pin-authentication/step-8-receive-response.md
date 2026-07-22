---
sidebar_position: 8
---

# Step 8 - Receive and Process Authentication Response

## Goal

Parse and validate the response returned by the Aadhaar Authentication Server. The response uses a two-layered structure: an outer AUA transport wrapper (`<xml>`) containing a Base64-encoded inner authentication response (`<AuthRes>`).

---

## Response Processing Workflow

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
 Base64 Decode <res_data>
            │
            ▼
 Parse Inner XML (<AuthRes>)
            │
            ▼
 Check 'ret' Attribute
            │
      ┌─────┴─────┐
      ▼           ▼
   ret="y"     ret="n"
      │           │
      ▼           ▼
   SUCCESS     FAILURE
```

---

## 1. Outer Response Structure

The server returns an outer XML wrapper containing gateway metadata and the payload.

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

| Element | Description |
| :--- | :--- |
| `client_id` | Identifier of the client/AUA that initiated the request. |
| `res_code` | Gateway response code returned by the intermediary/AUA (e.g., `10`). |
| `res_hash` | Hash value for response payload integrity verification. |
| `res_data` | **Base64-encoded** inner Authentication Response XML (`<AuthRes>`). |
| `res_msg` | Status message from the gateway. |

---

## 2. Decoded Inner Response (`<AuthRes>`)

Extracting and Base64-decoding `<res_data>` yields the root `<AuthRes>` element containing the actual authentication result.

### Example Decoded XML

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<AuthRes
    code="51047f1e0ff5494bacd35a17b66d0de9"
    info="04{...}"
    ret="y"
    ts="2024-05-02T11:42:39.187+05:30"
    txn="2405021142340169818591290pmg">

    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        ...
    </Signature>
</AuthRes>
```

### Key Attributes

| Attribute | Description | Action Required |
| :--- | :--- | :--- |
| `ret` | **Final authentication result** (`y` or `n`). | **Primary flag for success or failure.** |
| `txn` | Transaction ID. | Must match the original request `txn` for correlation. |
| `code` | Authentication/response code. | Retain for auditing and tracking. |
| `ts` | Response generation timestamp. | Validate for transaction logging. |
| `info` | Additional authentication/device metadata. | Treat as sensitive; do not display to end users. |

---

## 3. Critical Verification Rules

> ⚠️ **HTTP 200 ≠ Authentication Success**
> - **`HTTP 200 OK`** only indicates transport-level success.
> - **`<res_code> 10`** only indicates gateway-level processing success.
> - **`<AuthRes ret="y">`** is the **only** valid indicator of successful Aadhaar authentication.

### Result Handling

* **Success (`ret="y"`)**: Continue business logic, grant requested service, log transaction ID (`txn`).
* **Failure (`ret="n"`)**: Extract response/error code, display a user-friendly error message, allow retry if permitted.

---

## Response Validation Checklist

- [ ] **HTTP Status:** Confirm HTTP `200 OK` before parsing body.
- [ ] **Outer XML Parsing:** Validate `client_id`, `res_code`, and `res_hash`.
- [ ] **Payload Extraction:** Extract and Base64-decode `res_data`.
- [ ] **Inner XML Parsing:** Parse decoded `<AuthRes>` XML and verify its signature.
- [ ] **Transaction Correlation:** Verify that response `txn` matches request `txn`.
- [ ] **Result Verification:** Determine outcome strictly from `ret="y"` vs `ret="n"`.
- [ ] **Security & Compliance:**
  - Never log raw PID XML, biometric data, session keys, or unencrypted `info` attributes.
  - Retain only minimum required transaction metadata (`txn`, `ts`, `code`) for audit logs.