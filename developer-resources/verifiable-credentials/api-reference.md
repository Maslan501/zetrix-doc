# API Reference

This guide covers the **Zetrix Verifiable Credential (VC) system API endpoints,** organized by the major functions in the VC lifecycle: creating credential templates, issuing credentials, revoking credentials, managing **BBS+** issuer accounts, and creating/verifying verifiable presentations. Each endpoint includes its HTTP method and path, a description of its purpose, required request fields, response structure, example requests/responses, and potential error cases.

## VC Template APIs

Credential **templates** define the structure and metadata for credentials that an issuer can later issue. Templates are created and registered on the Zetrix network in two steps: first building a transaction blob, then submitting it after signing. These APIs are typically used by the issuer.

### Create Template Blob

**Endpoint:** `POST /cred/vc/template/create`\
**Description:** Creates a new credential template and returns a transaction blob that must be signed by the issuer. This blob represents a request to register the template on the blockchain. After obtaining the blob, the issuer should sign it with their Zetrix account private key (Ed25519) and use the **Submit Template** endpoint to finalize the template registration.

**Request Body:** _(JSON)_

* `templateName` (string): A human-readable name for the credential template (e.g., `"DrivingLicenseTemplate"`).
* `issuerDid` (string): The Decentralized Identifier (DID) of the issuer creating this template. This should correspond to the issuer’s Zetrix DID.
* `signerList` (array of objects): A list of one or more signers (accounts) authorized for this template’s transaction. Typically this will contain one entry for the issuer. Each object includes:
  * `signerDid` (string): The DID of an authorized signer (usually the issuer’s DID).
  * `publicKey` (string): The signer's public key (Ed25519) in an appropriate encoding (e.g., base58) that will be used to sign the template transaction.
* `credentialFormat` (array of objects): An array describing each attribute/field in the credential defined by this template. Each object in the array represents one credential field with the following properties:
  * `key` (string): A unique key or identifier for the attribute (e.g., `"fullName"`). This will be used as the field name in credential data.
  * `attribute` (string): A descriptive name of the attribute (e.g., `"Full Name"`).
  * `type` (number): A code or indicator for the data type or category of the attribute. (For example, `1` might indicate a text string, `2` an image, etc., as defined by the system.)
  * `format` (string): The expected format of the attribute’s value (e.g., `"string"`, `"date"`, `"image/png"` depending on the attribute).
  * `mandatory` (boolean): Whether this attribute is mandatory (`true`) or optional (`false`) for credential issuance.
* `version` (string): Version identifier for this template (e.g., `"1.0"`). Allows template updates or variations to be tracked.
* `remark` (string): An optional remark or description for the template. This can include any additional information or notes about the template.

**Response Body:** _(JSON)_

* `blobId` (string): An identifier for the created transaction blob (used to reference the blob in the submit call).
* `blob` (string): The transaction blob in hex string or base64 format. This blob encodes the template creation operation and must be signed by the issuer’s private key before submission.

**Example Request:**

```json
{
  "templateName": "DrivingLicenseTemplate",
  "issuerDid": "did:zid:123456789abcdef",
  "signerList": [
    {
      "signerDid": "did:zid:123456789abcdef",
      "publicKey": "BEd25519PubKeyExample=="
    }
  ],
  "credentialFormat": [
    {
      "key": "fullName",
      "attribute": "Full Name",
      "type": 1,
      "format": "string",
      "mandatory": true
    },
    {
      "key": "licenseNumber",
      "attribute": "License Number",
      "type": 1,
      "format": "string",
      "mandatory": true
    },
    {
      "key": "expiryDate",
      "attribute": "Expiry Date",
      "type": 1,
      "format": "date",
      "mandatory": true
    }
  ],

  "version": "1.0",
  "remark": "Template for issuing driving licenses"
}

```

**Example Response:**

```json
{
  "blobId": "be48f912-3a9f-4d00-90f5-2d9e6c8e7f01",
  "blob": "0a0c0b0a0931323334...5f6e" 
}
```

**Error Cases:**

* `400 Bad Request` – Missing or invalid fields (e.g., a required field like `templateName` is empty, or an attribute format is incorrect).
* `401 Unauthorized` – The caller is not authenticated or not allowed to create templates (e.g. invalid API token).
* `409 Conflict` – The template name is already in use or a similar template already exists (if the system enforces unique template names or IDs).
* `500 Internal Server Error` – An unexpected error occurred on the server (e.g., database or blockchain interaction failure).

### Submit Template

**Endpoint:** `POST /cred/vc/template/submit`\
**Description:** Finalizes the registration of a credential template on the Zetrix blockchain. The issuer must provide the signed transaction blob (from the **Create Template Blob** step). Upon successful submission, the template becomes available for credential issuance. Use this after signing the blob returned by the create endpoint.

**Request Body:** _(JSON)_

* `blobId` (string): The identifier of the template transaction blob obtained from the create step.
* `signature` (string): The issuer’s digital signature over the blob, typically an Ed25519 signature in hex or base64 format. This is generated by signing the `blob` with the issuer’s private key.
* `publicKey` (string): The public key corresponding to the private key used for signing. This should match one of the keys in the `signerList` from the template creation (usually the issuer’s Ed25519 public key).

**Response Body:** _(JSON)_

* `templateId` (string): A unique identifier assigned to the newly created template. This ID will be used to reference the template in subsequent operations (such as credential issuance).
* `templateHash` (string): A hash or transaction ID associated with the template registration on-chain. This can be used for audit or verification purposes to confirm the template was recorded on Zetrix.

**Example Request:**

```json
{
  "blobId": "be48f912-3a9f-4d00-90f5-2d9e6c8e7f01",
  "signature": "3a2b17f5...ab90d1ccee==",
  "publicKey": "BEd25519PubKeyExample=="
}
```

**Example Response:**

```json
{
  "templateId": "tmpl-614b1a2f8f7e4e0abc123456",
  "templateHash": "5F1A9C3D...EF7810"
}

```

**Error Cases:**

* `400 Bad Request` – The provided blob or signature is missing or malformed (e.g., wrong length or format).
* `401 Unauthorized` – The signature does not match the issuer’s public key, or the caller is not permitted to submit this template.
* `404 Not Found` – The `blobId` does not correspond to a valid pending template (for example, if it was already submitted or expired).
* `500 Internal Server Error` – An error occurred while processing the transaction (for instance, failure to write to the blockchain).

## VC Issuance APIs

Once a template is in place, these APIs manage the issuance of verifiable credentials to holders. The typical flow is: a holder applies for a credential, the issuer creates the credential (generating required signatures and transaction blob), the issuer submits the credential to finalize issuance, and then the credential can be downloaded by the holder or issuer.

### Apply VC (by holder)

**(Non-encrypted) Endpoint:** `POST /cred/v1/vc/apply`

**(Encrypted) Endpoint:**`POST /cred/v1/vc/enc/apply`

\
**Description:** Initiates a credential issuance request by a holder. The holder (or an application acting on behalf of the holder) calls this to apply for a credential of a given template. This step usually provides the data required for the credential and registers a request that the issuer can review/approve. The output is a VC request ID used to track the issuance process.

**(Non-encrypted) Request Body:** _(JSON)_

* `templateId` (string): The identifier of the credential template the holder is applying for. This should be a valid templateId obtained from the issuer (for example, the ID returned by the template submission).
* `holderDid` (string): The DID of the holder applying for the credential. This identifies the subject who will receive the credential.
* `attributes` (array of objects): The data values for the credential’s attributes, as required by the template. Each entry should provide one attribute’s value:
  * `key` (string): The attribute key, exactly as defined in the template’s `credentialFormat`.
  * `value` (string): The value the holder provides for this attribute. The format/type of this value should conform to what the template specifies (e.g., string, date).\
    &#xNAN;_&#x46;or example, if the template requires `fullName` and `licenseNumber`, the holder must provide those values._
* `holderPublicKey` (string, optional): The holder’s public key (for example, an Ed25519 public key corresponding to the holder’s DID). This may be provided if the system requires associating a specific key with the request (e.g., to later verify the holder’s presentation or for auditing). In many cases, this can be omitted if the holder’s DID document will be used to fetch keys.

**(Encrypted) Request Body:** _(JSON)_

* Encrypted raw data payload.

**Response Body:** _(JSON)_

* `vcId` (string): A unique identifier for the VC issuance request. This ID represents the pending credential application. It will be used by the issuer to create the credential and can be used by the holder to query status or download the final VC.
* `status` (string): The status of the request. Typically, a new application will be in a status such as `"Pending"` (or similar) indicating it is awaiting issuer action. The status may be updated to `"Approved"`, `"Issued"`, `"Rejected"`, etc., depending on the workflow (for this API reference, the initial status is pending issuance).

**(Non-encrypted) Example Request:**

```json
{
  "templateId": "tmpl-614b1a2f8f7e4e0abc123456",
  "holderDid": "did:zid:holder12345",
  "attributes": [
    { "key": "fullName", "value": "Alice Chen" },
    { "key": "licenseNumber", "value": "MYDL-987654321" },
    { "key": "expiryDate", "value": "2030-12-31" }
  ]
}
```

**(Encrypted) Example Request:**

<pre class="language-json"><code class="lang-json"><strong>aIg5AyAg33xNGs7JnqxZB39XPa8bR8Hcf2_S438xVH0A9kxUZADCt7eQl7ipMs5nm4EODsXaT_4S2I7BBS0qw-NVdqgj86XZls3PuZky7Ae84yG1ASZvHAB63ed6VBpKB_D3d3PnfRt1vIXpR2Oe_8yo9OrFiHqnV4h-LNWnnpTdV-2EHTfwnJzun1Fxq-79gzolreBcfYNfxjujNKe6Jr0hLS9gGxBZQgQ-y-GGeDeEoXjmTuCDeMnTiC77-nD31JGFyV5mC9uaLorCUX3FyQgFZn8Jr9anVgrrlb_Cjhj7YlnBforlojQgeGRddTfpcd_WMNhWsjifE-s8wFDnzxyWStq6tNXd_mSYL4r0Z8EWP_zFlIN-lynsMgZewDLTnjefDwhaoEmjZC_1FiUiHI_cU-dDzyjMQQHYZp0JNBU7IHcVFUHCv2MKrNSVvVSkK1YhgGtvQouGG6fKigqub2CGZnwKXUbPKiUdxeGd1vvzi1G7FSg8Pa98WgKU9gROMMMpmWxkS7UN2WALrcHCV1hOyOl32hI92d6DJq6Q0JXNo1Gzyzx1uckNxj9n8N7OAINqge_gbNateht1I_wwzIGofjR_Mr6NdvDZ8l41sgo9f9poiiiY3jT6ZQ76RapWCIYVqef65KeLKoGn9M0-_dr3UhKC
</strong></code></pre>

**Example Response:**

```json
{
  "vcId": "vc-501a8cde88b74bb0a23456789",
  "status": "Pending"
}
```

**Error Cases:**

* `400 Bad Request` – Missing required fields or invalid data format (e.g., a mandatory attribute value is not provided or has the wrong type).
* `404 Not Found` – The specified `templateId` does not exist or is not available for issuance.
* `401 Unauthorized` – If authentication is required, this error can occur if the holder is not properly authenticated or not allowed to apply for this credential.
* `409 Conflict` – A duplicate request already exists (for example, the holder already applied for this credential and it’s still pending).
* `500 Internal Server Error` – An unexpected server error occurred while recording the application (database error, etc.).

### Create VC (by issuer)

**(Non-encrypted)Endpoint:** `POST /cred/v1/vc/create`

**(Encrypted) Endpoint:** POST /cred/v1/vc/enc/create

\
**Description:** Used by the issuer to create (prepare) a verifiable credential in response to a holder’s application. This call will assemble the credential data from the template and the holder’s provided information, and produce the necessary cryptographic elements for the credential. Specifically, it generates the credential’s BBS+ signature (which the issuer must produce) and a blockchain transaction blob for recording the credential status on-chain. The issuer should call this after approving or deciding to issue the credential for a given `vcId`. The credential is not yet final until the **Submit VC** step is completed.

**(Non-encrypted) Request Body:** _(JSON)_

* `vcId` (string): The identifier of the VC request (from the **Apply VC** step) that the issuer is fulfilling. This links the create operation to the holder’s application and the associated template.
*   `bbsBlsSignature` (string): The BBS+ signature over the credential data, generated by the issuer’s BBS+ keys. This signature proves the authenticity of the credential’s contents and is used for privacy-preserving presentations later. **Important:** The issuer must produce this signature by signing all attribute values (in the order defined by the template) using their BBS+ private key. (See **BbsBls Sign Data** below for how to generate this signature.) The signature should be provided as a base64 or hex string as specified by the system.

    _Note:_ The issuer’s BBS+ public key is expected to be associated with the issuer’s DID (so verifiers can retrieve it during presentation verification). The system will validate the signature against the template’s attributes and issuer’s DID. If the issuer has configured the system with their seed (see BbsBls Account APIs), the signature may be generated internally, but typically the caller provides it for security.

**(Encrypted) Request Body:** _(JSON)_

* Encrypted raw data payload.

**Response Body:** _(JSON)_

* `blobId` (string): The identifier for the credential issuance transaction blob. This blob represents the on-chain operation to mark the credential as issued (and possibly to register its status or revocation entry).
* `blob` (string): The credential issuance transaction blob (hex string or base64). The issuer must sign this blob with their Zetrix account private key (Ed25519) to authorize writing the credential status to the blockchain.
* `bbsBlsSignature` (string): The BBS+ signature in base64/hex format for the credential (same as provided in the request, or regenerated). This is included in the response for reference, confirming the signature that will be embedded in the credential. The issuer should retain this value as it forms part of the credential's proof.

**(Non-encrypted) Example Request:**

```json
{
  "vcId": "vc-501a8cde88b74bb0a23456789",
  "bbsBlsSignature": "k3vX...ABC123=="
}
```

**(Encrypted) Example Request:**

```json
CImvIwjmub7nGUMAiKCM-dCB9-c3CMdI-zwjBD-ZO1gzNczenc5ni7tJmJzOIoaMWeKi0KhcHfoFCcJED75-lXMd3--gA1Z5Kq-b-tIN5VZk1v9JheJVFz5e2w8-0D2vfeFyrV-dGjHpBFIlT3cGpBxoW-fhHKXVa6YBXP6uuAt49LsnYKG3O0ng9dr88KuQBFr2oezCIoz82Q8p3B7-a4nJg0_j_m4c4Yz6zM0LFQRYZqG6kcsewm0X9NgupywP_tPowPnfwDYLwJU1SW7ktNshv3UtkZR_QRpVpBg8GHLC4cTBr2SvHn8ZSs_gZCdv2NMNJVv2feyFtAtKqIaXHR7AthYZ0dzyQPevd4ZMmXA59I0=
```

**Example Response:**

```json
{
  "blobId": "3e7f9a10-55c2-4d6b-8a9d-112233445566",
  "blob": "0abf2c9d...7890ae",
  "bbsBlsSignature": "k3vX...ABC123=="
}
```

**Error Cases:**

* `400 Bad Request` – The request is missing required data or has invalid format (for instance, no BBS+ signature provided, or the signature is the wrong format).
* `404 Not Found` – The `vcId` does not correspond to a valid pending application (e.g., if it was already processed or never existed).
* `403 Forbidden` – The issuer’s DID does not match the template’s issuer or the issuer is not authorized to issue this credential.
* `409 Conflict` – The credential might have already been created or is in a state that cannot be created again (to avoid duplicate issuance).
* `500 Internal Server Error` – A server error occurred while assembling the credential (e.g., issues in retrieving template or holder data, or internal cryptographic failure).

### Submit VC (by issuer)

**(Non-encrypted)Endpoint:** `POST /cred/v1/vc/submit`

**(Encrypted) Endpoint:** POST /cred/v1/vc/enc/submit

\
**Description:** Finalizes the issuance of the verifiable credential. The issuer calls this after signing the transaction blob obtained from **Create VC**. This will submit the issuance transaction to the Zetrix blockchain (recording the credential’s existence and status) and finalize the credential object. On success, the credential is officially issued and can be delivered to the holder. The response includes the complete VC in a verifiable format.

**(Non-encrypted) Request Body:** _(JSON)_

* `blobId` (string): The identifier of the credential issuance transaction blob (returned from the create step).
* `signature` (string): The issuer’s Ed25519 signature over the blob, authorizing the credential issuance on-chain. This should be provided in the required format (hex/base64).
* `publicKey` (string): The Ed25519 public key corresponding to the signature, used to verify that the blob was signed by the issuer’s account. This should match the issuer’s DID on the blockchain.

**(Encrypted) Request Body:** _(JSON)_

* Encrypted raw data payload.

**Response Body:** _(JSON)_

* `vcObject` (object): The issued Verifiable Credential, in its full JSON representation. This will typically include standard VC fields such as `@context`, `id` (which may incorporate the `vcId`), `type`, `issuer`, `credentialSubject` (containing the holder’s DID and the attributes with values), and `proof`. The `proof` section will include the BBS+ signature (often encoded as a proof value) and related metadata (like the public key or verification method, and type of proof). It may also include a `credentialStatus` entry indicating where the credential’s status (e.g., revocation status) is recorded (for example, referencing a blockchain registry or contract).
* `downloadExpiryDate` (string): A timestamp indicating how long the issued credential will be available for download via the **Download VC** endpoint. The credential might be stored temporarily on the server; this field informs the issuer/holder of the cutoff time to fetch it. After this expiry, the credential might be purged for security, requiring the issuer to re-share if needed.

**(Non-encrypted) Example Request:**

```json
{
  "blobId": "3e7f9a10-55c2-4d6b-8a9d-112233445566",
  "signature": "5f1d6c...aa321b==",
  "publicKey": "BEd25519PubKeyExample=="
}
```

**(Encrypted) Example Request:**

```json
CImvIwjmub7nGUMAiKCM-dCB9-c3CMdI-zwjBD-ZO1gzNczenc5ni7tJmJzOIoaMWeKi0KhcHfoFCcJED75-lXMd3--gA1Z5Kq-b-tIN5VZk1v9JheJVFz5e2w8-0D2vfeFyrV-dGjHpBFIlT3cGpBx
```

**Example Response (partial):**

```json
{
  "vcObject": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://w3id.org/security/bbs/v1"
    ],
    "id": "vc-501a8cde88b74bb0a23456789",
    "type": ["VerifiableCredential", "DrivingLicenseCredential"],
    "issuer": "did:zid:123456789abcdef",
    "credentialSubject": {
      "id": "did:zid:holder12345",
      "fullName": "Alice Chen",
      "licenseNumber": "MYDL-987654321",
      "expiryDate": "2030-12-31"
    },
    "credentialStatus": {
      "id": "zetrix:vcstatus:...abcdef", 
      "type": "CredentialStatusList2017"
    },
    "proof": {
      "type": "BbsBlsSignature2020",
      "created": "2025-04-07T01:23:45Z",
      "proofPurpose": "assertion",
      "verificationMethod": "did:zid:123456789abcdef#bbs-key",
      "proofValue": "k3vX...ABC123=="
    }
  },
  "downloadExpiryDate": "2025-04-14T01:23:45Z"
}
```

**Error Cases:**

* `400 Bad Request` – The signature is missing or invalid, or the blobId is not provided.
* `401 Unauthorized` – The signature does not match the issuer’s known public key (verification failed), or the issuer is not authorized to submit this credential.
* `404 Not Found` – The blobId is not recognized (e.g., if the create step was not done or the blob has expired), or the corresponding VC request was not found.
* `409 Conflict` – The credential has already been submitted/issued (duplicate submission) or was revoked by the time of submission (unlikely scenario).
* `500 Internal Server Error` – A server or blockchain error occurred during submission (e.g., failure writing to blockchain or assembling the final VC object).

### Download VC (by issuer or holder)

**(Non-encrypted)Endpoint:** `GET /cred/v1/vc/download`

**(Encrypted) Endpoint:** POST /cred/v1/vc/enc/download

\
**Description:** Retrieves a previously issued verifiable credential. This endpoint can be used by the holder (to download the credential that was issued to them) or by the issuer (to retrieve the credential, for example to send it through another channel). Typically, this would be called after the **Submit VC** step, using the provided `vcId`. The credential must still be available (not expired from temporary storage) for this call to succeed. The response is the VC in JSON format.

**(Non-encyrpted) Request:**\
This is a HTTP GET request. The `vcId` of the credential to download is usually provided as a query parameter. For example:

```
bashCopyEditGET /cred/v1/vc/download?vcId=vc-501a8cde88b74bb0a23456789
```

If authentication is required, the caller must be either the issuer of the credential or the holder (subject) of the credential. The system will validate that the requesting user has rights to access the specified `vcId`. (In an OAuth or token-based setup, this could be implicit; in others, the `holderDid` or similar might be required.)

**Response Body:** _(JSON)_\
The full Verifiable Credential object in JSON, identical to the `vcObject` returned in the Submit VC step. This includes all fields of the credential (context, id, type, issuer, credentialSubject, credentialStatus if any, and proof with BBS+ signature). This allows the holder to store or view their credential, and the issuer to archive it if needed.

**(Encrypted) Request Body:** _(JSON)_

* Encrypted raw data payload.

**(Non-encrypted) Example Request:**

```
GET /cred/v1/vc/download?vcId=vc-501a8cde88b74bb0a23456789
```

**(Encrypted) Example Request:**

```postman_json
sHaMF3ysLD7E9KZqW3AiEe05p9k_zAn8QiEM822w9ghiazIf22brMEkNY8FyLT2JrCJK-Bn7S8DAxtokezyyV1D2c40_xt48DFoqIJZrcWzRqYJjH6XShNA4S11quqkZQB2mnVmASsKKfe04oyqY_0yeWU7hmkD-GftXFuYeKtz76AZqLsY3Cgsh3xL7aXNHwYF7qMSt1rJ4w2FfS6_VcDswFLpj5E5LEyZqP5NXAWo5EilOsiLeDlg-lHZTNT3SX1damQwTpjxh83J2SShGDE2JdzG3UiAZswYgdjsMGkyxzuKOcRfrLZfhbfasZlM4M81qgLb7M9T9KfYzWgg7bVLSHzkYmA-IzJvdoyvq
```

**Example Response (full VC):**

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/security/bbs/v1"
  ],
  "id": "vc-501a8cde88b74bb0a23456789",
  "type": ["VerifiableCredential", "DrivingLicenseCredential"],
  "issuer": "did:zid:123456789abcdef",
  "credentialSubject": {
    "id": "did:zid:holder12345",
    "fullName": "Alice Chen",
    "licenseNumber": "MYDL-987654321",
    "expiryDate": "2030-12-31"
  },
  "credentialStatus": {
    "id": "zetrix:vcstatus:...abcdef",
    "type": "CredentialStatusList2017"
  },
  "proof": {
    "type": "BbsBlsSignature2020",
    "created": "2025-04-07T01:23:45Z",
    "proofPurpose": "assertion",
    "verificationMethod": "did:zid:123456789abcdef#bbs-key",
    "proofValue": "k3vX...ABC123=="
  }
}
```

**Error Cases:**

* `400 Bad Request` – If `vcId` is missing or in an invalid format in the request.
* `401 Unauthorized` – The requester is not authorized to download this credential (for example, the `vcId` does not belong to the holder making the request, or no valid auth token provided).
* `404 Not Found` – No credential found for the given `vcId`, or it’s no longer available for download (e.g., if the download window expired or the credential never existed).
* `410 Gone` – The credential was available only temporarily and the period has lapsed (the `vcId` was valid but the stored credential has been removed).
* `500 Internal Server Error` – An unexpected error on the server while retrieving the credential.

## VC Revocation APIs

These APIs allow an issuer to revoke an issued credential, invalidating it so it will no longer be considered valid by verifiers. Revocation is typically a two-step process (create a revocation transaction blob, then submit it after signing) similar to template creation and credential issuance.

### Create Revoke VC Blob

**Endpoint:** `POST /cred/v1/vc/revoke/create`\
**Description:** Initiates the revocation of a verifiable credential. The issuer calls this to build a revocation transaction for a given credential, which returns a blob to be signed. Revoking a credential typically updates its status on-chain (for example, updating a revocation registry or marking the credential as revoked in a smart contract). This step does not yet revoke the credential; it only prepares the transaction.

**Request Body:** _(JSON)_

* `vcId` (string): The identifier of the credential to be revoked. This is the same ID used during issuance (and present in the credential’s `id` field). The system will use this to locate the credential record or status entry on-chain.
* `reason` (string, optional): A human-readable reason for revocation. This reason might be recorded in logs or off-chain storage for audit purposes (e.g., “Credential revoked due to expiration” or “Holder requested revocation”). It is not typically part of the on-chain transaction (which usually just flips a status), but may be stored in an off-chain database.

**Response Body:** _(JSON)_

* `blobId` (string): The identifier for the revocation transaction blob.
* `blob` (string): The revocation transaction blob (hex/base64 string) that needs to be signed by the issuer’s private key. This blob encodes the action of revoking the credential identified by `vcId`.

**Example Request:**

```json
{
  "vcId": "vc-501a8cde88b74bb0a23456789",
  "reason": "Holder no longer entitled to this credential"
}
```

**Example Response:**

```json
{
  "blobId": "98c1f0e2-4477-4cfa-99a1-556677889900",
  "blob": "0a9cfd21...abcd1234"
}
```

**Error Cases:**

* `400 Bad Request` – Missing `vcId`, or the request is malformed.
* `404 Not Found` – The specified `vcId` does not correspond to an issued credential (perhaps it was never issued or already revoked).
* `403 Forbidden` – The caller is not the issuer of that credential or not authorized to revoke it. Only the original issuer (or an authorized delegate) should revoke a credential.
* `409 Conflict` – The credential is already revoked or a revocation is already in progress for this credential.
* `500 Internal Server Error` – Server error while generating the revocation blob (e.g., inability to fetch credential status from chain).

### Submit Revoke VC Blob

**Endpoint:** `POST /cred/v1/vc/revoke/submit`\
**Description:** Finalizes the credential revocation by submitting the signed revocation transaction to the blockchain. After this call succeeds, the credential identified by the given `vcId` is considered revoked. Verifiers checking the credential’s status will see it as revoked (invalid). This endpoint should be called after the issuer signs the blob from the create step.

**Request Body:** _(JSON)_

* `blobId` (string): The identifier of the revocation transaction blob (obtained from the create step).
* `signature` (string): The issuer’s signature over the revocation blob, using the issuer’s Ed25519 private key. This proves the issuer authorizes the revocation.
* `publicKey` (string): The Ed25519 public key of the issuer corresponding to the signature, to verify the signature. This should match the issuer’s DID and the key that was used in issuance.

**Response Body:** _(JSON)_

* `vcId` (string): The identifier of the credential that was revoked (echoing the input for clarity).
* `status` (string): The new status of the credential. Typically, this will be `"Revoked"` to indicate the credential is now revoked. In some implementations, this might also include a transaction hash or confirmation of revocation. For example, the response could include a blockchain transaction ID for the revocation or a timestamp of revocation, but at minimum the status is updated.

**Example Request:**

```json
{
  "blobId": "98c1f0e2-4477-4cfa-99a1-556677889900",
  "signature": "d1e2f3...445566==",
  "publicKey": "BEd25519PubKeyExample=="
}
```

**Example Response:**

```json
{
  "vcId": "vc-501a8cde88b74bb0a23456789",
  "status": "Revoked"
}
```

**Error Cases:**

* `400 Bad Request` – The signature is missing or invalid, or the blobId is not provided.
* `401 Unauthorized` – The signature does not match the issuer’s known public key (signature verification failed), or the caller is not authorized to revoke this credential.
* `404 Not Found` – The blobId is not recognized (e.g., if the create step was not done or already submitted). It could also mean the credential was not found (if an incorrect vcId was somehow used).
* `409 Conflict` – The credential is already revoked (the revocation transaction may have been submitted previously, possibly by another process).
* `500 Internal Server Error` – An error occurred during submission (for example, a blockchain failure or network issue).

## BbsBls Issuer Account APIs

The Zetrix VC system uses BBS+ digital signatures (BbsBls) for credentials, enabling zero-knowledge proof presentations. These endpoints help manage the issuer’s BBS+ key pair. Typically, the issuer will derive a BBS+ key from a seed (such as a wallet mnemonic or seed phrase) and use it to sign credential data. These APIs are mostly utilities for issuers: in many cases, issuers may manage keys client-side, but the API provides a way to derive and use keys if needed.

### Create Account from Seed

**Endpoint:** `POST /cred/bbs/create`\
**Description:** Derives a BBS+ issuer account (key pair) from a given seed. This is usually used to get the BBS+ public key corresponding to a seed phrase or seed value that the issuer controls. The BBS+ public key is needed to include in DIDs or to share with verifiers so they can verify credentials. The seed is used to deterministically generate the BBS+ private/public key pair. (This does not create an account on the blockchain; it’s purely cryptographic key derivation.)

**Request Body:** _(JSON)_

* `seed` (string): A seed used to generate the BBS+ keys. This can be a mnemonic phrase or a hex string/base64 string representing the seed bytes. The exact format depends on implementation: for example, it could be a 32-byte hex string derived from a 12-word mnemonic. The seed provided should be kept secure, as it determines the private key. _(If a mnemonic phrase is used, it should be a single string with words separated by spaces.)_

**Response Body:** _(JSON)_

* `publicKey` (string): The derived BBS+ public key in a portable format (e.g., base64 or hex). This is the public key that corresponds to the issuer’s BBS+ private key. The issuer’s DID document should include this public key (or a reference to it) so that verifiers can use it to verify credential signatures.\
  &#xNAN;_&#x4E;ote:_ The BBS+ private key is not returned by the API for security reasons. The caller is expected to secure the seed or derive the private key on their side if needed. The same seed will always generate the same key pair, so storing the seed is sufficient to reproduce the keys.

**Example Request:**

```json
{
  "seed": "trust toward breeze vapor enforce banana brief unfold carpet adjust obey parade"
}
```

**Example Response:**

```json
{
  "publicKey": "kqdPg3...XYZabc=="
}
```

_(The publicKey string is truncated for brevity. It would be a long base64 string.)_

**Error Cases:**

* `400 Bad Request` – The seed is missing or not in a valid format (e.g., wrong number of words in mnemonic, or invalid hex string).
* `401 Unauthorized` – If the API requires authentication (to prevent unauthorized key derivation) and the call is not authenticated.
* `500 Internal Server Error` – The server encountered an error during key generation (for example, if the seed phrase couldn’t be parsed or an internal library error occurred).

### Sign Data

**Endpoint:** `POST /cred/bbs/vc/sign`\
**Description:** Signs an array of data messages using the issuer’s BBS+ secret key. This is used to produce the BBS+ signature that will go into a Verifiable Credential. The messages to sign should correspond to the credential’s attributes (and possibly other fixed fields like a context or nonce, depending on implementation). The output is a BBS+ signature that can later be verified with the issuer’s BBS+ public key. Typically, the issuer will use the same seed as in **Create Account** to derive the secret key and sign the credential attributes.

**Request Body:** _(JSON)_

* `seed` (string): The seed or secret key material for signing. This should be the same seed used to derive the BBS+ keys (as provided in the create account step). The service will derive the BBS+ private key from this seed internally and use it to sign. _(In some cases, the API might accept a raw `privateKey` instead, but generally providing the seed is expected so that it can deterministically derive the key. Ensure this is sent over a secure connection as it is sensitive.)_
* `messages` (array of strings): The list of messages (attribute values) to sign. The order of this array is critical – it must match the order of attributes defined in the template’s `credentialFormat`. Each entry in this array is a string representation of the corresponding attribute’s value. For example, if the template’s first attribute is fullName = "Alice Chen", second is licenseNumber = "MYDL-987654321", etc., then `messages` should be `["Alice Chen", "MYDL-987654321", "2030-12-31"]` for those values.\
  &#xNAN;_&#x4E;ote:_ If any special encoding is required (for example, dates or numbers might need a specific format or zero-padding), the values should be formatted accordingly before signing.

**Response Body:** _(JSON)_

* `signature` (string): The BBS+ signature over the provided messages, encoded as a base64 string (or hex string depending on the system conventions). This signature will be used as the `bbsBlsSignature` in the **Create VC** call. It incorporates all messages in a single compact signature that allows selective disclosure proofs later. The length of this string is typically fixed (for example, 112 bytes in base64 for BBS+ sig).

**Example Request:**

```json
{
  "seed": "trust toward breeze vapor enforce banana brief unfold carpet adjust obey parade",
  "messages": [
    "Alice Chen",
    "MYDL-987654321",
    "2030-12-31"
  ]
}
```

**Example Response:**

```json
{
  "signature": "lQ1bFm...mnopQR=="
}
```

**Error Cases:**

* `400 Bad Request` – Missing seed or messages, or the messages array is empty. Also if a message is in an invalid format (e.g., not a string).
* `401 Unauthorized` – If the API requires auth and the call is not authorized to perform signing (to protect misuse of signing).
* `500 Internal Server Error` – If signing fails due to internal errors (for example, if the seed is incorrect or the cryptographic operation encountered an issue).

## Verifiable Presentation APIs

Verifiable Presentations (VPs) allow a credential holder to prove certain information from their credentials to a verifier. In Zetrix’s VC system (using BBS+), a holder can generate a proof that selectively discloses some attributes of their credential (and optionally proves statements about others, like ranges) without revealing the entire credential. The VP APIs help create and verify these presentations. Typically, the holder will use **Create VP** and **Submit VP** to produce a presentation, and a verifier will use **Verify VP** to check its validity.

### Create VP Blob

**(Non-encrypted) Endpoint:** `POST /cred/v1/vp/create`

**(Encrypted) Endpoint:** POST /cred/v1/vp/enc/create

\
**Description:** Creates a verifiable presentation object (unsigned) from a given credential and disclosure requirements. The holder calls this to prepare a presentation by specifying which credential to present and which attributes to reveal or prove. The endpoint returns a presentation blob that the holder must then sign (to prove possession) or finalize. This step performs the heavy cryptographic computation of generating a selective disclosure proof using the BBS+ signature from the credential.

**(Non-encrypted) Request Body:** _(JSON)_

* `vcObject` (object): The verifiable credential to be presented, provided in JSON format. This should be the full credential that was issued (as obtained from the download or issuance step), including its proof (BBS+ signature). The service will extract the credential’s data and proof from this object. _(Alternatively, some implementations might accept a `vcId` and fetch the credential if it’s stored, but in this context, providing the credential object ensures the holder has it and controls it.)_
* `revealedAttributes` (array of strings): A list of attribute keys that the holder wants to reveal in the presentation. Only these attributes from the credential will be visible to the verifier; all other signed attributes will remain hidden but cryptographically proven. For example, `["fullName", "expiryDate"]` would reveal the holder’s full name and the expiry date, but not reveal other fields like license number. If this array is empty, it means no attribute values are revealed (which typically is not useful unless combined with a proof like a range).
*   `rangeProofs` (array of objects, optional): A list of zero-knowledge range proofs the holder wants to include for certain numeric attributes, instead of revealing their exact values. Each object can specify that an attribute’s value lies within a certain range without disclosing the actual value. If provided, each object should contain:

    * `attribute` (string): The key of the numeric attribute for which to prove a range (e.g., `"age"` if the credential had an age field). This attribute’s value will not be revealed directly.
    * `domain` (string): A domain separator or identifier for the proof. (This can be a fixed string to avoid proof reuse across contexts or to bind the proof to a specific purpose. It may also identify the unit or context of the attribute; e.g., `"years"` for age.)
    * `min` (number): The inclusive lower bound of the range that the attribute’s value is claimed to be in.
    * `max` (number): The inclusive upper bound of the range for the attribute’s value.
    * `nonce` (integer): A random nonce or salt for the range proof. This ensures the proof is unique and cannot be reused. If not supplied, the system may generate a random nonce internally. The verifier must know this nonce (usually it will be included in the VP proof) to validate the range proof.

    _Example:_ To prove a birth year is between 1990 and 2000 without revealing it, use `attribute: "birthYear", min: 1990, max: 2000`. The presentation will show that the birthYear lies in \[1990,2000] but not the exact year. Range proofs require the credential to have that attribute signed originally.
* `challenge` (string, optional): A challenge nonce provided by the verifier (or generated by the holder) to include in the presentation proof. This helps prevent replay attacks by tying the VP to a specific verifier’s request. If provided, it will be incorporated into the proof signature. If not provided, the system might generate one or the VP might be considered bearer. Typically, for strongest security, a verifier will give a random challenge that the holder must include and later the verifier will check for it.

**(Encrypted) Request Body:** _(JSON)_

* Encrypted raw data payload.

**Response Body:** _(JSON)_

* `blobId` (string): An identifier for the presentation blob that was created.
*   `blob` (string): The unsigned Verifiable Presentation data in a serialized form (likely JSON, possibly encoded as needed). This blob includes the disclosed information (attributes listed in `revealedAttributes`) and the cryptographic proof data for hidden attributes (including any range proofs). However, it may not yet contain a proof of holder possession (i.e., a signature from the holder’s DID). The holder needs to sign this blob (or a subset of it) with their own key to prove that they are the legitimate holder of the credential. After signing, the holder will use **Submit VP Signed Data** to complete the VP.

    The `blob` essentially contains a Verifiable Presentation object with everything except the holder's signature. It will reference the credential (or include a derived proof of it) and list revealed fields. The holder’s signing step will typically add a `proof` section indicating the holder’s verification method and signature.

**(Non-encrypted) Example Request:**

```json
{
  "vcObject": {
    "@context": ["https://www.w3.org/2018/credentials/v1", "https://w3id.org/security/bbs/v1"],
    "id": "vc-501a8cde88b74bb0a23456789",
    "type": ["VerifiableCredential", "DrivingLicenseCredential"],
    "issuer": "did:zid:123456789abcdef",
    "credentialSubject": {
      "id": "did:zid:holder12345",
      "fullName": "Alice Chen",
      "licenseNumber": "MYDL-987654321",
      "expiryDate": "2030-12-31"
    },
    "proof": {
      "type": "BbsBlsSignature2020",
      "created": "2025-04-07T01:23:45Z",
      "proofPurpose": "assertion",
      "verificationMethod": "did:zid:123456789abcdef#bbs-key",
      "proofValue": "k3vX...ABC123=="
    }
  },
  "revealedAttributes": ["fullName", "expiryDate"],
  "rangeProofs": [
    {
      "attribute": "licenseNumber",
      "domain": "license",
      "min": 900000000,
      "max": 999999999,
      "nonce": 123456789
    }
  ],
  "challenge": "b25f5e9c-7d7b-4f1e-92d3-123456789abc"
}

```

_(In the above example, the holder will reveal `fullName` and `expiryDate` openly. They will not reveal `licenseNumber` but instead prove that it lies between 900000000 and 999999999, perhaps to show the license number is within a certain format or range. The `challenge` is a random UUID from the verifier.)_

**(Encrypted) Example request:**

```postman_json
VfPKszAiX3phb_nWRkzaoxKJgy9QbUmkNjthHKQVPJZa7lrnzan3QI6ZR-Ol7pMz2XIHmqHkkxGWIP5I_LYTcmQW2FA-YPKQXs3ZIaFKt40znXS8ruIv9pIua3agfH6nL1sIn-tIZulvEXz0T6HL3kX6_NsCdbBdo_bMoJH9r6PWB10WFegA0lHcn_tCGO5ekAE4zeaaKaWS5wPhSA1DssrpRR_rgGGyEANESDryszfBzFkAkgfdPvDcmXXtE8HMESfQFmaHuOLjIRXh6R7Due1lk4jJGftmx12tTZYig62JQYWulGxfUWCMS7rGGBXMpDRqWsUMf-NhUXOKOQc9oFQ3VGf3qqcHKTje7oowZavk_BKpmflG7bTa6kaWGMxGBAKGhFQr0K0XgipupmYKWZhvykxagx9XMMuPYR5Mkqk3sp4AYfWLyFXf9aD9LOGaUK8E0i71PYoLbp5BvQFw_oTxKSgQDMCWv305X_O3F-oVChU6AjHv4eKovIHM3G4K_mQclvTNMhxGgMs9Qz59acwe2DsHBiLGhiew7TRVjDf6ooweaV6qUbGb-z0pPK0ZvfsGNFMs_k4Tmoq7WnbeNSR0WJBft0SIu_IzpmYpwlp-zYqEF7JvRm-AGYCYPB-3gRvVRRO0evyBF5x1kvB944TaQgZFgu13EN1dqiV7wsFUHFcEvgwnJS-7i2PcHwZz3zvnUpaFC5jv0TyCBdp7_tI8oVVu2u1ku4oIsAwMTiLK8hIDYhYBIEiIeJpJ832e-WqX6wotzN6xPtIOaidREmOBaJ4DEpV51sg0iS_4C4OGimFxc60jpa1s5qy5Js22XPY7EFkhAa9lwl6dCJs8J-_DVo3ImpmM88clVOXC_t4uJ1HSC6tCwcKfG2JJi_MnzVEpsNlCPT_b7sYDYrdIW1Rew01PaDk6kroxcWS2VzU-civUE2ZWjuQN8air-reGNhZjV0n4fWP3TIc11LfUUq-rc6q8S3L2JtQcv4s7mLcBg8nJ1sD33wl1WxzyJlQ39eYsf9dySxkWKpuW8o8lV4SO2v1VqLXzrx8O94y1vtI-4OABB4i0Ws5P72RtDNATj4MtXfUsa7bLVujoU0fAGKxIfkg3dN8Q03XsD8hESie-TfTtdxvalYEb8jDXjqVawzgJ0YGvMgyqLJgiAG9qtLxrlfDc9ehV8bdw69XngThbQZcOu_CNRcnDezOQMB3pRi0ZRMZhLtaEl3yJPWYsp88k7TBNppqdKlM6vUd8TfYBtoBuIn8Br5t_prpdB0ZkdSiwpCLKuRKLKUDImidqjs37wzErc4CyhsJ9Z5NNiFhMcukya_qX43SCrIqPS2VzjqFJGzgj2WkZJzJgMB06695DGg9IZn1KYHkiV5jJNwJhiMaRNjGBkqcKcHZZFSeI88RNTukEUlySorLJ1bCvSSKU3AOprTGzbNF1QxjL4poW-NwfXJd3GqGpXNgvzzg9uzyhI-XLlvYRg2FkD8wR7beYMsOm9KqH9yJFE3HQPf1DISPK3y2muLsUCP8vtQ0XJ0N8Zr8j3Z-gIcJ5rYgrSqnSJa-RPY_dNn1fppScnWewDLdoakjMzlQV8xaqoYAt0CLWsbxrdQwxLgT0Pd_gtfU7LBsNdxEdRwjb3cXtXsLdwXy2ofms3FIJBEoHJSXVL9Qaq3FvFv4ZoRgaMk4YgHesBA_rZRbXq_OvTbeGaC3D2mShcWmAPDy3ejJcU3JexWykllLIgFqIQw9r2mOuPpcvczEvW-mskoJ4XOCTMNvqeTOc5XjKibGLZ6VfeMyj0kKVPFrd4EeCwTdaWJLjersOYmpB7tk9sruuLxioGF4vahM3bsAhTsQvxnSZjojuwG6dTGyqCpHgF6BBoYi3Y_aHbRfqTAVtL79DjzBotQ5VNuWhZk_ricmzCXll4DuVezYNx1oc4PfTgF0w214iWlFWlnNhxLvPaBCGZ4AACng2Nhn-wyAWV9HQJI0ChG1DKa_fWuwt_o1j6k8noZh1HZAmfed9Lh-KYitlsw3x-mu1qYiuETNK8AnOMGZFoy1yTPRhc-7Z5tghumqnV_sZhce0tohnByxSEKXxeaa76h1TPGfGAPGkwZSb2EbQrIYG37pmAyKSG3uLxgb-33q7j71m79OifEoX40-Bt5cQtPKSJbuzerrmmjH-EYxu9DL_eim_O1YejteBbUuqA6a6lLu3CvNRiyemrmGy8hBneOAae--QWyAvO8Jv4GCWjuBsH63EHbz4mz8_dazR5xmaoc9sLSCUwoOxQidHesBTQHvsxNMRbFTRtfWUMgqqX2h4y_4JfkvI0q8In5AzIgGqsNBBZLPqO2kFAX-pmd_QuWxHIO3bQUOOjBhEwnYDWm8Lh8XG4JU8Qw7gTy5E7-4ibATsMBE15ZD8rWzJm0HQCA-4xRCT-TecJcwDHLrX2mNC08pcy8rmNZ9KWa8Ih7P2AmVpO_5eBPJaFP_43c8hx-VG7x6BJTeIl6HY2uowQrc7OfEHUjo12lbbyaDSqXCoPZ1e9ubwOeSKmRF-OdALhyqdDxj6XghHZ1L6ec_9BDySnglqVxP3h6bEjhT5mtHRUdqYNib9eYrj4h43DxtztLjd2q8smHBWkttFfmL8CfTAG8ZFgTPoKDaLUvE1XT1vF--53pkXK-1GYmdsqKImqB7Ss2PHAv3HBIC1rZp_rw_dN7dbsDUBxOYkRaVwiELv6DEpEUKSieq-bvz54aD33sNllat7aUNBhD9rENK216PTkUTamSygwuA-Scr44hhA9N5sBzSa-dLVeTRqGo1T9mo71SmUZLFgD6AXYgZxENJOrxKL-zqDIOBomLOXi5827MosoxKrG-06HPzIizTZfCgF8FhD989j17-c0aSRhH8CfnV1axpjlfmpXa3RjW3m-m6XKcLBkFfIQKl_QF8OmZ4o-JKbkHRyK1vNiCDE_Qlbo1jTlQDGFZtFrH9BomgKoZ_eE-Bl4OVPkKtDQQUmsi_ceTEHHW0_Y9PN_Ot6vt_iMHjT69GLoiWxuu4ttvhW3YmZ9sFlpMXt6D5NEMkMEwcHM7XVFtL_E6Lzq5lA5MhMxkZfPqQiP0xOSaOrrY4KubcdXXMrvhKz6tLOeBHBQ6eEWtqrgVkFetdN1LDMWblYLbSMiYmGPW8f673KXjjYKW3-KHr24iy8OBjZ-IVULPr2vnHsvyVJj0YkMqXI8TuumiwJO1qJFoYQMbBqhscG6MG1KEWhmaHxkb6gm1oaJL3PyCHbdMeb3BJ5MFQU7zaAS9tby680NRKHnAezER85dpNDr7RZ_4NUzJSQ9IlnyxEbt7fLDiRY_Yd747zp-X1tm6lcTvnhSLwchXz4NUFAikCRInUvIvQYC5y0DU5tmcNaNVrlUcgsbRprB8naGv5DYWFVniOK8Mhp9sgrsY_Qb79V05ld9LI9KUv7aJ-6OpDRw4bfnEDPvzkM7PImYAe_EePLYfZtpcPOvls0qzq6-RGRzWR8b1xfVUmrVnlakFAPjD_Xnc6zcvbcY2kyyIlJ4x7daysXd4wpYAasqvymcw_279UY-_U4gkVeahvswktWoY8LB34NfYZHMlbsRd1NvbwkhxAS1BN73XX91Y25gKO4RAa6ZX3uVtJJr6EjiVaUVw==
```

**Example Response:**

```json
{
  "blobId": "a7c8d901-2233-4455-8899-abcdef123456",
  "blob": "{ \"@context\": [...], \"type\": [\"VerifiablePresentation\"], \"verifiableCredential\": [...], \"proof\": { \"type\": \"BbsBlsSignatureProof2020\", \"nonce\": \"MTIzNDU2Nzg5...\", ... } }"
}
```

_(For readability, the blob content is shown as a snippet. In practice, `blob` will be a JSON string or structure containing the Verifiable Presentation with the proof of the credential. The proof inside the blob at this stage is likely a **BbsBlsSignatureProof2020** (or similar) that contains the derived proof from the BBS+ signature, which shows consistency of hidden attributes and correctness of revealed ones. The holder’s own signature is not yet included.)_

**Error Cases:**

* `400 Bad Request` – Missing or invalid input (e.g., no `vcObject` provided, or the credential object is malformed; requesting to reveal an attribute that isn’t in the credential; rangeProof parameters invalid such as min > max).
* `401 Unauthorized` – If the holder is not authorized to create a VP for the given credential (e.g., the credential’s holder DID does not match the authenticated user). Typically, the holder should only present their own credential.
* `404 Not Found` – If a `vcId` was expected instead of `vcObject` and not found (not applicable in our assumed interface where `vcObject` is given).
* `500 Internal Server Error` – The server encountered an error during proof generation (could be due to cryptographic library issues or memory constraints if the credential is large). This could also happen if the credential’s proof is not valid (e.g., if the BBS+ signature in `vcObject` failed verification against issuer’s public key, meaning the credential is suspect).

### Submit VP Signed Data

**(Non-encrypted) Endpoint:** `POST /cred/v1/vp/submit`

**(Encrypted) Endpoint:** POST /cred/v1/vp/enc/submit

\
**Description:** Completes the verifiable presentation by incorporating the holder’s signature (or proof of holder) into the presentation blob. The holder calls this after **Create VP Blob**, providing their signature over the blob (usually covering the digest of the presentation and the verifier’s challenge). This produces the final Verifiable Presentation that can be given to a verifier. Essentially, this step adds a layer of proof that the presentation is furnished by the holder in possession of the credential, preventing anyone else from reusing the presentation.

**(Non-encrypted) Request Body:** _(JSON)_

* `blobId` (string): The identifier of the presentation blob from the create step.
* `signature` (string): The holder’s signature on the presentation data (blob). This is typically an Ed25519 signature using the holder’s DID private key, or another signature corresponding to the holder’s verification method. It proves that the holder (who controls the DID in the credentialSubject) is the one presenting the credential. The signature usually covers the challenge and the derived proof, binding them.
* `publicKey` (string): The public key associated with the holder’s DID that corresponds to the signature. This helps the server verify the signature. It should match a verification method in the holder’s DID Document (for example, the base58 or hex public key of the holder’s DID). If the DID and keys are registered in a DID resolver accessible to the system, this might be used as a cross-check or may be optional if the server can resolve the DID.

**(Encrypted) Request Body:** _(JSON)_

* Encrypted raw data payload.

**Response Body:** _(JSON)_

* `vpObject` (object): The completed Verifiable Presentation. This is a JSON object containing the Verifiable Presentation data, now including the holder’s proof. It will have a structure like:
  * `@context`: including both credentials and security contexts.
  * `type`: typically `["VerifiablePresentation"]` (and any specific sub-types if defined).
  * `verifiableCredential`: containing the proof of the credential (not the full original credential, but the derived proof or reference created in the blob step) along with any revealed attributes.
  * `proof`: an array or object that now includes the holder’s proof. For example, one proof might be the `BbsBlsSignatureProof2020` from the credential (already in place), and another might be a `Ed25519Signature2018` or similar from the holder. The holder’s proof will include the `verificationMethod` (the holder’s DID key id), the `challenge` (nonce) if any, a timestamp, and the actual signature (`proofValue` or `jws` depending on the format).

This `vpObject` is what the holder will deliver to the verifier (e.g., via QR code or API). It is a complete Verifiable Presentation that can be independently verified.

**(Non-encrypted) Example Request:**

```json
{
  "blobId": "a7c8d901-2233-4455-8899-abcdef123456",
  "signature": "MEUCIGDz...AQAB",
  "publicKey": "DIDHolderPubKeyExample=="
}
```

_(In this example, `signature` might be an Ed25519 signature in JWS format or raw base64. The `publicKey` is a placeholder for the holder’s public key. In practice, the holder’s DID and key could be resolved by the server, but it’s provided here for verification.)_

**(Encrypted) Example request:**

```postman_json
UbWc320b8hQabsj9emEFIM3lu20Jc0m_8DA-NwRbwA6x5_DZgycjoPbLf8xbS3fYb1Pz4ZrNu0CNseQCX-XY3A9b14GtZpJ7cttefpH5D8UkNTVHDGLtWZohkonisi2CG9U94WegeQjCHQeuNm5dJLubRtFvtPIxlexHGeqd5xp4TV4T23ZMHTib8_hlbFAvrgwygNWeg-Y6W80DEE8DMQExoUFov4Sdx-h_3B0ihFO-VoTCkvuKVnTOsPOhABoZxfeJfuk6h5QYBcbaEJgeQ2vsFfhetiQ4urIdVXJ6iDrItSs1iYvoLHNsbt0TyVZiOsJLHwFpMa0vTYps6I2Rg_Dl3WP28jZSJjBx5h4fBWIdUJR-8bF8ZdGEDF8cWQYD3csaIyM0eNghOT-TpmyeW-7qtL4Lic_7u3PEDaY=
```

**Example Response (partial VP):**

```json
{
  "vpObject": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1", 
      "https://w3id.org/security/bbs/v1",
      "https://w3id.org/security/v2"
    ],
    "type": ["VerifiablePresentation"],
    "verifiableCredential": [
      {
        "@context": ["https://www.w3.org/2018/credentials/v1"],
        "type": ["VerifiableCredential", "DrivingLicenseCredential"],
        "issuer": "did:zid:123456789abcdef",
        "credentialSubject": {
          "id": "did:zid:holder12345",
          "fullName": "Alice Chen",
          "expiryDate": "2030-12-31"
        },
        "proof": {
          "type": "BbsBlsSignatureProof2020",
          "verificationMethod": "did:zid:123456789abcdef#bbs-key",
          "proofValue": "tZ3v...WXYZ==",
          "nonce": "MTIzNDU2Nzg5..."
        }
      }
    ],
    "proof": {
      "type": "Ed25519Signature2018",
      "created": "2025-04-07T02:00:00Z",
      "verificationMethod": "did:zid:holder12345#key-1",
      "challenge": "b25f5e9c-7d7b-4f1e-92d3-123456789abc",
      "proofPurpose": "authentication",
      "proofValue": "zSign...abcd=="
    }
  }
}
```

_(In the `vpObject` above, the `verifiableCredential` array contains a credential object with only the revealed attributes and a proof that those attributes are valid (BBS+ proof). The final `proof` at the presentation level is the holder’s signature, which includes the challenge from the verifier. The combination of these proves that the issuer originally issued a credential to the holder and that the holder is now presenting it with certain info revealed.)_

**Error Cases:**

* `400 Bad Request` – Missing blobId or signature, or signature format is invalid. This can also occur if the holder’s publicKey is not provided when required or does not match the expected format.
* `401 Unauthorized` – The signature provided does not match the holder’s DID or public key (verification of the holder’s signature failed). The holder might not be the legitimate owner of the credential if this fails.
* `404 Not Found` – The blobId is not found or has expired (perhaps the create step was not done or was done too long ago).
* `500 Internal Server Error` – The server encountered an error combining the proof or verifying the holder’s signature. This could also cover cases where the underlying credential proof is invalid (although that would ideally be caught earlier), or other internal issues.

### Verify VP

**(Non-encrypted) Endpoint:** `POST /cred/v1/vp/verify`

**(Encrypted) Endpoint:** POST /cred/v1/vp/enc/verify

\
**Description:** Verifies a verifiable presentation. A verifier (or any relying party) uses this endpoint to check the authenticity and validity of a presented credential. This involves verifying the holder’s signature, the credential’s BBS+ proof, and checking the credential’s status (whether it’s revoked or expired) and that any disclosed data satisfies requirements (including range proofs). The endpoint returns whether the presentation is valid and may include the disclosed data.

**(Non-encrypted) Request Body:** _(JSON)_

* `vpObject` (object): The Verifiable Presentation to verify, provided as a JSON object. This should be the full presentation as obtained from the holder (for example, exactly what was returned by the **Submit VP** step or what the holder’s wallet produced). It contains both the credential proof and the holder’s proof. The service will use this object to perform all necessary verifications.\
  &#xNAN;_(Note: In some contexts, the VP might be passed as a base64 string or a JWT. Here we assume JSON for simplicity, which aligns with the earlier endpoints.)_

**(Encrypted) Request Body:** _(JSON)_

* Encrypted raw data payload.

**Response Body:** _(JSON)_

* `verified` (boolean): `true` if the presentation and the credential within it are cryptographically valid and not revoked/expired; `false` if any verification step fails.
* `verifiedAttributes` (object): If `verified` is true, this object contains the attributes from the credential that were revealed and verified. It will map attribute keys to their values as presented. For example: `"fullName": "Alice Chen", "expiryDate": "2030-12-31"`. These are the pieces of information the verifier is allowed to see and trust. Attributes that were not revealed will not appear here. For range proofs, the verifier might not get the exact value but knows the range condition was satisfied; the response might include a note like `"licenseNumber": "Verified in range 900000000-999999999"` or similar to indicate a successful range check (the exact format may vary, but including the fact of verification is useful).
* `status` (string): Additional status information about the verification. For example, `"valid"` if everything is good. If `verified` is false, this might contain a brief reason such as `"revoked"` (if the credential was found revoked) or `"signatureMismatch"` (if a signature failed). This field is optional but can help in debugging or audit.
* `errors` (array of strings, optional): If verification failed (`verified = false`), this may list the reasons for failure. Possible errors could be `"Invalid holder signature"`, `"Credential revoked on 2025-01-01"`, `"Expired credential"`, `"BBS+ proof invalid or not matching issuer key"`, etc. Each error string describes one issue found.

**(Non-encrypted) Example Request:**

```json
{
  "vpObject": {
    "@context": [...],
    "type": ["VerifiablePresentation"],
    "verifiableCredential": [ 
      { 
        /* credential proof object as seen in VP */ 
      } 
    ],
    "proof": { 
      /* holder's proof object */ 
    }
  }
}
```

**(Encrypted) Example Request:**

```postman_json
hxtxXa3VqAS51w6K6CIDyztFPhEc52-pljUIpjxYLY2AgtAZfZSSbnn9AfTM30cOISXfupnYryXoSljpw-z-FbScRZTzuzXbp8f3r6d-C2myBiGQsAYjubnmmwYIVWZfzg2lq-aFmgXJjlisP0tbV-1gDB2OGCc8lNM-KeIL9_vxaxzXm7dfCZDrQFmTdTL4W6BUbKUpo2E0as63D4o5M5aAL0HzR9TccnRAN2C4Qe3JMi_x6ElUrN_qyxs_fyoaEf6jUlMAGnFMpv5GK_tutuMiRJfv0cUeyeG3lvJ53h86GQtWE6lpAJZcBLCkF9q-QSGQZ-AiRu1_1UVW2mgiTs5LdrttOBPfQRHMF8dkbD_A71gt-ZMNAQRoaJeFF5SN5syliqY_zAehpem3IfsoTcMd0DKvS2bhYCEBsVnLuQ-4Z2tyIda0kCditlCwg-2FinBSGc8BquVrRFB1Q9XGbZjR8kPPLbFx4rH9q6kCToYrRsvXzcLue5Viis0xX3_IEq4pv3PSTNcG9JDolAZ6tFU-fHBTzhb8LEJkan_oX54dmEdaKUXnnBWqfq3s7tYHH894v8PTHkJgxPnEMWQsSvHXkGXaYsiMLRopFeuME3XZOCeb1E6dQ1MfEyDzMpOMXqG2Y_0F9RJcIQcHm1Ky5ujJPtu8Byx1sIvAjUWjJCMfpNafhcJxpzjM3bhRj9OLKYW3Sg6-mCWyQGAvzPFVWnjvHzPVkUmJ4oXDwKjr5CICGFs6kQL5CD1dlbWzL_TL4zHRw4J0OVCnlDxsHD_87wnf0c9xY8DMsQ0PIB5MGlVtx80gxndbKbuTIUTMmsE5YtfQGzyRMEeyu8GrsIJvXOzF_odFjuZ2cur_sXRmfFdkE0hk8ycuB_qJiZZPLe-mDrAo2DHwPsI5vCNDvPMYSVFF2gzx6CztRTb4elRH9S72q6Y2-1R29w0261k_jvR4--UWKpijdkpX0UxFsc_uyF0GYHXxDQQsZTaVVHO0P1cQ43WAxUfYDrpDEDJ04uC7Z-uAK8erJ-jfByQ8QiP3mVWWQOvJq4vmPPUG_5CsubFvKxAVsF8pqaKCeWVQfZ5H6VCwIMtDTIy6aks0tzdKAabbsml7JXPqPMKmaZd-67bhWWPMpWIeS13_zfbRqaGHfuyf1p9EE5q4vwYXRMlyXcC5SvsVVW5INe8ezIAGQ7KiqQVgjrbyiZeq4HiFShL5yLq9NAnm7vTCcDtUwCVU6m1sTU4wlo114UNF_PmUXoDVteJLYrj65XkpB3mRCu728gzWXsM0oUm3PWecaqRJDWm-KqMA0ljhgmrCyGPv8n-sf2udy-SUzD7nwYl5E6TKkkqcwwbYh4Y647RU6eXpg8FyBdCFZLyWaGUXQE-f8S8ihwc8oRgb9088A4JRO3GnjtCEAiQkK6L8nf-eQZRAEw9Z_3CghKuU0V_2Hg8d4es2XbODvM8M0L1oW3LEVPcejPYvoTCS6Rv-9BpV-hoDcpuYtkXHZlUbM5IU34U9C5D4Gb-v9CXUtS6SfEw3DI6G72dkJmjh37Xpf6kmUEoG4iwogdUcfpe9zQBTsKFWreNX1RHYe4Cy7o1ShpZJlmuzGRstFt-h6Qp8YrsnVYLJERfaMslnRKDaD_FbGTZsi_NE85sX1IPWbQGfBRLWB84KCtPDKmBU1UbHS4nMCELXB1XEYIj7v2-_b7oyLlsT8aEC2TBaaDmgQjdjFVXQ1n3r_x_65UkI6DBEFvWGBlARZzqYWKd2wLgjuPhyglri1HqK1Nbe1hyftrefSeyJ7PpIrv2ZJo1OYfKSXhEFR5qPaT5DrfwJTXc4mySXf4ld3mz7bVMG1wTSWXlGT6Cg1IiO5OuigL8DQbqR082Ycb3n1S-vcxBGJ_pVoKR8r87P_80dggZBwfPWOFNcbkq4gL2VprVTI1mjU9W8qh3IW6JiBTLZbkw-YrgAU4--ng8woJs0dCqwPOLMcwcSE-JA9jlpGifBa1p0pmVyRuufUQl-BGVRUQ-5B79s3RRbJ5j0jMt5T6wOsAKUtNx5ZZdqUl5TQFsW1kOtl7ikq97qIAajzytbWyBzna8cifE8QGmuhT0AXZWdzVXW_f8LliOLy6Y6EkyPU3RmawzuzNKfJ6gLQznL1fTeKKdc9BJiXEJoWSJpI-NZ-YnNDykinQz7T4gGyQypHN-spqqsX_buy1PS3XfRCbybv26IHW_7mDrzr5iX1QAb5ToOBZsCKh-VxbfgyX1-vAj6p75wBVMl6Fy7T_vNId3u9gZW4ouU-eErL84qcGcfZYFEC35x1omLli5Gm3FrJ1hdk5wX1qkbFuImqGwJqSChLQhcRLBHE-1XKXSDwHb2O7LjnJIcKhjWTYNvlTG4wwiZsFxLUIuPUDaVdFHCNETAg0j1lXOKXt3_qWMGfxc7lWwXu1cgwVJDAozfuByKJ_8t9Lcbn2zbYVPB6QYoB9z5Mp9zL6eVv71ZeogWlhy4MUlQTRfgJaDNTaTy5h01neqtCG5Rt2ZG5YY9I_3EvcXFbJ773czIzsA6AJnISLmUqNduQo44zFupiJ8MNC-HfcObOpcGM1nJwBvIb16X0WD20va6fs_rPfIHfDHSOF-Q2avFhTJDWb20kZVhjVhZl5IBi_b49P2CZWAjzUETgCah1KPlKGVFOWvqzRI2mHBaVbDbu4NULy-XGVJilbsdBHklJz_byU5WdmZyx2iONGtpMEavvqxYoYiafOb_tPS8qNGT6rYposqkILjsXeCwj7Jy2Xj8AJovtQSgT8xC3s3jrFfu99TNumRN1vCGmTeoZdCLq1IGwsP5BVnodICOPTvBaY_WKdkcRj99V31idCBaTtBSFPIIfB60HJ5gCbVoLDZCZqouJd59wcSoPox-sT_5OEdsKWy3Kj7VKpeIDLFpt1RokZ4fiXKv9yP-66XtSgvNE8XIWNpNYf4GqELPlnlrNiazNYy7gPrYshAd1j2cR26GxaLVAM489ykVtGU0xV0x5-QtwHRr0_p-MATvNXXKTwIXtLSuBHQoiYDzBxR78pzuq-DR3XCMWfQuUhOc1RV4-AjaKcr0VEVfWHAEuEP5BiRIXOMmnWfQbBuL9qHuVIqokVK7W_eig9_i_J7RiewFYhO5GBuNVfjbGElyEnBqHPHDCCPi3351UEJFC_jga_2AV-UpZJefbs1yiFAL-rfeZOVczkGdUX5I4BeTe7ONxWOS_iDj-e9zwlkZtuwWamvlPZvbRsJcbyLoRhMV4khDLGGynr3dLgFzDxBoArDIxu8HrSDG6L6oZlgYMw2J58HGJlQ2vY6MeFn5r6Xjpd0wk2ZkFzfLjpjgZ_lVVbw6UspfmIQChngGzOb6wXcJS6TeYBIQ8o_8AO3edBnjmE1pRWYWR8BeDnunPAln1FCMlexCYSijLMLX98kIGtTuo_n03L9DujDhJdiAIEOJAnkI_z1louBOyUMQkP8NuOGBQ9SKFYkVNDn_qM7Boelxy3Z-unNfMuQJRHHKg2opRvEZ2IWdXFcZ5ow4Rr6FzlMO0SqWOCsA3K6ZQZxUZVj4kyCfvb0Ph7XJH-LDyRY3_67435QWoUPBfEypsF3zfY9zvugtNdVEosxNvLYLa7iX8Q-jlpHq7mfGwmnTCUii3-TP8ZyNxdu8Y4zdfZHDpgl3aA5xL3D7ZyFPl4x2WKqffLqsVgbqTe5uAy3D89QqsFT7JHqwxV2_DX12m7uohD34MExDm17eHBovy2wuy6IUN78V88LkxnS6Axt28m4dG7dpypgvE5zHK8jkPTVnZeQf9arTltAJKcOXUt9jQVBmc-UZ2PUNTzOjbV3TTnmA7gSOXI2XlHY_lpJ_XxUPdIbTQslW4FmERiUKSg4O11m8SBs5KP5pzpoDJejdKarnTQlayLacjbW4835SNKIJyfQwPIHV2i5Xj9r3oYvJPwVO-uygiSM0bevGJZ1gzL6rE0ykZ7GBBoygve5BrV_VaL12riwHlbs9U788_evc-OrJASA5kw2I28jXIGtMUKoGZK02tcW_ZgAQHwbteXH0ymweqlgTlnbRtuvFzBGLrtUWoRVrFkuOuRzf7zUESq6tUQue_6CnsLJYD-cG2--9R6DeqnfLmtIgAQ_qTzl7hJQvahrlRHp5NAs3u3Dit8ZDtIPyMFpyjY30Qr9SnHr3ZgLh_xlx_1dbixf1dTmbfVG85tR0RSI50nZ0_vU4fuuZ4OtSZly_0b7YlU06NFZ6uRYKOZJudTeGgdaCVx_3Ao74aCv2Ssxsfs0g9CCC0TsM_0nQrF2aUi8ddI1n0sjylfdS0AkR_d7XiwcLNqZcIftaB7nsBxxq0_TLpaZl3AAfwSemIJYkVP33QFOAeY5inqUlU615jCoTAVSSmXXuSL-s-zpXOOSr__C510gfMc76DlbomxrdzM-xI-o8ScYK5RvvvH62VGjOy4if_SNhvrfK77bVqMbzWwQsoIt-ExXomKZGSZL8kZaOI37ae83zM_a6CPzut-qIvj_v5zoNn-JEbCT4w49xQGj9HNAxLjbKz35IGdQpdgzKvligfzKHdGhe_04JxMVIbdttdtbazFuenzEcU-vAj1ENqDF6oNKKCWxfQTOoohIrfwuuiEovaLdIRhd9nUqWLBSRQQG8faDi1tcdini-mXe420zAQibkGWkIH4tIvMGZV8LuxpKgTHQijYZdv59XZZixOCrTaveLXydOqPCHf40N3x5bdmnmsfaKINO9JPkX_xG1i7W8CAiK4X0YYsxKFpNv_mwToam0qJdiO6JE8fKpLpBcD9Se-T43IjiNB3OW2lRJxgk1Gbq7Qt9Z63vUwTIbWRoeQtLjFynFwNMCWIzx028OpWrM9j7RqEqZUaXoPl7CmxoqSJvPbsC8o4ab-D6Yye4yfJ7ukku4XMv7f6-UkPauxV4aBjS4MIfg6rH4LJyh_qXl_XfPcc906ppi_I4OXat7L2xsmVoyCyjbcGTawg1OgAIwN8uso-Z1glZJUhTZXznk180yK8ZaS_eEnGSliT0Lnont5eTjuwqcI-lnjvNkHII7xuPad87lCyqJTDk_U0ABaJD17LIYvhdCMCREl1_uJk7kHf2eHP1m3jVp3RKsSLlnOl6tYHm0TnUgTniRi1QCzaf-faotaodUdTJrKOF4TzBng5SywjfhXnq1Bx3Am7iub-NqEhDuKqZU23DkNbsLlAawRQpFN4jcMk465OR5cRceB57HIN0NmYICfAxyfAh3onLvnM4bYsbPN4-2R4xDfsFMwLCrq2l3fS5bC5HybXv7PGY_6kilsB0qnK-SIv8OhY4LvA0gbE7RvV3YOzpImtvMUdA1DCX0t9DgfSZLcHn8dlVKVWNaXRqbCmxBGGn6oqFi2nV2INt1hXuKQi11DE6vkDS1HsSk8vENd0hfc7XXpBkGN9mZ7zBydabPNarUpMpQlaUaCcSK5itd3s0I-hafml05N9iB7_Y77_QGgmnT434wP-9VDpmobuzmaiqvb-p4F6ZN1zD6N3nlrF4t5M4L9-TewouSqVG3CoQa7diN_e2vwYuxcY6Jh3hYWKFrJXoh2jZ9TH181T9QBnK_B_FabPqfrt7aoK9xlvyEJPvtkpfoqhhoLaeUaMmsHUqYalULnF4s3Hvm04iH7Zz4gVQBwYUtuMFJyyb05i-J3iQuNVSNar5ALKeOq2bZ7PDbM11N6AyHvhqucuGfCNGEcbhD70aB9mAORX-OvxjJTHMZoZE9eQzyVnOi4G2d_tYPm9sYEwnc2I63MHPIamPafKRo2MtpgBM-ow6i1hgMHm3umCyahHouQz-vFxS-gkNpedUJ7FNjKaVHxA4oj8B2rzG47ujLtIk2-wILdfWEpuRyU9KMT9d8WQJ56JNQGJ6vK6MxENDaapwv7UyMzUsDTEqmdpgmAEMTy1MlIdwfZVac3JuhG8srYCxPpeIBVyFXTAmkBOZQMCH4JE5Tzy5x5QraJ2aW6fYgqIKD3Ly43P3PG2t4XY7SvcIi1L4lTRXGo_V5hcYKDLXOhSFPn8oYM3DpQK75Ex7s596kONQsvFqbmKaavdaU6OQCd8RHPPFrS8YfbUvTrG6Q6IvMhtMglliW_XtgywvPnQh4jUb9Zk4tgHAyCYzXvxibvh8u4ipXKPSchMVQKsdDVKgZwYeSH0xngHEq1v6MIHPXUwgHGwrPghvuEuLQfFoADMNvOZ67qCPhEcy2PicnXvnPnB4hUsbNRDoSS26ewdNW73j10rfl38dMFm71E4VXVb1jc5IK7uSEr7o0TFPYcFdp8NW8mdlRn31mrWBlCsRDaiYEVc4oV7i5WOhXH1Ua-zO_evuyyBoFvC5hjXCluCYPzm6bxIovCb-VsTPKr-NT_3jMaKXXeBrB4qA1Lf_M9hBpJ65BjwcrhR0c9CeMfhLaymuLZh0c15-KtKMlB0tgwfjW28Bw406z3pSoywnU1Wem40K5jNTKqkUNdSprscjRsIoqIN2BntJAyX5Q2ARXj2F4wJYVNt06QfARc4ZkXvmWCWoAwjro5aRcpKqD2siAqbBGE5brpk9gQBrt9n-SRzxRoMOyU_TYCE_qoikSKBN5MkDVnyFxZGtlGV6q3wPqpYTs6fpzHsbXyG1RZYfGTlvhQSEaW0Rdl-whi4Zovvr7jqIx-qLZo_bI0FyGkxtEK1upmsRnqMuEYlPFUmRY5DCF80eGRtGicZPUtjTBmGTxV6Mq5SuZ2PeVjUMpVicjAQ-UG3SrKcdNUqhXf2PLpsqz6aFTcEzoZoTIORhyTWAz-_gPx7yQXzArm7NWFsxlTXYK8a-6NX4zsWl0_NnwOjotJiI-h-KT8o4wmWm9zpEreuWbgP_WovLSMLpKTJhxKvSfZ_c1iWheEnR3LTTODGSl9cZYhbV4b7kcq6jHrU-UrodMhVREe4ao8hShH9rx7UgA43HH6k5He_8-TETuaaKdiomcLM96vc5Olvh_E2_vxUjQoPq4ylHUlRp3d3La6BuaYdvuXKsywUKqr5X4zIlKdHsnOI6N4M2Prvf7yFPGIIO0vNlvKYr2QRoESzI-a7j1fUv4NMY9kv_RpWE5FQ9qyw_MFs2jMYVqsxBJ3wcTE7ueTPXajIT4LaL9UxzLi_zmLhvY1a-bLfnIJrYIj7jiSv4TZbZdTZInByTMghnovaD4JqxsTTw2XwloSExlEQa5tly964XZPTBP4GQcWAHjWzd0m2i5HB9YmTYKH9W_pnoIgW7fKh4wJpvMwBbPNrXhQf7a8SG3alXHjeBfsThAaWDMpioMywJ4SgSVC7_5bOPP89k_niI7iQ2xSghUnavWgFqXp3KxMNyi2dRz6ZePtuUb9fXTnu2M-ueWoKI4J8xRCgkRd5czuTbFBjZXkB2kAU3PsFj2drM2uwaJuKS_lxXvEE-IkANZWphlQdeA49xqO1JzA5DNdJT_hW0Orrjk7NjrHVAZMebJ85HaK9vSVxoIdA-9M60lfx295mXJgSwguO8X1wuJPbc55JRLph2q9CzlTVc1UXovzaokspgNBaMhDeE0lBXLITvJUC9Ydt-1n7aJHHdpG1qY5AQiA_Bcb_QJQ438L7FclzSLSPQ7CMHzFQg_p6eMb7st8yP-F-4pyJ9c3HU-zrOsZeWi4-WlUdP4UMuqZudq36ELhtVZplHAHFSaZ0_S1tF8xxvPiknFnaVaNyncsihAB8x5DxEb5Q-01r8ZezEKC4nGds20Mr4dhbHoi2DhYWWVuTPCbuam8y0zkavANfgRnzjJmHsJ11S3FMJhLW6xTiqVJGlrXB24OXVbGV4ftpO12wQ-8kBrbGWHpzAv7GZyzCraUs5RBLdv5RnucNlDAYuMJPlox1HkEHxsiJ4XqR7RyMKME0MkXcnWjNwpuzt7lP2TUqZI-NCizJzRBPfiOZ7FFBYwRIaxyxzL-bcHtI1bDtcbByWryoujl6Wu3d6T1R6LM_XoIT8H_UmEd5A8P9S_FVTWK_LTVDvEjNwjzbSvX7Oh480xgD8NFI_geTMGBu1lpmjygwaIofOi8tH28jLgupSQHEoIpLcvLQRsBNim7D7mKe0H2tw82ruk7yYfnmlovu5yqPMKa4u2XwxtUJkkRd9OCEnhvPh-WYXkUVOeBNrsF5ffYSr_Yd4oyBYE8wg0vo2qo2a4erQoYIvAqfTc70FIRYV9EOHYhddJevVyxD4pKxgThTetmBJ51bAHyUESHB_Z56yyNgEyWMtorUmaHSPrf7-gEpHoMo6hXmCrfg4uZfEE4mA4DUsF9bwUVOdmAWCH2VHDl4_rEE36nwv0Rkno_LoD5iTdY0-nZogmndhs5Vvl9DbVUWr3DtzSnq3QpkAGNnmrTbBHC58qQF3CL0Y7BWV2Xs14GoZclzVKRa94QaVphOMSD_alysDncSjo0SEgynSoDQyZiss9j4nceWNLEmbBSHk0qoTVheuYNM4-XI2D9ndZFlcxDP-EbJJD4nr4_LKq9jGfXytdYD0TvONQMyo3HYKkK2ARl1_9xaIGUcqdUU7r5JgUsyrqoLGfi8UL_-fWGaKGlmrFzlo5FeLpg4Zi2B0GUbh5AS-OE_dfNPQfJYa7KLyOAuLeR0pQI2PNwxd3f1MTfRspmx0gH90BEil5t0Pssvn4eywoOa68xzt0axTkePd1SeZjvCIhskS1xrI=
```

**Example Response (verified case):**

```json
{
  "verified": true,
  "verifiedAttributes": {
    "fullName": "Alice Chen",
    "expiryDate": "2030-12-31",
    "licenseNumber": "✔ (in range 900000000-999999999)"
  },
  "status": "valid"
}
```

_(In this example, the licenseNumber wasn’t revealed, but the verifier knows it was in the specified range, hence a checkmark or note is given instead of a value.)_

**Example Response (failure case):**

```json
{
  "verified": false,
  "status": "revoked",
  "errors": [
    "Credential status is revoked as of 2025-04-01",
    "Holder signature invalid"
  ]
}
```

_(This example shows a scenario where verification failed. It indicates the credential had been revoked and additionally the holder’s signature didn’t match, either of which would invalidate the VP.)_

**Error Cases:**

* `400 Bad Request` – The provided `vpObject` is missing or not a valid presentation format. For instance, if required fields are missing or the JSON is malformed.
* `500 Internal Server Error` – An unexpected error occurred during verification. This could be due to an inability to reach the DID resolver (to fetch issuer or holder public keys), or a failure in cryptographic verification that didn’t properly set an error message. If this occurs, `verified` might be false by default.

During verification, the following checks are performed internally (each must pass for `verified` to be true):

* **Signature Checks:** The holder’s signature on the presentation is verified using the holder’s public key (retrieved via `publicKey` provided or via the holder’s DID document). The BBS+ proof inside the VP is verified using the issuer’s BBS+ public key (retrieved from the issuer’s DID document, presumably via the DID Resolver).
* **Data Integrity:** It ensures that the revealed attributes in the VP match the values that were originally signed in the credential (the BBS+ proof ties them). Any tampering would break the proof verification.
* **Non-Disclosure:** It confirms that no more information than requested is revealed (i.e., hidden attributes remain hidden except for what the proof inherently reveals, like perhaps the bounds in a range proof).
* **Status Check:** The credential’s status (often indicated by `credentialStatus.id` in the VC) is checked on-chain (or in the relevant registry) to ensure the credential is not revoked or expired. If the VC has an expiry date, that is also checked to ensure it’s still valid at verification time.
* **Challenge Check:** If a `challenge` (nonce) was included in the VP, verify that it matches what was expected (this prevents replays – usually the verifier provides the challenge and knows what to expect).

If all checks pass, the presentation is valid and the disclosed information can be trusted by the verifier. Otherwise, the presentation should be rejected.
