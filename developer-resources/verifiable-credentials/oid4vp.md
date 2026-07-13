---
description: >-
  Learn how OpenID for Verifiable Presentations enables secure credential
  verification.
---

# OID4VP

## OpenID for Verifiable Presentations

**OpenID for Verifiable Presentations (OID4VP)** is an OpenID Connect protocol for requesting and presenting Verifiable Credentials.

It lets a verifier request specific credential data from a holder. The holder reviews the request and shares a Verifiable Presentation only after consent.

### How it works

1. A verifier creates a presentation request.
2. The holder receives and reviews the request.
3. The holder selects credentials and approves the presentation.
4. The verifier validates the presentation and its proofs.

OID4VP supports interoperable, privacy-preserving credential exchange. Holders can share only the claims required for a transaction.

### Participants

OID4VP involves two primary participants:

* **Verifier** — The application requesting credential data. It defines the required claims and validates the returned presentation.
* **Holder** — The user controlling the credentials. The holder reviews the request and decides what to share.

An issuer also has an indirect role. It signs the credentials that the holder presents.

### Presentation requests

A presentation request describes what the verifier needs to validate. It can define:

* The credential type or schema the verifier accepts
* Required claims, such as a name, qualification, or eligibility status
* Accepted credential formats and presentation requirements

The holder's wallet uses this request to find suitable credentials. The holder should review every requested claim before approving the presentation.

### Verification and privacy

After receiving a presentation, the verifier checks the credential proof. It also confirms that the credential meets the request requirements.

Verifiers should request only the claims needed for their use case. Holders should share credentials only with trusted verifiers.

{% hint style="info" %}
OID4VP separates credential issuance from credential presentation. A verifier can validate a presentation without contacting the issuer during each verification.
{% endhint %}

### Detailed guide

<a href="https://docs.zetrix.com/oid4vp" class="button primary">Open OID4VP documentation</a>
