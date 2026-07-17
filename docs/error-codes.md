---
sidebar_position: 11
---

# Error Codes

## Overview

During Aadhaar Authentication, the Authentication Server validates the request and processes the resident's authentication data.

If any validation fails or the authentication cannot be completed, the server returns an **Authentication Response XML (`AuthRes`)** containing an **error code**.

The error code helps identify the reason for the failure and enables developers to implement appropriate error handling and user guidance.

---

## Response Example

```xml
<AuthRes
    ret="N"
    err="400"
    txn="AUTH202607171530001"
    ts="2026-07-17T15:30:05"/>
```

If:

* `ret="Y"` → Authentication Successful
* `ret="N"` → Authentication Failed (Check the `err` attribute)

---

## Response Attributes

| Attribute | Description                                                  |
| --------- | ------------------------------------------------------------ |
| `ret`     | Authentication result (`Y` = Success, `N` = Failure)         |
| `err`     | Authentication error code                                    |
| `txn`     | Transaction ID                                               |
| `ts`      | Response timestamp                                           |
| `code`    | Success code (when applicable)                               |
| `info`    | Additional information returned by the server (if available) |

---

# Authentication Error Codes

| Code    | Description                                | Recommended Action                                       |
| ------- | ------------------------------------------ | -------------------------------------------------------- |
| **100** | Personal Identity attributes did not match | Verify the resident's demographic details.               |
| **200** | Address attributes did not match           | Verify the resident's address information.               |
| **300** | Biometric data did not match               | Capture biometric data again and retry authentication.   |
| **310** | Duplicate fingers used                     | Capture unique fingerprints only.                        |
| **311** | Duplicate iris captured                    | Capture each iris only once.                             |
| **312** | FMR and FIR cannot be used together        | Use only one fingerprint format.                         |
| **313** | FIR contains multiple fingerprints         | Capture one fingerprint per FIR record.                  |
| **314** | More than 10 fingerprint records           | Reduce fingerprint records to the supported limit.       |
| **315** | More than 2 iris records                   | Submit a maximum of two iris records.                    |
| **316** | More than one face record                  | Submit only one face image.                              |
| **330** | Biometrics locked by resident              | Ask the resident to unlock biometrics through UIDAI.     |
| **400** | Invalid OTP                                | Ask the resident to enter the correct OTP.               |
| **402** | OTP transaction does not match             | Generate a new OTP and retry authentication.             |
| **500** | Invalid Session Key encryption             | Verify the Session Key encryption process.               |
| **501** | Invalid Certificate Identifier (`ci`)      | Verify the certificate identifier used in `<Skey>`.      |
| **502** | Invalid PID encryption                     | Verify PID XML encryption.                               |
| **503** | Invalid HMAC encryption                    | Regenerate the HMAC correctly.                           |
| **504** | Session Key expired                        | Generate a new Session Key and retry.                    |
| **505** | Synchronized key usage not allowed         | Verify AUA license configuration.                        |
| **510** | Invalid Authentication XML                 | Validate the Authentication Request XML.                 |
| **511** | Invalid PID XML                            | Validate the generated PID XML.                          |
| **512** | Invalid resident consent value             | Verify the consent (`rc`) attribute.                     |
| **520** | Invalid Terminal ID                        | Verify the configured Terminal ID.                       |
| **521** | Invalid Device Code                        | Verify the device code in the request.                   |
| **524** | Invalid Device Model Information           | Verify the metadata supplied by the device.              |
| **527** | Invalid Device Certificate                 | Verify the registered device certificate.                |
| **530** | Invalid Authenticator Code                 | Verify the configured AUA credentials.                   |
| **540** | Invalid Authentication API version         | Use a supported API version.                             |
| **541** | Invalid PID XML version                    | Use the supported PID version.                           |
| **542** | AUA not authorized for ASA                 | Contact your ASA administrator.                          |
| **543** | Sub-AUA not associated with AUA            | Verify Sub-AUA configuration.                            |
| **550** | Invalid Uses element                       | Verify the `<Uses>` attributes.                          |
| **551** | Invalid Transaction ID                     | Generate a unique transaction ID.                        |
| **553** | Registered Device not supported            | Verify RD Service compatibility.                         |
| **554** | Public Device not allowed                  | Use a registered biometric device.                       |
| **555** | Invalid RD Service ID                      | Verify `rdsId`.                                          |
| **556** | Invalid RD Service Version                 | Verify `rdsVer`.                                         |
| **557** | Invalid Device Provider ID                 | Verify `dpId`.                                           |
| **558** | Invalid Device Hash                        | Verify `dih`.                                            |
| **559** | Device Certificate expired                 | Renew the device certificate.                            |
| **560** | Device Provider Certificate expired        | Renew the provider certificate.                          |
| **561** | Request expired                            | Generate a fresh request and retry.                      |
| **562** | Future timestamp                           | Verify the system clock.                                 |
| **563** | Duplicate authentication request           | Generate a new transaction ID and resend.                |
| **564** | HMAC validation failed                     | Regenerate the HMAC using the correct PID XML.           |
| **579** | OTP authentication not allowed             | Verify the AUA license configuration.                    |
| **580** | PIN authentication not allowed             | Verify the AUA license configuration.                    |
| **581** | Fuzzy matching not allowed                 | Check your authentication configuration.                 |
| **582** | Local language usage not allowed           | Verify language configuration.                           |
| **586** | Face authentication not allowed            | Verify the AUA license permissions.                      |
| **587** | Namespace not allowed                      | Verify the XML namespace.                                |
| **588** | Registered Device not allowed              | Verify your AUA license.                                 |
| **590** | Public Device not allowed                  | Use an approved Registered Device.                       |
| **710** | Missing Personal Identity (`Pi`) data      | Include the required demographic information.            |
| **720** | Missing Address (`Pa`) data                | Include the required address information.                |
| **721** | Missing Full Address (`Pfa`) data          | Include the required full address.                       |
| **730** | Missing PIN data                           | Include the resident PIN if required.                    |
| **740** | Missing OTP                                | Generate and include the OTP.                            |
| **800** | Invalid biometric data                     | Capture biometrics again.                                |
| **810** | Missing biometric data                     | Include biometric data in the PID XML.                   |
| **811** | Resident has no biometric data             | Use another authentication factor if available.          |
| **812** | Best Finger Detection not completed        | Complete Best Finger Detection before authentication.    |
| **820** | Missing biometric type (`bt`)              | Include the biometric type.                              |
| **821** | Invalid biometric type (`bt`)              | Verify the biometric type value.                         |
| **822** | Invalid biometric subtype (`bs`)           | Verify the biometric subtype.                            |
| **901** | No authentication data found               | Verify the request payload.                              |
| **902** | Invalid Date of Birth                      | Verify the DOB format and value.                         |
| **910** | Invalid matching value (`mv`) in `Pi`      | Verify demographic matching configuration.               |
| **911** | Invalid matching value (`mv`) in `Pfa`     | Verify full address matching configuration.              |
| **912** | Invalid matching strategy (`ms`)           | Verify matching strategy configuration.                  |
| **913** | Both `Pa` and `Pfa` supplied               | Use either `Pa` or `Pfa`, not both.                      |
| **930** | Technical error                            | Retry after some time.                                   |
| **931** | Technical error                            | Retry after some time.                                   |
| **932** | Technical error                            | Retry after some time.                                   |
| **933** | Technical error                            | Retry after some time.                                   |
| **934** | Technical error                            | Retry after some time.                                   |
| **935** | Technical error                            | Retry after some time.                                   |
| **936** | Technical error                            | Retry after some time.                                   |
| **937** | Technical error                            | Retry after some time.                                   |
| **938** | Technical error                            | Retry after some time.                                   |
| **939** | Technical error                            | Retry after some time.                                   |
| **940** | Unauthorized ASA Channel                   | Verify the configured ASA channel.                       |
| **941** | Unknown ASA Channel                        | Verify ASA configuration.                                |
| **950** | OTP service error                          | Retry or contact your service provider.                  |
| **951** | Biometric lock service error               | Retry later.                                             |
| **980** | Unsupported option                         | Remove or correct the unsupported option.                |
| **995** | Aadhaar suspended                          | Contact UIDAI for assistance.                            |
| **996** | Aadhaar cancelled                          | Authentication cannot be performed.                      |
| **997** | Aadhaar suspended                          | Authentication cannot be performed until reactivated.    |
| **998** | Invalid Aadhaar Number                     | Verify the Aadhaar Number.                               |
| **999** | Unknown error                              | Retry the request or contact your AUA/ASA administrator. |

---

# Best Practices

* Always check the `ret` attribute before processing the response.
* Never rely solely on the HTTP status code.
* Display user-friendly messages instead of raw error codes.
* Log the Transaction ID for troubleshooting.
* Do not log Aadhaar Numbers, OTPs, biometric data, PID XML, Session Keys, or HMAC values.
* Retry requests only for temporary network or server errors.

---

# Notes

> The descriptions above are intended to help developers understand and troubleshoot common authentication failures. Always refer to the latest UIDAI Authentication API specification and your AUA/ASA implementation guidelines for the most up-to-date definitions and handling requirements, as error codes and behaviors may change between API versions.
