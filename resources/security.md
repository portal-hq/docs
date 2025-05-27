---
description: Security at Portal has been built in from day one.
---

# Security

## SOC 2 Type II

**Portal is SOC 2 Type II certified**. You can read about Portal’s SOC 2 Type II certification renewal [here](https://www.portalhq.io/post/portals-soc-2-type-2-certification-renewed).

## Internal Security

* All data is **encrypted at rest** and all data transmitted two and from Portal **protected by TLS**.
* Sensitive data is further encrypted at the application layer **backed by KMS**.
* Portal performs **quarterly pen tests** in addition to internal **threat modeling**.

## Cryptography

All cryptographic functions required for secure MPC are built on widely reviewed open source libraries and have been formally reviewed by external security teams.

Portal SDK's leverage the [CGGMP MPC protocol](https://eprint.iacr.org/2021/060.pdf) for ECDSA signatures.

Check out our blog post [here](https://www.portalhq.io/post/portal-security-principles-and-practices) for more details regarding Portal's security principles and practices.
