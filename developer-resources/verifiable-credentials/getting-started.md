# Getting Started

## Authorization

All API requests require a bearer token in the header:

```json
Authorization: Bearer <token>
```

## Zetrix DID Format

Example:

```
did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb
```

## Template Design Tips

* Use `USER` fields the holder fills in.
* Use `FIXED` for fields the issuer controls.
* Use camelCase for keys (e.g., `fullName`)
* Specify field format (`STRING`, `NUMBER`), and whether it is `mandatory` (true/false)

## Sample VC Lifecycle

Let's walk through each stage step-by-step:

### 1. Template Flow (by Issuer)

**Goal**: Define the structure of a credential (schema).

**Steps:**

1. **Submit Template Payload**
   * Inputs:
     * `signedTx`, `signerId`, `signerPublicKey` (to authenticate/sign the template)
     * Template metadata like `templateName`, `issuerDid`, `credentialFormat`, etc.
2. **API Call:** `/cred/tds/template/blob/create`
   * Backend generates a `blobId` and a `blob` that needs to be signed.
3. **Submit signed blob**
   * API: `/cred/tds/template/submit`
   * Signed blob is submitted to blockchain.
   * Output: `templateId`, `txHash`&#x20;

### 2. Apply VC Flow (by Holder)

**Goal**: A user (holder) applies for a credential based on a template.

**Steps:**

1. **Submit Application**
   * API: `/cred/v1/vc/apply`
   * Inputs:
     * `vcPayload`: includes `templateId`, `metadata`, and holder's public key/signature.
2. **Backend Actions**:
   * Validates the template.
   * Stores the VC application temporarily.
   * Output: VC ID and status (e.g., `APPLIED`)

### 3. Issue VC Flow (by Issuer)

**Goal**: Issuer issues a credential based on the holder’s application.

**Steps:**

1. **Create VC Payload**
   * API: `/cred/v1/vc/create`
   * Inputs: `vcId`, `data`, `issuanceDate`, `expirationDate`
   * Output:
     * `ed25519Blob` (data to be signed using Ed25519)
     * `bbsBlsBase64` (data to be signed using BBS+ for selective disclosure)
2. **Sign & Submit VC**
   * API: `/cred/v1/vc/submit`
   * Inputs:
     * `ed25519PubKey`, `ed25519SignData`
     * `bbsBlsPubKey`, `bbsBlsSignData`
     * `keyExpiry`
   * Output: Full VC Object with proof(s)

**Note**: Both signature schemes (Ed25519 and BbsBls) are used to ensure W3C compliance and support for selective disclosure.

### 4. Download VC (by Holder/Issuer)

**Goal**: Download the VC before expiry.

**Steps:**

1. **Submit Download Payload**
   * API: `/cred/v1/vc/download`
   * Inputs: `vcId`, `signVcId` (signed value of VC ID), `isIssuer` (optional)
2. **Backend fetches VC**
   * Checks temporary storage and resolves DID document.
   * Returns full VC object.

### 5. Create VP (by Holder)

**Goal**: Holder creates a **Verifiable Presentation** (VP) to selectively share claims.

**Steps:**

1. **Create VP Payload**
   * API: `/cred/v1/vp/create`
   * Inputs include selected claims and `rangeProof` conditions (e.g., age > 18)
2. **Backend generates `blobId` + `blob`** for the holder to sign.
3. **Submit signed VP**
   * API: `/cred/v1/vp/submit`
   * Output: Verifiable Presentation (VP) object

### 6. Verify VP (by Verifier)

**Goal**: Verifier checks authenticity and integrity of the VP.

**Steps:**

1. **Submit VP Payload**
   * API: `/cred/v1/vp/verify`
   * Input: Full VP object (with BbsBls signature, context)
2. **Backend resolves DIDs**
   * Verifier uses the DID of issuer and holder to check proof chain.
3. **Returns**: Verification result (valid/invalid)
