---
sidebar_position: 8
---

# Step 8 - Receive and Process Authentication Response

## Overview

After sending a Biometric Authentication Request, the Sub-AUA receives a two-layered response:

1. **Outer AUA Response**: Contains transaction wrapper fields (`client_id`, `res_code`, `res_hash`, `res_data`, `res_msg`).
2. **Inner Authentication XML (`<AuthRes>`)**: Base64-encoded XML payload inside the `res_data` field.

To determine final authentication success, the Sub-AUA must parse the outer response, decode `res_data`, and check the `<AuthRes>` XML.

---

## Response Processing Workflow

```text
Receive HTTP Response (200 OK)
            │
            ▼
 Parse Outer AUA XML (<xml>)
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
  Verify XML Signature
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

## 1. Outer AUA Response Structure

The AUA returns a response wrapped inside an `<xml>` element.

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


### Outer Response Fields

| Field | Description |
| :--- | :--- |
| `client_id` | Sub-AUA client identifier. Verify this matches expected config. |
| `res_code` | Response code from AUA layer (e.g., `10`). |
| `res_hash` | Hash associated with response data for verification. |
| `res_data` | **Base64-encoded** Authentication Response XML (`<AuthRes>`). |
| `res_msg` | Status message returned by the AUA. |

---

## 2. Inner Authentication Response (`<AuthRes>`)

Decode the Base64 value inside `<res_data>` to get the actual authentication response:

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

### Key `<AuthRes>` Attributes

| Attribute | Description | Action Required |
| :--- | :--- | :--- |
| `ret` | **Final authentication result** (`y` or `n`). | **Primary flag for success/failure.** |
| `txn` | Transaction ID. | Match with original request `txn` for correlation. |
| `code` | Authentication response code. | Store for auditing/tracking. |
| `ts` | Response timestamp. | Validate for auditing. |
| `info` | Additional metadata. | Treat as sensitive; do not expose to end users. |

---

## 3. Critical Verification Rules

> ⚠️ **HTTP 200 ≠ Authentication Success**
> - **`HTTP 200`** only indicates the API request reached the server.
> - **`<res_code> 10`** only indicates success at the AUA gateway layer.
> - **`<AuthRes ret="y">`** is the **only** indicator of a successful authentication.

### Result Handling

* **Success (`ret="y"`)**: Continue business logic, grant service, log transaction ID (`txn`).
* **Failure (`ret="n"`)**: Read error response, display appropriate resident message, allow retry if permitted.
* **Signature Verification**: Validate the XML `<Signature>` inside `<AuthRes>` to ensure data integrity.

---

## Processing Checklist & Best Practices

- [ ] **Validate HTTP Status:** Confirm HTTP `200`.
- [ ] **Parse Outer XML:** Verify `client_id`, `res_code`, and `res_hash`.
- [ ] **Decode Payload:** Base64 decode `<res_data>` to reveal `<AuthRes>`.
- [ ] **Verify Signature:** Validate the digital signature on `<AuthRes>`.
- [ ] **Correlate Transaction:** Confirm response `txn` matches request `txn`.
- [ ] **Check Authentication Result:** Evaluate `ret="y"` vs `ret="n"`.
- [ ] **Security & Privacy:**
  - Never log raw biometric data, PID XML, session keys, or unencrypted `info`.
  - Retain only minimum transaction metadata needed for compliance/auditing.
