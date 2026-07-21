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

| S. No. | Code    | Description                                | Recommended Action                                       |
| ------ | ------- | ------------------------------------------ | -------------------------------------------------------- |
| 01     | **100** | Personal Identity attributes did not match | Verify the resident's demographic details.               |
| 02     | **200** | Address attributes did not match           | Verify the resident's address information.               |
| 03     | **300** | Biometric data did not match               | Capture biometric data again and retry authentication.   |
| 04     | **310** | Duplicate fingers used                     | Capture unique fingerprints only.                        |
| 05     | **311** | Duplicate iris captured                    | Capture each iris only once.                             |
| 06     | **312** | FMR and FIR cannot be used together        | Use only one fingerprint format.                         |
| 07     | **313** | FIR contains multiple fingerprints         | Capture one fingerprint per FIR record.                  |
| 08     | **314** | More than 10 fingerprint records           | Reduce fingerprint records to the supported limit.       |
| 09     | **315** | More than 2 iris records                   | Submit a maximum of two iris records.                    |
| 10     | **316** | More than one face record                  | Submit only one face image.                              |
| 11     | **330** | Biometrics locked by resident              | Ask the resident to unlock biometrics through UIDAI.     |
| 12     | **400** | Invalid OTP                                | Ask the resident to enter the correct OTP.               |
| 13     | **402** | OTP transaction does not match             | Generate a new OTP and retry authentication.             |
| 14     | **500** | Invalid Session Key encryption             | Verify the Session Key encryption process.               |
| 15     | **501** | Invalid Certificate Identifier (`ci`)      | Verify the certificate identifier used in `<Skey>`.      |
| 16     | **502** | Invalid PID encryption                     | Verify PID XML encryption.                               |
| 17     | **503** | Invalid HMAC encryption                    | Regenerate the HMAC correctly.                           |
| 18     | **504** | Session Key expired                        | Generate a new Session Key and retry.                    |
| 19     | **505** | Synchronized key usage not allowed         | Verify AUA license configuration.                        |
| 20     | **510** | Invalid Authentication XML                 | Validate the Authentication Request XML.                 |
| 21     | **511** | Invalid PID XML                            | Validate the generated PID XML.                          |
| 22     | **512** | Invalid resident consent value             | Verify the consent (`rc`) attribute.                     |
| 23     | **520** | Invalid Terminal ID                        | Verify the configured Terminal ID.                       |
| 24     | **521** | Invalid Device Code                        | Verify the device code in the request.                   |
| 25     | **524** | Invalid Device Model Information           | Verify the metadata supplied by the device.              |
| 26     | **527** | Invalid Device Certificate                 | Verify the registered device certificate.                |
| 27     | **530** | Invalid Authenticator Code                 | Verify the configured AUA credentials.                   |
| 28     | **540** | Invalid Authentication API version         | Use a supported API version.                             |
| 29     | **541** | Invalid PID XML version                    | Use the supported PID version.                           |
| 30     | **542** | AUA not authorized for ASA                 | Contact your ASA administrator.                          |
| 31     | **543** | Sub-AUA not associated with AUA            | Verify Sub-AUA configuration.                            |
| 32     | **550** | Invalid Uses element                       | Verify the `<Uses>` attributes.                          |
| 33     | **551** | Invalid Transaction ID                     | Generate a unique transaction ID.                        |
| 34     | **553** | Registered Device not supported            | Verify RD Service compatibility.                         |
| 35     | **554** | Public Device not allowed                  | Use a registered biometric device.                       |
| 36     | **555** | Invalid RD Service ID                      | Verify `rdsId`.                                          |
| 37     | **556** | Invalid RD Service Version                 | Verify `rdsVer`.                                         |
| 38     | **557** | Invalid Device Provider ID                 | Verify `dpId`.                                           |
| 39     | **558** | Invalid Device Hash                        | Verify `dih`.                                            |
| 40     | **559** | Device Certificate expired                 | Renew the device certificate.                            |
| 41     | **560** | Device Provider Certificate expired        | Renew the provider certificate.                          |
| 42     | **561** | Request expired                            | Generate a fresh request and retry.                      |
| 43     | **562** | Future timestamp                           | Verify the system clock.                                 |
| 44     | **563** | Duplicate authentication request           | Generate a new transaction ID and resend.                |
| 45     | **564** | HMAC validation failed                     | Regenerate the HMAC using the correct PID XML.           |
| 46     | **579** | OTP authentication not allowed             | Verify the AUA license configuration.                    |
| 47     | **580** | PIN authentication not allowed             | Verify the AUA license configuration.                    |
| 48     | **581** | Fuzzy matching not allowed                 | Check your authentication configuration.                 |
| 49     | **582** | Local language usage not allowed           | Verify language configuration.                           |
| 50     | **586** | Face authentication not allowed            | Verify the AUA license permissions.                      |
| 51     | **587** | Namespace not allowed                      | Verify the XML namespace.                                |
| 52     | **588** | Registered Device not allowed              | Verify your AUA license.                                 |
| 53     | **590** | Public Device not allowed                  | Use an approved Registered Device.                       |
| 54     | **710** | Missing Personal Identity (`Pi`) data      | Include the required demographic information.            |
| 55     | **720** | Missing Address (`Pa`) data                | Include the required address information.                |
| 56     | **721** | Missing Full Address (`Pfa`) data          | Include the required full address.                       |
| 57     | **730** | Missing PIN data                           | Include the resident PIN if required.                    |
| 58     | **740** | Missing OTP                                | Generate and include the OTP.                            |
| 59     | **800** | Invalid biometric data                     | Capture biometrics again.                                |
| 60     | **810** | Missing biometric data                     | Include biometric data in the PID XML.                   |
| 61     | **811** | Resident has no biometric data             | Use another authentication factor if available.          |
| 62     | **812** | Best Finger Detection not completed        | Complete Best Finger Detection before authentication.    |
| 63     | **820** | Missing biometric type (`bt`)              | Include the biometric type.                              |
| 64     | **821** | Invalid biometric type (`bt`)              | Verify the biometric type value.                         |
| 65     | **822** | Invalid biometric subtype (`bs`)           | Verify the biometric subtype.                            |
| 66     | **901** | No authentication data found               | Verify the request payload.                              |
| 67     | **902** | Invalid Date of Birth                      | Verify the DOB format and value.                         |
| 68     | **910** | Invalid matching value (`mv`) in `Pi`      | Verify demographic matching configuration.               |
| 69     | **911** | Invalid matching value (`mv`) in `Pfa`     | Verify full address matching configuration.              |
| 70     | **912** | Invalid matching strategy (`ms`)           | Verify matching strategy configuration.                  |
| 71     | **913** | Both `Pa` and `Pfa` supplied               | Use either `Pa` or `Pfa`, not both.                      |
| 72     | **930** | Technical error                            | Retry after some time.                                   |
| 73     | **931** | Technical error                            | Retry after some time.                                   |
| 74     | **932** | Technical error                            | Retry after some time.                                   |
| 75     | **933** | Technical error                            | Retry after some time.                                   |
| 76     | **934** | Technical error                            | Retry after some time.                                   |
| 77     | **935** | Technical error                            | Retry after some time.                                   |
| 78     | **936** | Technical error                            | Retry after some time.                                   |
| 79     | **937** | Technical error                            | Retry after some time.                                   |
| 80     | **938** | Technical error                            | Retry after some time.                                   |
| 81     | **939** | Technical error                            | Retry after some time.                                   |
| 82     | **940** | Unauthorized ASA Channel                   | Verify the configured ASA channel.                       |
| 83     | **941** | Unknown ASA Channel                        | Verify ASA configuration.                                |
| 84     | **950** | OTP service error                          | Retry or contact your service provider.                  |
| 85     | **951** | Biometric lock service error               | Retry later.                                             |
| 86     | **980** | Unsupported option                         | Remove or correct the unsupported option.                |
| 87     | **995** | Aadhaar suspended                          | Contact UIDAI for assistance.                            |
| 88     | **996** | Aadhaar cancelled                          | Authentication cannot be performed.                      |
| 89     | **997** | Aadhaar suspended                          | Authentication cannot be performed until reactivated.    |
| 90     | **998** | Invalid Aadhaar Number                     | Verify the Aadhaar Number.                               |
| 91     | **999** | Unknown error                              | Retry the request or contact your AUA/ASA administrator. |

---


---

# Additional Authentication Error Codes

The following error codes are also applicable to Aadhaar authentication and related services.

| S. No. | Code | Description |
| ------ | ---- | ----------- |
| 01 | **110** | Aadhaar number does not have email ID |
| 02 | **111** | Aadhaar number does not have mobile number |
| 03 | **112** | Aadhaar number does not have email ID or mobile number |
| 04 | **113** | Aadhaar number does not have verified email ID |
| 05 | **114** | Aadhaar number does not have verified mobile number |
| 06 | **115** | Aadhaar number does not have verified email and mobile |
| 07 | **318** | BFD transaction should not contain other modalities in input |
| 08 | **319** | Reserved / not specified |
| 09 | **320** | Reserved / not specified |
| 10 | **321** | Reserved / not specified |
| 11 | **322** | Reserved / not specified |
| 12 | **323** | Reserved / not specified |
| 13 | **324** | Reserved / not specified |
| 14 | **325** | Reserved / not specified |
| 15 | **326** | Reserved / not specified |
| 16 | **327** | Reserved / not specified |
| 17 | **328** | Reserved / not specified |
| 18 | **329** | Reserved / not specified |
| 19 | **331** | Aadhaar locked by Aadhaar number holder for all authentications |
| 20 | **332** | Aadhaar number usage is blocked by Aadhaar number holder |
| 21 | **430** | TOTP usage is not allowed for this Aadhaar holder. Please install m-Aadhaar and generate TOTP |
| 22 | **513** | Invalid Protobuf format |
| 23 | **514** | Invalid UID Token in input |
| 24 | **515** | Invalid VID number in input |
| 25 | **517** | Expired VID is used in input |
| 26 | **522** | Invalid `type` attribute |
| 27 | **523** | Invalid `ts` attribute |
| 28 | **528** | Device key rotation related issue |
| 29 | **549** | Invalid API version |
| 30 | **552** | Invalid Wadh element |
| 31 | **556** | Invalid `rdsVer` and not part of certification registry |
| 32 | **557** | Invalid `dpId` and not part of certification registry |
| 33 | **558** | Invalid `dih` |
| 34 | **565** | AUA license key has expired or is invalid |
| 35 | **566** | ASA license key has expired or is invalid |
| 36 | **567** | Invalid input |
| 37 | **568** | Unsupported language |
| 38 | **569** | Digital signature verification failed |
| 39 | **570** | Invalid key information in digital signature / invalid digital certificate |
| 40 | **571** | PIN requires reset |
| 41 | **572** | Invalid biometric position |
| 42 | **573** | `Pi` not allowed |
| 43 | **574** | `Pa` not allowed |
| 44 | **575** | `Pfa` not allowed |
| 45 | **576** | FMR not allowed |
| 46 | **577** | FIR not allowed |
| 47 | **578** | IIR not allowed |
| 48 | **583** | Reserved / not specified |
| 49 | **584** | Reserved / not specified |
| 50 | **585** | Reserved / not specified |
| 51 | **588** | Registered device not allowed as per license |
| 52 | **591** | BFD usage is not allowed as per license |
| 53 | **700** | Invalid demographic data |
| 54 | **950** | Could not generate and/or send OTP |
| 55 | **952** | OTP flooding error |
| 56 | **953** | OTP flooding error |
| 57 | **953** | OTP flooding error due to repeated OTP generation without successful submission |
| 58 | **953** | OTP store related technical error |
| 59 | **403** | Maximum number of OTP match attempts exceeded or OTP was not generated. Generate a fresh OTP and try again |
| 60 | **994** | Aadhaar deactivated due to deceased status |
| 61 | **531** | Invalid Sub-AUA Code |
| 62 | **594** | Biometric device is in blocked state |
| 63 | **992 / T992** | Aadhaar status is suspended due to pending Mandatory Biometric Update (MBU) |
| 64 | **915** | Biometric authentication attempted for a child below 5 years of age |
| 65 | **1605** | Organization continues to operate in the Pre-Production environment for more than 90 days without migrating to Production |
| 66 | **1606** | Entity exceeded the maximum permitted transaction limit of 500 transactions in the Pre-Production environment for an authentication modality |
| 67 | **K-100** | Resident authentication failed |
| 68 | **K-200** | Resident data currently not available |
| 69 | **K-514** | Invalid UID Token used |
| 70 | **K-515** | Invalid VID used |
| 71 | **K-516** | Invalid ANCS Token used |
| 72 | **K-517** | VID used is expired |
| 73 | **K-519** | Invalid Authenticator Code |
| 74 | **K-540** | Invalid KYC XML |
| 75 | **K-541** | Invalid e-KYC API version |
| 76 | **K-542** | Invalid resident consent (`rc`) sent in KYC element |
| 77 | **K-544** | Resident authentication type (`ra`) in KYC element does not match the authentication type in the PID block |
| 78 | **K-545** | Resident has opted out of this service; feature is currently not implemented |
| 79 | **K-546** | Invalid value for `pfr` attribute |
| 80 | **K-547** | Invalid value for `wadh` attribute within PID block |
| 81 | **K-550** | Invalid Uses attribute |
| 82 | **K-551** | Invalid transaction namespace |
| 83 | **K-552** | Invalid KUA license key |
| 84 | **K-553** | KUA license key expired |
| 85 | **K-569** | Digital signature verification failed for e-KYC XML |
| 86 | **K-570** | Invalid digital signature certificate (Key Info) in e-KYC XML |
| 87 | **K-571** | Technical error while signing the e-KYC response |
| 88 | **K-600** | AUA is invalid or not an authorized KUA |
| 89 | **K-601** | ASA is invalid or not an authorized ASA |
| 90 | **K-602** | KUA encryption key not available |
| 91 | **K-603** | ASA encryption key not available |
| 92 | **K-604** | ASA signature not allowed |
| 93 | **K-605** | Neither KUA nor ASA encryption key is available |
| 94 | **K-955** | Technical failure internal to UIDAI |
| 95 | **K-956** | Technical error while generating the PDF file |
| 96 | **K-999** | Unknown error |
| 97 | **k-531** | Invalid Sub-AUA |
| 98 | **k-998** | Invalid Aadhaar Number or non-availability of Aadhaar data |
| 99 | **k-521** | Incorrect TID or invalid FDC/IDC |

> **Note:** Some codes in the supplied list had duplicate entries or differing descriptions for the same code. The table above consolidates those entries where appropriate. Verify the exact meaning and applicability against the latest UIDAI/AUA/ASA specification used by your integration.


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
