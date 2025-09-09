# API Reference

This guide covers the **Zetrix Verifiable Credential (VC) system API endpoints,** organized by the major functions in the VC lifecycle: creating credential templates, issuing credentials, revoking credentials, managing **BBS+** issuer accounts, and creating/verifying verifiable presentations. Each endpoint includes its HTTP method and path, a description of its purpose, required request fields, response structure, example requests/responses, and potential error cases.

In the Verifiable Credential (VC) system, two types of APIs are provided: **encrypted-based** and **non-encrypted-based**.

* **Encrypted VC API**
  * Secures credential exchange using hybrid cryptography.
  * Combines RSA for key exchange, AES/GCM with HMAC-SHA256 for data encryption, and X25519 ECDH for shared key generation.
  * Protects sensitive data such as holder identifiers, metadata, and proofs with end-to-end security.
  * Recommended for high-assurance environments, cross-domain credential exchange, and public verification.
* **Non-Encrypted VC API**
  * Operates without cryptographic wrapping for faster processing and simpler integration.
  * Suitable where data sensitivity is lower or security is enforced at another layer (e.g., TLS or secure internal networks).
  * Provides flexibility for lightweight operations, testing, and internal trusted use cases.

## VC Template APIs

Credential **templates** define the structure and metadata for credentials that an issuer can later issue. Templates are created and registered on the Zetrix network in two steps: first building a transaction blob, then submitting it after signing. These APIs are typically used by the issuer.

### Create Template Blob

**Endpoint:** `POST /cred/tds/template/blob/create`\
**Description:** Creates a new credential template and returns a transaction blob that must be signed by the issuer. This blob represents a request to register the template on the blockchain. After obtaining the blob, the issuer should sign it with their Zetrix account private key (Ed25519) and use the **Submit Template** endpoint to finalize the template registration.

**Request Body:** _(JSON)_

* `templateName` (string): A human-readable name for the credential template (e.g., `"DrivingLicense"`).
* `issuerZid` (string): The Decentralized Identifier (DID) of the issuer creating this template. This should correspond to the issuer’s Zetrix DID.
* `credentialFormat` (array of objects): An array describing each attribute/field in the credential defined by this template. Each object in the array represents one credential field with the following properties:
  * `attribute` (string): A descriptive name of the attribute (e.g., `"Full Name"`).
  * `format` (string): The expected format of the attribute’s value (e.g., `"string"`, `"date"`, `"image/png"` depending on the attribute).
  * `key` (string): A unique key or identifier for the attribute (e.g., `"fullName"`). This will be used as the field name in credential data.
  * `mandatory` (boolean): Whether this attribute is mandatory (`true`) or optional (`false`) for credential issuance.
  * `type` (number): A code or indicator for the data type or category of the attribute. (For example, `1` might indicate a text string, `2` an image, etc., as defined by the system.)
* `version` (string): Version identifier for this template (e.g., `"1.0"`). Allows template updates or variations to be tracked.
* `remark` (string): An optional remark or description for the template. This can include any additional information or notes about the template.

**Response Body:** _(JSON)_

* `blobId` (string): An identifier for the created transaction blob (used to reference the blob in the submit call).
* `blob` (string): The transaction blob in hex string or base64 format. This blob encodes the template creation operation and must be signed by the issuer’s private key before submission.

**Example Request:**

```json
{
    "templateName": "DrivingLicense",
    "issuerZid": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb",
    "credentialFormat": [
        {
            "attribute": "Name",
            "format": "STRING",
            "key": "name",
            "mandatory": true,
            "type": "USER"
        },
        {
            "attribute": "Class",
            "format": "STRING",
            "key": "class",
            "mandatory": true,
            "type": "USER"
        }, 
        {
            "attribute": "Gender",
            "format": "STRING",
            "key": "gender",
            "mandatory": false,
            "type": "USER"
        },
        {
            "attribute": "Age",
            "format": "STRING",
            "key": "age",
            "mandatory": true,
            "type": "USER"
        },
        {
            "attribute": "Id no.",
            "format": "STRING",
            "key": "idNo",
            "mandatory": true,
            "type": "FIXED"
        }
    ],
    "version": "1.0.0",
    "remark": "test remark"
        
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

### Create Template

**Endpoint:** POST `/cred/tds/template/create`

**Description:** Initiates the creation of a new credential template definition. The issuer specifies the structure, attributes, and rules that will govern credentials issued under this template. The response returns a transaction blob that must be signed before the template can be registered on the Zetrix blockchain. Use this as the first step in defining a credential template.

**Request Body:** _(JSON)_

* `templateName` (string):  A human-readable name for the credential template (e.g., `"DrivingLicenseTemplate"`).
* `issuerZID(`string): The Decentralized Identifier (DID) of the issuer creating this template. This should correspond to the issuer’s Zetrix DID.
* `issuerPrivateKey(`string): The issuer’s confidential private key used to sign transaction blobs and credential data for authenticity on the Zetrix blockchain.
* `credentialFormat` (array of objects): An array describing each attribute/field in the credential defined by this template. Each object in the array represents one credential field with the following properties:
  * `attribute` (string): A descriptive name of the attribute (e.g., `"Full Name"`).
  * `format` (string): The expected format of the attribute’s value (e.g., `"string"`, `"date"`, `"image/png"` depending on the attribute).
  * `key` (string): A unique key or identifier for the attribute (e.g., `"fullName"`). This will be used as the field name in credential data.
  * `mandatory` (boolean): Whether this attribute is mandatory (`true`) or optional (`false`) for credential issuance.
  * `type` (number): A code or indicator for the data type or category of the attribute. (For example, `1` might indicate a text string, `2` an image, etc., as defined by the system.)
* `version` (string): Version identifier for this template (e.g., `"1.0"`). Allows template updates or variations to be tracked.
* `remark` (string): An optional remark or description for the template. This can include any additional information or notes about the template.

**Response Body:** _(JSON)_

* `templateId`(string): Unique identifier of the credential template registered on the Zetrix blockchain.
* `txHash` (string): Transaction hash that records the template registration on the Zetrix blockchain.

**Example Request:**

```json
{
    "templateName": "PASSPORT MALAYSIA",
    "issuerZid": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb",
    "issuerPrivateKey": "privBrhqgrF5zYTBQmGQnhMvaPccXRpnc31W29XhaRmCwoz44SqVeNM9",
    "credentialFormat": [
        {
            "attribute": "Full Name",
            "format": "STRING",
            "key": "name",
            "mandatory": true,
            "type": "USER"
        },
        {
            "attribute": "Class",
            "format": "STRING",
            "key": "class",
            "mandatory": false,
            "type": "USER"
        },
        {
            "attribute": "Age",
            "format": "STRING",
            "key": "age",
            "mandatory": false,
            "type": "USER"
        },
        {
            "attribute": "Gender",
            "format": "STRING",
            "key": "gender",
            "mandatory": false,
            "type": "USER"
        },
        {
            "attribute": "Id No",
            "format": "NUMBER",
            "key": "idNo",
            "mandatory": true,
            "type": "FIXED"
        },
        {
            "attribute": "Height",
            "format": "NUMBER",
            "key": "height",
            "mandatory": true,
            "type": "APPLICATION"
        }
    ],
    "version": "1.0.0",
    "remark": "test remark"
}
```

**Example Response:**

```json
{
    "object": {
        "templateId": "did:zid:01ac71495075ad9b6d611315e39b2466647281c448d72ba7aea2437a2f0eafad",
        "txHash": "0003ced4581b2dfe10d307d59659bed09cdb01a46066bf84b0f7cc2cbed96eb2"
    },
    "messages": []
}
```

### Submit Template

**Endpoint:** POST `/cred/tds/template/submit`\
**Description:** Finalizes the registration of a credential template on the Zetrix blockchain. The issuer must provide the signed transaction blob (from the **Create Template Blob** step). Upon successful submission, the template becomes available for credential issuance. Use this after signing the blob returned by the create endpoint.

**Request Body:** _(JSON)_

* `blobId` (string): The identifier of the template transaction blob obtained from the create step.
* `signBlob` (string): The signed transaction blob generated from the create step, required to finalize template or credential submission on the Zetrix blockchain.
* `publicKey` (string): The public key corresponding to the private key used for signing. This should match one of the keys in the `signerList` from the template creation (usually the issuer’s Ed25519 public key).

**Response Body:** _(JSON)_

* `templateId` (string): A unique identifier assigned to the newly created template. This ID will be used to reference the template in subsequent operations (such as credential issuance).
* `templateHash` (string): A hash or transaction ID associated with the template registration on-chain. This can be used for audit or verification purposes to confirm the template was recorded on Zetrix.

**Example Request:**

```json
{
    "blobId": "320AE2388C9F38DD62F1633AE2A8E2B6",
    "signerList": [
         {
            "signBlob": "F0EE813BDC5D033C5017DC2DDC2EB1937824708C3FD472AA7007BC045D92971536A9D33C890AE3301EFCDC705EAE4662A400C3F515A73E46FC44E810CE84F805",
            "publicKey": "b0010d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb43fad909"
        }
    ]   
}
```

**Example Response:**

```json
{
    "object": {
        "templateId": "did:zid:66a960f2e709b8baa1259fcd2901b6f9afa4e03d60556e93c04a4322b6606f5c",
        "txHash": "a6f284d27aba2dfebfd6d2e916fdc039b7d420b7eb2940dd19119e6993588993"
    },
    "messages": []
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

**(Non-encrypted) Endpoint:** POST `/cred/v1/vc/apply`

**(Encrypted) Endpoint:** POST `/cred/v1/vc/enc/apply`

\
**Description:** Initiates a credential issuance request by a holder. The holder (or an application acting on behalf of the holder) calls this to apply for a credential of a given template. This step usually provides the data required for the credential and registers a request that the issuer can review/approve. The output is a VC request ID used to track the issuance process.

**(Non-encrypted) Request Body:** _(JSON)_

* `templateId` (string): The identifier of the credential template the holder is applying for. This should be a valid `templateId` obtained from the issuer (for example, the ID returned by the template submission).
* `metadata` (array of objects): The data values for the credential’s attributes, as required by the template. Each entry should provide one attribute’s value:
  * `key` (string): The attribute key, exactly as defined in the template’s `credentialFormat`.
  * `value` (string): The value the holder provides for this attribute. The format/type of this value should conform to what the template specifies (e.g., string, date).\
    &#xNAN;_&#x46;or example, if the template requires `fullName` and `licenseNumber`, the holder must provide those values._
* `signData:`&#x20;
* `holderPublicKey` (string, optional): The holder’s public key (for example, an Ed25519 public key corresponding to the holder’s DID). This may be provided if the system requires associating a specific key with the request (e.g., to later verify the holder’s presentation or for auditing). In many cases, this can be omitted if the holder’s DID document will be used to fetch keys.

**(Encrypted) Request Body:** _(JSON)_

* Header: `secretKey` :  Encrypted ECDH key.
* Encrypted application data consist of signed data, metadata, public key, and X25519 public key. Refer to VC Encryption Process- Apply VC Process.

**Response Body:** _(JSON)_

* `vcId` (string): A unique identifier for the VC issuance request. This ID represents the pending credential application. It will be used by the issuer to create the credential and can be used by the holder to query status or download the final VC.
* `status` (string): The status of the request. Typically, a new application will be in a status such as `"Pending"` (or similar) indicating it is awaiting issuer action. The status may be updated to `"Approved"`, `"Issued"`, `"Rejected"`, etc., depending on the workflow (for this API reference, the initial status is pending issuance).

**(Non-encrypted) Example Request:**

```json
{
    "data": [
        {
            "templateId": "did:zid:7380129d526a274ee507a5f806e4b8d88c2b7f34040897bf03571df7d1c6c9f0",
            "metadata": {
                "name": "Ali bin Abu",
                "class": "B2",
                "age": "21",
                "gender": "Male"
            }
        },
        {
            "templateId": "did:zid:551aba03d76f420b0b041df212a365f4d97c9abc1955107bd46480cafaf07c90",
            "metadata": {
                "course": "Science Computer",
                "cgpa": "3.9"      
            }
        }
    ],
    "signData":"6A670EDD9887B774B48A6403BBFF04A88ADFB7BDB6A70E5FF49C0DB0E5FE32783C19FB1EC3511B848FEB6D3D78620FF5803A7D1425D850B9C04D44BCA7D57003",
    "publicKey": "b0012c8c214c958e0f28e8e3aac60a38abd161e3135efa33bb1c4233e0c6a4518a1780176809"
}


```

**(Encrypted) Example Request:**

**Header:**

| Key         | Value                                                                                                                                                                                                                                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `secretKey` | UJugVbU79RHI5gF6g3pMVw\_YnoZxdrFqA5hGm7PkBcoKsyTj-paiOBKcgRApDP2KmCfbGcBtvrnEjO0GhsIFoDGxueE8oK8rWNIFWN4Swdl4Yimdf45C5\_x30b6Z4t3za8osfYdSn0kgOAdRo3khMRjr3XNEiGDVl1CjcV2mHmaFRu\_6TsBc9ZB7w95UBhKWTl6U7zMRNnZni6envSFRdz3IMthp9UMWmU2MyWqCUXdaZzEkKOOskQMdgpsXdOdnSJgsEPB75VAwhwiiPcp1n4AFjrUqSkGJcggxoP54brdSjbk4MNFbNVlXBfEqAPMBMG639vYj27\_mDTB1RFhSQA== |

**Request Body:** _(JSON)_

<pre class="language-json"><code class="lang-json"><strong>aIg5AyAg33xNGs7JnqxZB39XPa8bR8Hcf2_S438xVH0A9kxUZADCt7eQl7ipMs5nm4EODsXaT_4S2I7BBS0qw-NVdqgj86XZls3PuZky7Ae84yG1ASZvHAB63ed6VBpKB_D3d3PnfRt1vIXpR2Oe_8yo9OrFiHqnV4h-LNWnnpTdV-2EHTfwnJzun1Fxq-79gzolreBcfYNfxjujNKe6Jr0hLS9gGxBZQgQ-y-GGeDeEoXjmTuCDeMnTiC77-nD31JGFyV5mC9uaLorCUX3FyQgFZn8Jr9anVgrrlb_Cjhj7YlnBforlojQgeGRddTfpcd_WMNhWsjifE-s8wFDnzxyWStq6tNXd_mSYL4r0Z8EWP_zFlIN-lynsMgZewDLTnjefDwhaoEmjZC_1FiUiHI_cU-dDzyjMQQHYZp0JNBU7IHcVFUHCv2MKrNSVvVSkK1YhgGtvQouGG6fKigqub2CGZnwKXUbPKiUdxeGd1vvzi1G7FSg8Pa98WgKU9gROMMMpmWxkS7UN2WALrcHCV1hOyOl32hI92d6DJq6Q0JXNo1Gzyzx1uckNxj9n8N7OAINqge_gbNateht1I_wwzIGofjR_Mr6NdvDZ8l41sgo9f9poiiiY3jT6ZQ76RapWCIYVqef65KeLKoGn9M0-_dr3UhKC
</strong></code></pre>

**Example Response:**

```json
{
    "object": {
        "vcId": "did:zid:6421ca70444138f3495d5f50ddd174dfedec06d668fb697070b0a849771d7768",
        "status": "APPLIED"
    },
    "messages": []
}

```

**Error Cases:**

* `400 Bad Request` – Missing required fields or invalid data format (e.g., a mandatory attribute value is not provided or has the wrong type).
* `404 Not Found` – The specified `templateId` does not exist or is not available for issuance.
* `401 Unauthorized` – If authentication is required, this error can occur if the holder is not properly authenticated or not allowed to apply for this credential.
* `409 Conflict` – A duplicate request already exists (for example, the holder already applied for this credential and it’s still pending).
* `500 Internal Server Error` – An unexpected server error occurred while recording the application (database error, etc.).

### Create VC (by issuer)

**(Non-encrypted)Endpoint:** POST `/cred/v1/vc/create`

**(Encrypted) Endpoint:** POST /cred/v1/vc/enc/create

\
**Description:** Used by the issuer to create (prepare) a verifiable credential in response to a holder’s application. This call will assemble the credential data from the template and the holder’s provided information, and produce the necessary cryptographic elements for the credential. Specifically, it generates the credential’s BBS+ signature (which the issuer must produce) and a blockchain transaction blob for recording the credential status on-chain. The issuer should call this after approving or deciding to issue the credential for a given `vcId`. The credential is not yet final until the **Submit VC** step is completed.

**(Non-encrypted) Request Body:** _(JSON)_

* `vcId` (string): The identifier of the VC request (from the **Apply VC** step) that the issuer is fulfilling. This links the create operation to the holder’s application and the associated template.
* `issuanceData`(string)`:`The credential content or claims to be issued based on the selected template.
* `expiredDate`(string)`:`The credential content or claims to be issued based on the selected template.
* `templateId`(string)`:`The unique identifier of the credential template used for issuance.
* `metadata`(string)Additional information or attributes that provide context to the credential issuance.

**(Encrypted) Request Body:** _(JSON)_

* Header: `secretKey` :  Encrypted ECDH key.
* Encrypted issuance data {`vcId, issuanceData, expiredDate, templateId, metadata}` . Refer to VC Encryption Process- Apply VC Process.

**Response Body:** _(JSON)_

* `ed25519Blob` (string): A cryptographic blob generated using the Ed25519 private key that contains the signed transaction or credential data, enabling verification of authenticity and integrity on the blockchain.
* bbsBlsBase64 (string): The BBS+ signature in base64/hex format for the credential (same as provided in the request, or regenerated). This is included in the response for reference, confirming the signature that will be embedded in the credential. The issuer should retain this value as it forms part of the credential's proof.

**(Non-encrypted) Example Request:**

```json
{
    "vcId": "did:zid:6421ca70444138f3495d5f50ddd174dfedec06d668fb697070b0a849771d7768",
    "issuanceDate": "2025-01-01",
    "expirationDate": "2026-01-01",
    "data": [
        {
            "templateId": "did:zid:7380129d526a274ee507a5f806e4b8d88c2b7f34040897bf03571df7d1c6c9f0",
            "metadata": {
                "idNo": "abc12345"
            }
        },
        {
            "templateId": "did:zid:551aba03d76f420b0b041df212a365f4d97c9abc1955107bd46480cafaf07c90",
            "metadata": {
                "id": "gg1233222"    
            }
        }
    ]
}


```

**(Encrypted) Example Request:**

**Header:**

| Key         | Value                                                                                                                                                                                                                                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `secretKey` | UJugVbU79RHI5gF6g3pMVw\_YnoZxdrFqA5hGm7PkBcoKsyTj-paiOBKcgRApDP2KmCfbGcBtvrnEjO0GhsIFoDGxueE8oK8rWNIFWN4Swdl4Yimdf45C5\_x30b6Z4t3za8osfYdSn0kgOAdRo3khMRjr3XNEiGDVl1CjcV2mHmaFRu\_6TsBc9ZB7w95UBhKWTl6U7zMRNnZni6envSFRdz3IMthp9UMWmU2MyWqCUXdaZzEkKOOskQMdgpsXdOdnSJgsEPB75VAwhwiiPcp1n4AFjrUqSkGJcggxoP54brdSjbk4MNFbNVlXBfEqAPMBMG639vYj27\_mDTB1RFhSQA== |

**Request Body:** _(JSON)_

```json
CImvIwjmub7nGUMAiKCM-dCB9-c3CMdI-zwjBD-ZO1gzNczenc5ni7tJmJzOIoaMWeKi0KhcHfoFCcJED75-lXMd3--gA1Z5Kq-b-tIN5VZk1v9JheJVFz5e2w8-0D2vfeFyrV-dGjHpBFIlT3cGpBxoW-fhHKXVa6YBXP6uuAt49LsnYKG3O0ng9dr88KuQBFr2oezCIoz82Q8p3B7-a4nJg0_j_m4c4Yz6zM0LFQRYZqG6kcsewm0X9NgupywP_tPowPnfwDYLwJU1SW7ktNshv3UtkZR_QRpVpBg8GHLC4cTBr2SvHn8ZSs_gZCdv2NMNJVv2feyFtAtKqIaXHR7AthYZ0dzyQPevd4ZMmXA59I0=
```

**Example Response:**

```json
{
    "object": {
        "ed25519Blob": "65794a68624763694f694a465a45525451534a392e65794a4159323975644756346443493657794a6f64485277637a6f764c336433647935334d793576636d63764d6a41784f43396a636d566b5a57353061574673637939324d534973496d68306448427a4f693876647a4e705a433576636d63766332566a64584a7064486b76596d4a7a4c335978496977696148523063484d364c7939305a584e304c5735765a475575656d5630636d6c344c6d4e766253396e5a58524259324e766457353054575630595552686447455f5957526b636d567a637a31615646677a556b6449546d316b65454649537a56484d6e5a76527a64726232397a6332464465453569626c7069644868324a6d746c655431305a573177624746305a5639665a476c6b4f6e70705a446f334d7a67774d5449355a4455794e6d45794e7a526c5a5455774e3245315a6a67774e6d5530596a686b4f44686a4d6d49335a6a4d304d4451774f446b33596d59774d7a55334d57526d4e325178597a5a6a4f575977496977696148523063484d364c7939305a584e304c5735765a475575656d5630636d6c344c6d4e766253396e5a58524259324e766457353054575630595552686447455f5957526b636d567a637a31615646677a556b6449546d316b65454649537a56484d6e5a76527a64726232397a6332464465453569626c7069644868324a6d746c655431305a573177624746305a5639665a476c6b4f6e70705a446f314e544668596d45774d3251334e6d59304d6a42694d4749774e44466b5a6a49784d6d457a4e6a566d4e4751354e324d3559574a6a4d546b314e5445774e324a6b4e4459304f44426a59575a685a6a4133597a6b77496c3073496d4e795a57526c626e52705957785464574a715a574e30496a7037496d526c5a334a6c5a554e6c636e52705a6d6c6a5958526c496a7037496d4e6e634745694f69497a4c6a6b694c434a6a62335679633255694f694a5459326c6c626d4e6c49454e76625842316447567949697769615751694f694a6e5a7a45794d7a4d794d6a4969665377695a484a70646d6c755a30787059325675633255694f6e73695957646c496a6f694d6a45694c434a6a6247467a63794936496b4979496977695a3256755a475679496a6f69545746735a534973496d6c6b546d38694f694a68596d4d784d6a4d304e534973496d3568625755694f694a4262476b67596d6c754945466964534a394c434a705a434936496d52705a447036615751364d6d4d34597a49784e474d354e54686c4d4759794f4755345a544e6859574d324d47457a4f4746695a4445324d57557a4d544d315a575a684d7a4e69596a466a4e44497a4d325577597a5a684e4455784f4745784e794a394c434a6c65484270636d463061573975524746305a534936496a49774d6a59744d4445744d4446554d4441364d4441364d44426149697769615751694f694a6b61575136656d6c6b4f6a59304d6a466a595463774e4451304d544d345a6a4d304f54566b4e5759314d47526b5a4445334e47526d5a57526c597a41325a4459324f475a694e6a6b334d446377596a42684f4451354e7a63785a4463334e6a67694c434a7063334e315957356a5a555268644755694f6949794d4449314c5441784c544178564441774f6a41774f6a417757694973496d6c7a6333566c63694936496d52705a447036615751364d4751304f446b784e6d5534596a426d4d5441794f5464694d44497a596a59344d475a694e3246694d6a4d324e3251315a5749774d32457859545a69596a67334e6d51324d32517859546c6d4e5459334e32526a59694973496e5235634755694f6c7369566d567961575a7059574a735a554e795a57526c626e5270595777694c434a45636d6c326157356e54476c6a5a57357a5a534973496b526c5a334a6c5a554e6c636e52705a6d6c6a5958526c496c3139",
        "bbsBlsBase64": [
            "aWQ6IGRpZDp6aWQ6MmM4YzIxNGM5NThlMGYyOGU4ZTNhYWM2MGEzOGFiZDE2MWUzMTM1ZWZhMzNiYjFjNDIzM2UwYzZhNDUxOGExNw==",
            "ZHJpdmluZ0xpY2Vuc2UubmFtZTogQWxpIGJpbiBBYnU=",
            "ZHJpdmluZ0xpY2Vuc2UuY2xhc3M6IEIy",
            "ZHJpdmluZ0xpY2Vuc2UuYWdlOiAyMQ==",
            "ZHJpdmluZ0xpY2Vuc2UuZ2VuZGVyOiBNYWxl",
            "ZHJpdmluZ0xpY2Vuc2UuaWRObzogYWJjMTIzNDU=",
            "ZGVncmVlQ2VydGlmaWNhdGUuY291cnNlOiBTY2llbmNlIENvbXB1dGVy",
            "ZGVncmVlQ2VydGlmaWNhdGUuY2dwYTogMy45",
            "ZGVncmVlQ2VydGlmaWNhdGUuaWQ6IGdnMTIzMzIyMg=="
        ]
    },
    "messages": []
}

```

**Error Cases:**

* `400 Bad Request` – The request is missing required data or has invalid format (for instance, no BBS+ signature provided, or the signature is the wrong format).
* `404 Not Found` – The `vcId` does not correspond to a valid pending application (e.g., if it was already processed or never existed).
* `403 Forbidden` – The issuer’s DID does not match the template’s issuer or the issuer is not authorized to issue this credential.
* `409 Conflict` – The credential might have already been created or is in a state that cannot be created again (to avoid duplicate issuance).
* `500 Internal Server Error` – A server error occurred while assembling the credential (e.g., issues in retrieving template or holder data, or internal cryptographic failure).

### Submit VC (by issuer)

**(Non-encrypted)Endpoint:** POST `/cred/v1/vc/submit`

**(Encrypted) Endpoint:** POST `/cred/v1/vc/enc/submit`

\
**Description:** Finalizes the issuance of the verifiable credential. The issuer calls this after signing the transaction blob obtained from **Create VC**. This will submit the issuance transaction to the Zetrix blockchain (recording the credential’s existence and status) and finalize the credential object. On success, the credential is officially issued and can be delivered to the holder. The response includes the complete VC in a verifiable format.

**(Non-encrypted) Request Body:** _(JSON)_

* `vcId` (string): Unique identifier of the issued Verifiable Credential.
* `ed25519PubKey`(string): The issuer’s ED25519 public key used for verifying the credential signature.
* `ed25519SignData` (string): Signature of the credential data generated with the issuer’s ED25519 private key.
* `bbsB1sPubKey` (string): The issuer’s BBS+ public key used for verifying selective disclosure proofs.
* `bbsB1SignData` (string): Signature of the credential data generated with the issuer’s BBS+ private key.
* `keyExpiry` (string): Expiry timestamp of the signing key pair to ensure key validity and rotation.

**(Encrypted) Request Body:** _(JSON)_

* Header: `secretKey` :  Encrypted ECDH key.
* Encrypted VC data {`vcId, ed25519PubKey, ed25519SignData, bbsB1sPubKey , bbsB1SignData, keyExpiry }` . Refer to VC Encryption Process- Issue and Download VC Process.

**Response Body:** _(JSON)_

* `vcObject` (object): The issued Verifiable Credential, in its full JSON representation. This will typically include standard VC fields such as `@context`, `id` (which may incorporate the `vcId`), `type`, `issuer`, `credentialSubject` (containing the holder’s DID and the attributes with values), and `proof`. The `proof` section will include the BBS+ signature (often encoded as a proof value) and related metadata (like the public key or verification method, and type of proof). It may also include a `credentialStatus` entry indicating where the credential’s status (e.g., revocation status) is recorded (for example, referencing a blockchain registry or contract).
* `downloadExpiryDate` (string): A timestamp indicating how long the issued credential will be available for download via the **Download VC** endpoint. The credential might be stored temporarily on the server; this field informs the issuer/holder of the cutoff time to fetch it. After this expiry, the credential might be purged for security, requiring the issuer to re-share if needed.

**(Non-encrypted) Example Request:**

```json
{
    "vcId": "did:zid:6421ca70444138f3495d5f50ddd174dfedec06d668fb697070b0a849771d7768",
    "ed25519PubKey": "b0010d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb43fad909",
    "ed25519SignData": "D75ED9C72E8D95BEDED02058143267C41DD9943BE1235C3201D59BB73BE8BAB4A28424F057C220C6E650D36DB45EDB2DC58AA34E30672AB454A3A4E075B88E03",
    "bbsBlsPubKey": "zmGHfsGYtRbh6MfDQegV976maR1FwAJxFnboew8wydSVd2YwprnhXQvBDUYnQ13PCeifouGzeecE3dko8F6KTEGkZtuDFLBYfAJHSCzpk9ukfXWqX9ykpz326RSWPxjeagQp",
    "bbsBlsSignData": "ukFiZpQ2quVC4mTgEuzyF8lVqOExC5HGBWnCHcxCuR7jPuZOhnWT9Lrg4w9YVqcM1QlHj0EpPUsTGYCnZA1-_pPAZYyLHjrPGZEv5rJADJRUkj6ocu7HgDxf5tnwvzFVyFABEjt0Ac4E9mBfHIrcLPA",
    "keyExpiry": 2592000
}


```

**(Encrypted) Example Request:**

**Header:**

| Key         | Value                                                                                                                                                                                                                                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `secretKey` | UJugVbU79RHI5gF6g3pMVw\_YnoZxdrFqA5hGm7PkBcoKsyTj-paiOBKcgRApDP2KmCfbGcBtvrnEjO0GhsIFoDGxueE8oK8rWNIFWN4Swdl4Yimdf45C5\_x30b6Z4t3za8osfYdSn0kgOAdRo3khMRjr3XNEiGDVl1CjcV2mHmaFRu\_6TsBc9ZB7w95UBhKWTl6U7zMRNnZni6envSFRdz3IMthp9UMWmU2MyWqCUXdaZzEkKOOskQMdgpsXdOdnSJgsEPB75VAwhwiiPcp1n4AFjrUqSkGJcggxoP54brdSjbk4MNFbNVlXBfEqAPMBMG639vYj27\_mDTB1RFhSQA== |

**Request Body:** _(JSON)_

```json
cKyWw4_ixz0lPtchJr1VeWEpoWL2Jvf_hRbmJTMYa4cKMLhKv7-bNe-PFcNXkgwGacsBzH47JFANYdltLdK32f-BEnG4D_gu0o9aimUobXYKHVGCNseHEnKV5YCBwHBhiCXPSg3wDnzTQctYIui8Wu75ec8ODh3uiyYPFi8dMLboRVPMddRGun7vmWIa246PgjX9Yn7OyjXotGXx5bDfZ05c6NUh7PS7-K5gPUB2BSQ_tfc6ps81mYkW58inpgqlBz3WPzqsM86CCTTGAyZLsyUIjtyCy3Es5raaLir25N6_OZ7tTqGjqkKnYXpfhnwGhn2d9VtMAd5PFmDNFO4oJQUQtkK0YNLXOs1cga9rkHD7sJd7buXuy7g4bc4AhMj2ciI5tKSWCByWMfoBbzsTRw3T75kGtZO04yZFTZbvexHos_8dv_KCosKck6UvfpcBexJZjbOTFgI-auy1CB1mKcghKwagwIkuxjAli6P-jX89n-uGf8gwWl6KGtFRhIphX9j_eGSWjcYZGjCfJImfy6uCRVovyZJ2OW_DXLufGatKE6F7OguWWJtS3r821Kdnv3n5uegtnI2W7OkEApYUmC3Jh13Xa98YZzZcMrCo2bHwjNBG4DwsIAo4RGOuRyaezC9Ikki-PLhVTFn59qnYECipb9CSlG1oaNDJfy18u017d6kjAw9mFN3Qr8J3kiG4xxUIypdw4aCZ3chPpxIieH15VONRFsKSQ4zgdmWODgYRxRJmA8DjcjjodDRMyyqofdKm_W_j_76HajLDdSVGc5g5S8645N73XYduPT48F-hRX_HlApZE8woNxtjBtNZslsahf-aLoM6kYqsI16X2bG04YPQOdLxrJhr-R5DvAmjUNc7EgIVg4sSGGv5uCSCosMEzZvIg9maDPNVw5MKzZsuf0AJuTQTz
```

**Example Response (partial):**

```json
{
    "object": {
        "vc": {
            "id": "did:zid:6421ca70444138f3495d5f50ddd174dfedec06d668fb697070b0a849771d7768",
            "type": [
                "VerifiableCredential",
                "DrivingLicense",
                "DegreeCertificate"
            ],
            "issuer": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb",
            "issuanceDate": "2025-01-01T00:00:00Z",
            "expirationDate": "2026-01-01T00:00:00Z",
            "credentialSubject": {
                "id": "did:zid:2c8c214c958e0f28e8e3aac60a38abd161e3135efa33bb1c4233e0c6a4518a17",
                "drivingLicense": {
                    "name": "Ali bin Abu",
                    "class": "B2",
                    "age": "21",
                    "gender": "Male",
                    "idNo": "abc12345"
                },
                "degreeCertificate": {
                    "course": "Science Computer",
                    "cgpa": "3.9",
                    "id": "gg1233222"
                }
            },
            "proof": [
                {
                    "type": "BbsBlsSignature2020",
                    "created": "2025-03-07T07:06:44.747608Z",
                    "proofPurpose": "assertionMethod",
                    "proofValue": "ukFiZpQ2quVC4mTgEuzyF8lVqOExC5HGBWnCHcxCuR7jPuZOhnWT9Lrg4w9YVqcM1QlHj0EpPUsTGYCnZA1-_pPAZYyLHjrPGZEv5rJADJRUkj6ocu7HgDxf5tnwvzFVyFABEjt0Ac4E9mBfHIrcLPA",
                    "verificationMethod": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb#delegate-1"
                },
                {
                    "type": "Ed25519Signature2020",
                    "created": "2025-03-07T07:06:44.750767Z",
                    "proofPurpose": "assertionMethod",
                    "verificationMethod": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb#controllerKey",
                    "jws": "eyJhbGciOiJFZERTQSJ9.eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdzNpZC5vcmcvc2VjdXJpdHkvYmJzL3YxIiwiaHR0cHM6Ly90ZXN0LW5vZGUuemV0cml4LmNvbS9nZXRBY2NvdW50TWV0YURhdGE_YWRkcmVzcz1aVFgzUkdITm1keEFISzVHMnZvRzdrb29zc2FDeE5iblpidHh2JmtleT10ZW1wbGF0ZV9fZGlkOnppZDo3MzgwMTI5ZDUyNmEyNzRlZTUwN2E1ZjgwNmU0YjhkODhjMmI3ZjM0MDQwODk3YmYwMzU3MWRmN2QxYzZjOWYwIiwiaHR0cHM6Ly90ZXN0LW5vZGUuemV0cml4LmNvbS9nZXRBY2NvdW50TWV0YURhdGE_YWRkcmVzcz1aVFgzUkdITm1keEFISzVHMnZvRzdrb29zc2FDeE5iblpidHh2JmtleT10ZW1wbGF0ZV9fZGlkOnppZDo1NTFhYmEwM2Q3NmY0MjBiMGIwNDFkZjIxMmEzNjVmNGQ5N2M5YWJjMTk1NTEwN2JkNDY0ODBjYWZhZjA3YzkwIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImRlZ3JlZUNlcnRpZmljYXRlIjp7ImNncGEiOiIzLjkiLCJjb3Vyc2UiOiJTY2llbmNlIENvbXB1dGVyIiwiaWQiOiJnZzEyMzMyMjIifSwiZHJpdmluZ0xpY2Vuc2UiOnsiYWdlIjoiMjEiLCJjbGFzcyI6IkIyIiwiZ2VuZGVyIjoiTWFsZSIsImlkTm8iOiJhYmMxMjM0NSIsIm5hbWUiOiJBbGkgYmluIEFidSJ9LCJpZCI6ImRpZDp6aWQ6MmM4YzIxNGM5NThlMGYyOGU4ZTNhYWM2MGEzOGFiZDE2MWUzMTM1ZWZhMzNiYjFjNDIzM2UwYzZhNDUxOGExNyJ9LCJleHBpcmF0aW9uRGF0ZSI6IjIwMjYtMDEtMDFUMDA6MDA6MDBaIiwiaWQiOiJkaWQ6emlkOjY0MjFjYTcwNDQ0MTM4ZjM0OTVkNWY1MGRkZDE3NGRmZWRlYzA2ZDY2OGZiNjk3MDcwYjBhODQ5NzcxZDc3NjgiLCJpc3N1YW5jZURhdGUiOiIyMDI1LTAxLTAxVDAwOjAwOjAwWiIsImlzc3VlciI6ImRpZDp6aWQ6MGQ0ODkxNmU4YjBmMTAyOTdiMDIzYjY4MGZiN2FiMjM2N2Q1ZWIwM2ExYTZiYjg3NmQ2M2QxYTlmNTY3N2RjYiIsInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJEcml2aW5nTGljZW5zZSIsIkRlZ3JlZUNlcnRpZmljYXRlIl19.RDc1RUQ5QzcyRThEOTVCRURFRDAyMDU4MTQzMjY3QzQxREQ5OTQzQkUxMjM1QzMyMDFENTlCQjczQkU4QkFCNEEyODQyNEYwNTdDMjIwQzZFNjUwRDM2REI0NUVEQjJEQzU4QUEzNEUzMDY3MkFCNDU0QTNBNEUwNzVCODhFMDM"
                }
            ],
            "@context": [
                "https://www.w3.org/2018/credentials/v1",
                "https://w3id.org/security/bbs/v1",
                "https://test-node.zetrix.com/getAccountMetaData?address=ZTX3RGHNmdxAHK5G2voG7koossaCxNbnZbtxv&key=template__did:zid:7380129d526a274ee507a5f806e4b8d88c2b7f34040897bf03571df7d1c6c9f0",
                "https://test-node.zetrix.com/getAccountMetaData?address=ZTX3RGHNmdxAHK5G2voG7koossaCxNbnZbtxv&key=template__did:zid:551aba03d76f420b0b041df212a365f4d97c9abc1955107bd46480cafaf07c90"
            ]
        },
        "downloadExpiryDate": 1743923204755
    },
    "messages": []
}


```

**Error Cases:**

* `400 Bad Request` – The signature is missing or invalid, or the blobId is not provided.
* `401 Unauthorized` – The signature does not match the issuer’s known public key (verification failed), or the issuer is not authorized to submit this credential.
* `404 Not Found` – The blobId is not recognized (e.g., if the create step was not done or the blob has expired), or the corresponding VC request was not found.
* `409 Conflict` – The credential has already been submitted/issued (duplicate submission) or was revoked by the time of submission (unlikely scenario).
* `500 Internal Server Error` – A server or blockchain error occurred during submission (e.g., failure writing to blockchain or assembling the final VC object).

### Download VC (by issuer or holder)

**(Non-encrypted)Endpoint:** POST `/cred/v1/vc/download`

**(Encrypted) Endpoint:** POST `/cred/v1/vc/enc/download`\
**Description:** Retrieves a previously issued verifiable credential. This endpoint can be used by the holder (to download the credential that was issued to them) or by the issuer (to retrieve the credential, for example to send it through another channel). Typically, this would be called after the **Submit VC** step, using the provided `vcId`. The credential must still be available (not expired from temporary storage) for this call to succeed. The response is the VC in JSON format.

**(Non-encrypted) Request:**

* `vcId` (string): Unique identifier of the issued Verifiable Credential.
* `signVcId:`The signed identifier of the Verifiable Credential, ensuring authenticity and integrity.
* `isIssuer:`Indicates whether the entity performing the action is the credential issuer (`true`) or not (`false`).

**Response Body:** _(JSON)_\
The full Verifiable Credential object in JSON, identical to the `vcObject` returned in the Submit VC step. This includes all fields of the credential (`context, id, type, issuer, credentialSubject, credentialStatus if any, and proof with BBS+ signature`). This allows the holder to store or view their credential, and the issuer to archive it if needed.

**(Encrypted) Request Body:** _(JSON)_

* Header: `secretKey` :  Encrypted ECDH key.
* Encrypted VC data {`vcId, signVcId, isIssuer}` . Refer to VC Encryption Process- Issue and Download VC Process.

**(Non-encrypted) Example Request:**

```json
{
    "vcId": "did:zid:9d449e8fa06cd970b6a17c6dbf9b56211184f90bd9730d2f4728faddea7f03d8",
    "signVcId": "F40D10AD190481B4AD82D4E39222C7B432000DCC334794204E0CD45F76DA54137E20AD18C7D80D925D2C26B3B79DDCEB6D86A2152258974C463E1C689428120C",
    "isIssuer": false
}
```

**(Encrypted) Example Request:**

**Header:**

| Key         | Value                                                                                                                                                                                                                                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `secretKey` | UJugVbU79RHI5gF6g3pMVw\_YnoZxdrFqA5hGm7PkBcoKsyTj-paiOBKcgRApDP2KmCfbGcBtvrnEjO0GhsIFoDGxueE8oK8rWNIFWN4Swdl4Yimdf45C5\_x30b6Z4t3za8osfYdSn0kgOAdRo3khMRjr3XNEiGDVl1CjcV2mHmaFRu\_6TsBc9ZB7w95UBhKWTl6U7zMRNnZni6envSFRdz3IMthp9UMWmU2MyWqCUXdaZzEkKOOskQMdgpsXdOdnSJgsEPB75VAwhwiiPcp1n4AFjrUqSkGJcggxoP54brdSjbk4MNFbNVlXBfEqAPMBMG639vYj27\_mDTB1RFhSQA== |

**Request Body:** _(JSON)_

```postman_json
sHaMF3ysLD7E9KZqW3AiEe05p9k_zAn8QiEM822w9ghiazIf22brMEkNY8FyLT2JrCJK-Bn7S8DAxtokezyyV1D2c40_xt48DFoqIJZrcWzRqYJjH6XShNA4S11quqkZQB2mnVmASsKKfe04oyqY_0yeWU7hmkD-GftXFuYeKtz76AZqLsY3Cgsh3xL7aXNHwYF7qMSt1rJ4w2FfS6_VcDswFLpj5E5LEyZqP5NXAWo5EilOsiLeDlg-lHZTNT3SX1damQwTpjxh83J2SShGDE2JdzG3UiAZswYgdjsMGkyxzuKOcRfrLZfhbfasZlM4M81qgLb7M9T9KfYzWgg7bVLSHzkYmA-IzJvdoyvq
```

**Example Response (full VC):**

```json
{
    "object": {
        "id": "did:zid:6421ca70444138f3495d5f50ddd174dfedec06d668fb697070b0a849771d7768",
        "type": [
            "VerifiableCredential",
            "DrivingLicense",
            "DegreeCertificate"
        ],
        "issuer": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb",
        "issuanceDate": "2025-01-01T00:00:00Z",
        "expirationDate": "2026-01-01T00:00:00Z",
        "credentialSubject": {
            "id": "did:zid:2c8c214c958e0f28e8e3aac60a38abd161e3135efa33bb1c4233e0c6a4518a17",
            "drivingLicense": {
                "name": "Ali bin Abu",
                "class": "B2",
                "age": "21",
                "gender": "Male",
                "idNo": "abc12345"
            },
            "degreeCertificate": {
                "course": "Science Computer",
                "cgpa": "3.9",
                "id": "gg1233222"
            }
        },
        "proof": [
            {
                "type": "BbsBlsSignature2020",
                "created": "2025-03-07T07:06:44.747608Z",
                "proofPurpose": "assertionMethod",
                "proofValue": "ukFiZpQ2quVC4mTgEuzyF8lVqOExC5HGBWnCHcxCuR7jPuZOhnWT9Lrg4w9YVqcM1QlHj0EpPUsTGYCnZA1-_pPAZYyLHjrPGZEv5rJADJRUkj6ocu7HgDxf5tnwvzFVyFABEjt0Ac4E9mBfHIrcLPA",
                "verificationMethod": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb#delegate-1"
            },
            {
                "type": "Ed25519Signature2020",
                "created": "2025-03-07T07:06:44.750767Z",
                "proofPurpose": "assertionMethod",
                "verificationMethod": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb#controllerKey",
                "jws": "eyJhbGciOiJFZERTQSJ9.eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdzNpZC5vcmcvc2VjdXJpdHkvYmJzL3YxIiwiaHR0cHM6Ly90ZXN0LW5vZGUuemV0cml4LmNvbS9nZXRBY2NvdW50TWV0YURhdGE_YWRkcmVzcz1aVFgzUkdITm1keEFISzVHMnZvRzdrb29zc2FDeE5iblpidHh2JmtleT10ZW1wbGF0ZV9fZGlkOnppZDo3MzgwMTI5ZDUyNmEyNzRlZTUwN2E1ZjgwNmU0YjhkODhjMmI3ZjM0MDQwODk3YmYwMzU3MWRmN2QxYzZjOWYwIiwiaHR0cHM6Ly90ZXN0LW5vZGUuemV0cml4LmNvbS9nZXRBY2NvdW50TWV0YURhdGE_YWRkcmVzcz1aVFgzUkdITm1keEFISzVHMnZvRzdrb29zc2FDeE5iblpidHh2JmtleT10ZW1wbGF0ZV9fZGlkOnppZDo1NTFhYmEwM2Q3NmY0MjBiMGIwNDFkZjIxMmEzNjVmNGQ5N2M5YWJjMTk1NTEwN2JkNDY0ODBjYWZhZjA3YzkwIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImRlZ3JlZUNlcnRpZmljYXRlIjp7ImNncGEiOiIzLjkiLCJjb3Vyc2UiOiJTY2llbmNlIENvbXB1dGVyIiwiaWQiOiJnZzEyMzMyMjIifSwiZHJpdmluZ0xpY2Vuc2UiOnsiYWdlIjoiMjEiLCJjbGFzcyI6IkIyIiwiZ2VuZGVyIjoiTWFsZSIsImlkTm8iOiJhYmMxMjM0NSIsIm5hbWUiOiJBbGkgYmluIEFidSJ9LCJpZCI6ImRpZDp6aWQ6MmM4YzIxNGM5NThlMGYyOGU4ZTNhYWM2MGEzOGFiZDE2MWUzMTM1ZWZhMzNiYjFjNDIzM2UwYzZhNDUxOGExNyJ9LCJleHBpcmF0aW9uRGF0ZSI6IjIwMjYtMDEtMDFUMDA6MDA6MDBaIiwiaWQiOiJkaWQ6emlkOjY0MjFjYTcwNDQ0MTM4ZjM0OTVkNWY1MGRkZDE3NGRmZWRlYzA2ZDY2OGZiNjk3MDcwYjBhODQ5NzcxZDc3NjgiLCJpc3N1YW5jZURhdGUiOiIyMDI1LTAxLTAxVDAwOjAwOjAwWiIsImlzc3VlciI6ImRpZDp6aWQ6MGQ0ODkxNmU4YjBmMTAyOTdiMDIzYjY4MGZiN2FiMjM2N2Q1ZWIwM2ExYTZiYjg3NmQ2M2QxYTlmNTY3N2RjYiIsInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJEcml2aW5nTGljZW5zZSIsIkRlZ3JlZUNlcnRpZmljYXRlIl19.RDc1RUQ5QzcyRThEOTVCRURFRDAyMDU4MTQzMjY3QzQxREQ5OTQzQkUxMjM1QzMyMDFENTlCQjczQkU4QkFCNEEyODQyNEYwNTdDMjIwQzZFNjUwRDM2REI0NUVEQjJEQzU4QUEzNEUzMDY3MkFCNDU0QTNBNEUwNzVCODhFMDM"
            }
        ],
        "@context": [
            "https://www.w3.org/2018/credentials/v1",
            "https://w3id.org/security/bbs/v1",
            "https://test-node.zetrix.com/getAccountMetaData?address=ZTX3RGHNmdxAHK5G2voG7koossaCxNbnZbtxv&key=template__did:zid:7380129d526a274ee507a5f806e4b8d88c2b7f34040897bf03571df7d1c6c9f0",
            "https://test-node.zetrix.com/getAccountMetaData?address=ZTX3RGHNmdxAHK5G2voG7koossaCxNbnZbtxv&key=template__did:zid:551aba03d76f420b0b041df212a365f4d97c9abc1955107bd46480cafaf07c90"
        ]
    },
    "messages": []
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

**Endpoint:** POST `/cred/v1/vc/revoke/create`\
**Description:** Initiates the revocation of a verifiable credential. The issuer calls this to build a revocation transaction for a given credential, which returns a blob to be signed. Revoking a credential typically updates its status on-chain (for example, updating a revocation registry or marking the credential as revoked in a smart contract). This step does not yet revoke the credential; it only prepares the transaction.

**Request Body:** _(JSON)_

* `vcId` (string): The identifier of the credential to be revoked. This is the same ID used during issuance (and present in the credential’s `id` field). The system will use this to locate the credential record or status entry on-chain.
* `remark` (string, optional): A human-readable reason for revocation. This reason might be recorded in logs or off-chain storage for audit purposes (e.g., “Credential revoked due to expiration” or “Holder requested revocation”). It is not typically part of the on-chain transaction (which usually just flips a status), but may be stored in an off-chain database.
* `issuerAddress`(string): The blockchain address of the credential issuer, used to identify and verify the source of credential issuance.

**Response Body:** _(JSON)_

* `blobId` (string): The identifier for the revocation transaction blob.
* `blob` (string): The revocation transaction blob (hex/base64 string) that needs to be signed by the issuer’s private key. This blob encodes the action of revoking the credential identified by `vcId`.

**Example Request:**

```json
{
    "vcId": "did:zid:f88afee30394b52c054e676baba135ace09015770747196a212e857bff1629ff",
    "remark": "test revoke",
    "issuerAddress": "ZTX3LU2NyoRPmgZqMHwiwupr6us5Lv9by3pUo"
}
```

**Example Response:**

```json
{
    "object": {
        "blobId": "94FD5A0F5F312A0E420EC1FB412FBE5A",
        "blob": "0A255A5458334C55324E796F52506D675A714D48776977757072367573354C763962793370556F104622DD01080712255A5458334C55324E796F52506D675A714D48776977757072367573354C763962793370556F62B1010A255A5458334D6D6F7671313535677A7244364D6564693662433570474B4169355933514D77781A87017B226D6574686F64223A227265766F6B65222C22706172616D73223A7B2272656D61726B223A2274657374207265766F6B65222C2276634964223A226469643A7A69643A66383861666565333033393462353263303534653637366261626131333561636530393031353737303734373139366132313265383537626666313632396666227D7D30B68901380A"
    },
    "messages": []
}
```

**Error Cases:**

* `400 Bad Request` – Missing `vcId`, or the request is malformed.
* `404 Not Found` – The specified `vcId` does not correspond to an issued credential (perhaps it was never issued or already revoked).
* `403 Forbidden` – The caller is not the issuer of that credential or not authorized to revoke it. Only the original issuer (or an authorized delegate) should revoke a credential.
* `409 Conflict` – The credential is already revoked or a revocation is already in progress for this credential.
* `500 Internal Server Error` – Server error while generating the revocation blob (e.g., inability to fetch credential status from chain).

### Submit Revoke VC

**Endpoint:** POST `/cred/v1/vc/revoke/submit`\
**Description:** Finalizes the credential revocation by submitting the signed revocation transaction to the blockchain. After this call succeeds, the credential identified by the given `vcId` is considered revoked. Verifiers checking the credential’s status will see it as revoked (invalid). This endpoint should be called after the issuer signs the blob from the create step.

**Request Body:** _(JSON)_

* `blobId` (string): The identifier of the revocation transaction blob (obtained from the create step).
* `signBlob` (string): A signed cryptographic blob used to authorize and finalize blockchain transactions for credential or template operations.
* `publicKey` (string): The Ed25519 public key of the issuer corresponding to the signature, to verify the signature. This should match the issuer’s DID and the key that was used in issuance.

**Response Body:** _(JSON)_

* `vcId` (string): The identifier of the credential that was revoked (echoing the input for clarity).
* `status` (string): The new status of the credential. Typically, this will be `"Revoked"` to indicate the credential is now revoked. In some implementations, this might also include a transaction hash or confirmation of revocation. For example, the response could include a blockchain transaction ID for the revocation or a timestamp of revocation, but at minimum the status is updated.

**Example Request:**

```json
{
    "blobId": "DE629ACA66639EE4E37A5A2B93A37BF7",
    "signerList": [
        {
            "signBlob": "47569120F80E52225B64F39E32A0C4872A1A512DBE171393FC3737370A09F9D8C7AFB065BDF29239F3F58845AD1607149A6288F6E17F3B800548C0B2F6E3E604",
            "publicKey": "b0010d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb43fad909"
        }
    ]
}
```

**Example Response:**

```json
{
    "object": {
        "txHash": "32c7f76868e62c15a59d9cafd217005ce0674ea177b56bb2d3c5f38599058041"
    },
    "messages": []
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

**Endpoint:** POST `/cred/bbs/create`\
**Description:** Derives a BBS+ issuer account (key pair) from a given seed. This is usually used to get the BBS+ public key corresponding to a seed phrase or seed value that the issuer controls. The BBS+ public key is needed to include in DIDs or to share with verifiers so they can verify credentials. The seed is used to deterministically generate the BBS+ private/public key pair. (This does not create an account on the blockchain; it’s purely cryptographic key derivation.)

**Request :** _(JSON)_

* `authorization:` must be sent with all client requests. The authorization helps the server to validate the request source. &#x20;
* `seed` (string): A seed used to generate the BBS+ keys. This can be a mnemonic phrase or a hex string/base64 string representing the seed bytes. The exact format depends on implementation: for example, it could be a 32-byte hex string derived from a 12-word mnemonic. The seed provided should be kept secure, as it determines the private key. _(If a mnemonic phrase is used, it should be a single string with words separated by spaces.)_

**Response Body:** _(JSON)_

* `publicKeyMultibase` (string): The issuer’s public key encoded in Multibase format, allowing interoperable representation of keys across different systems and encoding schemes.
* `privateKeyMultibase`(string): The issuer’s private key encoded in Multibase format, used for cryptographic signing operations and must be kept strictly confidential.

**Example Request:**

```json
{
  "seed": "trust toward breeze vapor enforce banana brief unfold carpet adjust obey parade"
}
```

**Example Response:**

```json
{
    "object": {
        "publicKeyMultibase": "zmGHfsGYtRbh6MfDQegV976maR1FwAJxFnboew8wydSVd2YwprnhXQvBDUYnQ13PCeifouGzeecE3dko8F6KTEGkZtuDFLBYfAJHSCzpk9ukfXWqX9ykpz326RSWPxjeagQp",
        "privateKeyMultibase": "zMUD9RhqyST16EWBUvSdeQkvE2dMz5oQCTxMCbxT5wHo"
    },
    "messages": []
}
```

_(The publicKey string is truncated for brevity. It would be a long base64 string.)_

**Error Cases:**

* `400 Bad Request` – The seed is missing or not in a valid format (e.g., wrong number of words in mnemonic, or invalid hex string).
* `401 Unauthorized` – If the API requires authentication (to prevent unauthorized key derivation) and the call is not authenticated.
* `500 Internal Server Error` – The server encountered an error during key generation (for example, if the seed phrase couldn’t be parsed or an internal library error occurred).

### Sign Data

**Endpoint:** POST `/cred/bbs/vc/sign`\
**Description:** Signs an array of data messages using the issuer’s BBS+ secret key. This is used to produce the BBS+ signature that will go into a Verifiable Credential. The messages to sign should correspond to the credential’s attributes (and possibly other fixed fields like a context or nonce, depending on implementation). The output is a BBS+ signature that can later be verified with the issuer’s BBS+ public key. Typically, the issuer will use the same seed as in **Create Account** to derive the secret key and sign the credential attributes.

**Request:** _(JSON)_

* `authorization:` must be sent with all client requests. The authorization helps the server to validate the request source.
* `publicKeyMultibase` (string): The issuer’s public key encoded in Multibase format, allowing interoperable representation of keys across different systems and encoding schemes.
* `data` (strings): The list of messages (attribute values) to sign.&#x20;

**Response Body:** _(JSON)_

* `signature` (string): The BBS+ signature over the provided messages, encoded as a base64 string (or hex string depending on the system conventions). This signature will be used as the `bbsBlsSignature` in the **Create VC** call. It incorporates all messages in a single compact signature that allows selective disclosure proofs later. The length of this string is typically fixed (for example, 112 bytes in base64 for BBS+ sig).

**Example Request:**

```json
{
    "publicKeyMultibase": "zmGHfsGYtRbh6MfDQegV976maR1FwAJxFnboew8wydSVd2YwprnhXQvBDUYnQ13PCeifouGzeecE3dko8F6KTEGkZtuDFLBYfAJHSCzpk9ukfXWqX9ykpz326RSWPxjeagQp",
    "privateKeyMultibase": "zMUD9RhqyST16EWBUvSdeQkvE2dMz5oQCTxMCbxT5wHo",
    "data": [
            "aWQ6IGRpZDp6aWQ6YmE0ZjFmY2Y2ODgzMWE1YzY4OWRmYWEyMTk1ZGExYTNhN2MzNzkzMDIyOGY4ODY2MTFmOTM2ZmVkMGRmNjZiOQ==",
            "cGFzc3BvcnRNYWxheXNpYS5uYW1lOiBBbGkgYmluIEFidQ==",
            "cGFzc3BvcnRNYWxheXNpYS5jbGFzczogQjI=",
            "cGFzc3BvcnRNYWxheXNpYS5hZ2U6IDIx",
            "cGFzc3BvcnRNYWxheXNpYS5nZW5kZXI6IE1hbGU="
        ]
}
```

**Example Response:**

```json
{
    "object": {
        "signData": "uoULOVqFq4SQ-6sBZe_paCOGhOhcZcT64sNp9RlyhJDYQGPijNPPXJgTCm0bKqWmGbmHxe2iXKF2oPhWlqgBa-49J8-QGETTPiYmA5udXW882IuoLALYxrlZELsS5QhdX0FjRBEcYxdkuA6pmMR45Lg"
    },
    "messages": []
}
```

**Error Cases:**

* `400 Bad Request` – Missing seed or messages, or the messages array is empty. Also if a message is in an invalid format (e.g., not a string).
* `401 Unauthorized` – If the API requires auth and the call is not authorized to perform signing (to protect misuse of signing).
* `500 Internal Server Error` – If signing fails due to internal errors (for example, if the seed is incorrect or the cryptographic operation encountered an issue).

## Verifiable Presentation APIs

Verifiable Presentations (VPs) allow a credential holder to prove certain information from their credentials to a verifier. In Zetrix’s VC system (using BBS+), a holder can generate a proof that selectively discloses some attributes of their credential (and optionally proves statements about others, like ranges) without revealing the entire credential. The VP APIs help create and verify these presentations. Typically, the holder will use **Create VP** and **Submit VP** to produce a presentation, and a verifier will use **Verify VP** to check its validity.

### Create VP Blob

**(Non-encrypted) Endpoint:** POST `/cred/v1/vp/create`

**(Encrypted) Endpoint:** POST `/cred/v1/vp/enc/create`

\
**Description:** Creates a verifiable presentation object (unsigned) from a given credential and disclosure requirements. The holder calls this to prepare a presentation by specifying which credential to present and which attributes to reveal or prove. The endpoint returns a presentation blob that the holder must then sign (to prove possession) or finalize. This step performs the heavy cryptographic computation of generating a selective disclosure proof using the BBS+ signature from the credential.

**(Non-encrypted) Request Body:** _(JSON)_

* `vc`(object): The verifiable credential to be presented, provided in JSON format. This should be the full credential that was issued (as obtained from the download or issuance step), including its proof (BBS+ signature). The service will extract the credential’s data and proof from this object. _(Alternatively, some implementations might accept a `vcId` and fetch the credential if it’s stored, but in this context, providing the credential object ensures the holder has it and controls it.)_
* `vc.id` (string)**:** Unique identifier (DID) of the Verifiable Credential.
* `vc.type`**`(`**&#x61;rray of string)**:** Array of credential types; defines the base type (`VerifiableCredential`) and the specific credential type (`PASSPORT MALAYSIA`).
* `vc.issuer`(string)**:** DID of the credential issuer (organization or authority that issues the VC).
* `vc.validFrom`(string)**:** Start date and time (ISO 8601) when the credential becomes valid.
* `vc.validUntil`(string)**:** Expiration date and time (ISO 8601) after which the credential is no longer valid.
* `vc.credentialSubject.id`(string)**:** DID of the credential subject (the individual or entity to whom the VC is issued).
* `vc.proof.type`(string)**:**&#x53;ignature type used for cryptographic proof (e.g., `BbsBlsSignature2020`, `Ed25519Signature2020`).
* `vc.proof.created`(string)**:** Timestamp (ISO 8601) when the proof was generated.
* `vc.proof.proofPurpose`(string)**:**&#x50;urpose of the proof (e.g., `assertionMethod` to assert authenticity).
* `vc.proof.proofValue` (string)**:**&#x54;he actual cryptographic proof value for BBS+ signatures.
* `vc.proof.verificationMethod`(string)**:** DID URL of the verification method (public key reference).
* `vc.proof.jws`(string)**:** JSON Web Signature (JWS) string created with ED25519, binding the VC data to the issuer’s key.
* `vc.@context`(string)**:** Array of JSON-LD contexts that define semantic meaning, including W3C VC data model, security vocabularies, and custom Zetrix metadata.

**(Encrypted) Request Body:** _(JSON)_

* Header: `secretKey` :  Encrypted ECDH key.
* Encrypted VP data {reveal attributes and range proof} . Refer to Create and Verify VP Process.

**Response Body:** _(JSON)_

* `blobId` (string): An identifier for the presentation blob that was created.
*   `blob` (string): The unsigned Verifiable Presentation data in a serialized form (likely JSON, possibly encoded as needed). This blob includes the disclosed information (attributes listed in `revealedAttributes`) and the cryptographic proof data for hidden attributes (including any range proofs). However, it may not yet contain a proof of holder possession (i.e., a signature from the holder’s DID). The holder needs to sign this blob (or a subset of it) with their own key to prove that they are the legitimate holder of the credential. After signing, the holder will use **Submit VP Signed Data** to complete the VP.

    The `blob` essentially contains a Verifiable Presentation object with everything except the holder's signature. It will reference the credential (or include a derived proof of it) and list revealed fields. The holder’s signing step will typically add a `proof` section indicating the holder’s verification method and signature.

**(Non-encrypted) Example Request:**

```json
{
    "vc": {
        "id": "did:zid:6421ca70444138f3495d5f50ddd174dfedec06d668fb697070b0a849771d7768",
        "type": [
            "VerifiableCredential",
            "DrivingLicense",
            "DegreeCertificate"
        ],
        "issuer": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb",
        "issuanceDate": "2025-01-01T00:00:00Z",
        "expirationDate": "2026-01-01T00:00:00Z",
        "credentialSubject": {
            "id": "did:zid:2c8c214c958e0f28e8e3aac60a38abd161e3135efa33bb1c4233e0c6a4518a17",
            "drivingLicense": {
                "name": "Ali bin Abu",
                "class": "B2",
                "age": "21",
                "gender": "Male",
                "idNo": "abc12345"
            },
            "degreeCertificate": {
                "course": "Science Computer",
                "cgpa": "3.9",
                "id": "gg1233222"
            }
        },
        "proof": [
            {
                "type": "BbsBlsSignature2020",
                "created": "2025-03-07T07:06:44.747608Z",
                "proofPurpose": "assertionMethod",
                "proofValue": "ukFiZpQ2quVC4mTgEuzyF8lVqOExC5HGBWnCHcxCuR7jPuZOhnWT9Lrg4w9YVqcM1QlHj0EpPUsTGYCnZA1-_pPAZYyLHjrPGZEv5rJADJRUkj6ocu7HgDxf5tnwvzFVyFABEjt0Ac4E9mBfHIrcLPA",
                "verificationMethod": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb#delegate-1"
            },
            {
                "type": "Ed25519Signature2020",
                


```

_(In the above example, the holder will reveal `fullName` and `expiryDate` openly. They will not reveal `licenseNumber` but instead prove that it lies between 900000000 and 999999999, perhaps to show the license number is within a certain format or range. The `challenge` is a random UUID from the verifier.)_

**(Encrypted) Example Request:**

**Header:**

| Key         | Value                                                                                                                                                                                                                                                                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `secretKey` | dyv1KFrmUzcWA\_dtytgYQmFP2vuFK9k\_3gj9tFvJc8W4nfEU628FpQPoDnhLgR\_ep5uBGgAECWUW-uiBWYZL5YfOc42IgZh46FtrNd2sy6gfVG8jzSdikpb5iwdN6Ptfpb3bSTq\_SIIPvHuPrzFhb7IXePXX8pPSLE1qQ0vZ9hT43ISb6d5jIIpBYTIZApuACKYcMngpU7neUSwgjd\_48Py4wF-grVnJon5pZYbhIfrzDR-IHckaXjHH4iHHCNamANaBxxZCmFV4UHMnKHKuSkhMDUZwpyd707CRbnAVgF\_6NqBlmlDgE\_04CmI-ztK-M9EmenTjmgKEw0ZL3pWZyg== |

**Request Body:** _(JSON)_

```postman_json
VfPKszAiX3phb_nWRkzaoxKJgy9QbUmkNjthHKQVPJZa7lrnzan3QI6ZR-Ol7pMz2XIHmqHkkxGWIP5I_LYTcmQW2FA-YPKQXs3ZIaFKt40znXS8ruIv9pIua3agfH6nL1sIn-tIZulvEXz0T6HL3kX6_NsCdbBdo_bMoJH9r6PWB10WFegA0lHcn_tCGO5ekAE4zeaaKaWS5wPhSA1DssrpRR_rgGGyEANESDryszfBzFkAkgfdPvDcmXXtE8HMESfQFmaHuOLjIRXh6R7Due1lk4jJGftmx12tTZYig62JQYWulGxfUWCMS7rGGBXMpDRqWsUMf-NhUXOKOQc9oFQ3VGf3qqcHKTje7oowZavk_BKpmflG7bTa6kaWGMxGBAKGhFQr0K0XgipupmYKWZhvykxagx9XMMuPYR5Mkqk3sp4AYfWLyFXf9aD9LOGaUK8E0i71PYoLbp5BvQFw_oTxKSgQDMCWv305X_O3F-oVChU6AjHv4eKovIHM3G4K_mQclvTNMhxGgMs9Qz59acwe2DsHBiLGhiew7TRVjDf6ooweaV6qUbGb-z0pPK0ZvfsGNFMs_k4Tmoq7WnbeNSR0WJBft0SIu_IzpmYpwlp-zYqEF7JvRm-AGYCYPB-3gRvVRRO0evyBF5x1kvB944TaQgZFgu13EN1dqiV7wsFUHFcEvgwnJS-7i2PcHwZz3zvnUpaFC5jv0TyCBdp7_tI8oVVu2u1ku4oIsAwMTiLK8hIDYhYBIEiIeJpJ832e-WqX6wotzN6xPtIOaidREmOBaJ4DEpV51sg0iS_4C4OGimFxc60jpa1s5qy5Js22XPY7EFkhAa9lwl6dCJs8J-_DVo3ImpmM88clVOXC_t4uJ1HSC6tCwcKfG2JJi_MnzVEpsNlCPT_b7sYDYrdIW1Rew01PaDk6kroxcWS2VzU-civUE2ZWjuQN8air-reGNhZjV0n4fWP3TIc11LfUUq-rc6q8S3L2JtQcv4s7mLcBg8nJ1sD33wl1WxzyJlQ39eYsf9dySxkWKpuW8o8lV4SO2v1VqLXzrx8O94y1vtI-4OABB4i0Ws5P72RtDNATj4MtXfUsa7bLVujoU0fAGKxIfkg3dN8Q03XsD8hESie-TfTtdxvalYEb8jDXjqVawzgJ0YGvMgyqLJgiAG9qtLxrlfDc9ehV8bdw69XngThbQZcOu_CNRcnDezOQMB3pRi0ZRMZhLtaEl3yJPWYsp88k7TBNppqdKlM6vUd8TfYBtoBuIn8Br5t_prpdB0ZkdSiwpCLKuRKLKUDImidqjs37wzErc4CyhsJ9Z5NNiFhMcukya_qX43SCrIqPS2VzjqFJGzgj2WkZJzJgMB06695DGg9IZn1KYHkiV5jJNwJhiMaRNjGBkqcKcHZZFSeI88RNTukEUlySorLJ1bCvSSKU3AOprTGzbNF1QxjL4poW-NwfXJd3GqGpXNgvzzg9uzyhI-XLlvYRg2FkD8wR7beYMsOm9KqH9yJFE3HQPf1DISPK3y2muLsUCP8vtQ0XJ0N8Zr8j3Z-gIcJ5rYgrSqnSJa-RPY_dNn1fppScnWewDLdoakjMzlQV8xaqoYAt0CLWsbxrdQwxLgT0Pd_gtfU7LBsNdxEdRwjb3cXtXsLdwXy2ofms3FIJBEoHJSXVL9Qaq3FvFv4ZoRgaMk4YgHesBA_rZRbXq_OvTbeGaC3D2mShcWmAPDy3ejJcU3JexWykllLIgFqIQw9r2mOuPpcvczEvW-mskoJ4XOCTMNvqeTOc5XjKibGLZ6VfeMyj0kKVPFrd4EeCwTdaWJLjersOYmpB7tk9sruuLxioGF4vahM3bsAhTsQvxnSZjojuwG6dTGyqCpHgF6BBoYi3Y_aHbRfqTAVtL79DjzBotQ5VNuWhZk_ricmzCXll4DuVezYNx1oc4PfTgF0w214iWlFWlnNhxLvPaBCGZ4AACng2Nhn-wyAWV9HQJI0ChG1DKa_fWuwt_o1j6k8noZh1HZAmfed9Lh-KYitlsw3x-mu1qYiuETNK8AnOMGZFoy1yTPRhc-7Z5tghumqnV_sZhce0tohnByxSEKXxeaa76h1TPGfGAPGkwZSb2EbQrIYG37pmAyKSG3uLxgb-33q7j71m79OifEoX40-Bt5cQtPKSJbuzerrmmjH-EYxu9DL_eim_O1YejteBbUuqA6a6lLu3CvNRiyemrmGy8hBneOAae--QWyAvO8Jv4GCWjuBsH63EHbz4mz8_dazR5xmaoc9sLSCUwoOxQidHesBTQHvsxNMRbFTRtfWUMgqqX2h4y_4JfkvI0q8In5AzIgGqsNBBZLPqO2kFAX-pmd_QuWxHIO3bQUOOjBhEwnYDWm8Lh8XG4JU8Qw7gTy5E7-4ibATsMBE15ZD8rWzJm0HQCA-4xRCT-TecJcwDHLrX2mNC08pcy8rmNZ9KWa8Ih7P2AmVpO_5eBPJaFP_43c8hx-VG7x6BJTeIl6HY2uowQrc7OfEHUjo12lbbyaDSqXCoPZ1e9ubwOeSKmRF-OdALhyqdDxj6XghHZ1L6ec_9BDySnglqVxP3h6bEjhT5mtHRUdqYNib9eYrj4h43DxtztLjd2q8smHBWkttFfmL8CfTAG8ZFgTPoKDaLUvE1XT1vF--53pkXK-1GYmdsqKImqB7Ss2PHAv3HBIC1rZp_rw_dN7dbsDUBxOYkRaVwiELv6DEpEUKSieq-bvz54aD33sNllat7aUNBhD9rENK216PTkUTamSygwuA-Scr44hhA9N5sBzSa-dLVeTRqGo1T9mo71SmUZLFgD6AXYgZxENJOrxKL-zqDIOBomLOXi5827MosoxKrG-06HPzIizTZfCgF8FhD989j17-c0aSRhH8CfnV1axpjlfmpXa3RjW3m-m6XKcLBkFfIQKl_QF8OmZ4o-JKbkHRyK1vNiCDE_Qlbo1jTlQDGFZtFrH9BomgKoZ_eE-Bl4OVPkKtDQQUmsi_ceTEHHW0_Y9PN_Ot6vt_iMHjT69GLoiWxuu4ttvhW3YmZ9sFlpMXt6D5NEMkMEwcHM7XVFtL_E6Lzq5lA5MhMxkZfPqQiP0xOSaOrrY4KubcdXXMrvhKz6tLOeBHBQ6eEWtqrgVkFetdN1LDMWblYLbSMiYmGPW8f673KXjjYKW3-KHr24iy8OBjZ-IVULPr2vnHsvyVJj0YkMqXI8TuumiwJO1qJFoYQMbBqhscG6MG1KEWhmaHxkb6gm1oaJL3PyCHbdMeb3BJ5MFQU7zaAS9tby680NRKHnAezER85dpNDr7RZ_4NUzJSQ9IlnyxEbt7fLDiRY_Yd747zp-X1tm6lcTvnhSLwchXz4NUFAikCRInUvIvQYC5y0DU5tmcNaNVrlUcgsbRprB8naGv5DYWFVniOK8Mhp9sgrsY_Qb79V05ld9LI9KUv7aJ-6OpDRw4bfnEDPvzkM7PImYAe_EePLYfZtpcPOvls0qzq6-RGRzWR8b1xfVUmrVnlakFAPjD_Xnc6zcvbcY2kyyIlJ4x7daysXd4wpYAasqvymcw_279UY-_U4gkVeahvswktWoY8LB34NfYZHMlbsRd1NvbwkhxAS1BN73XX91Y25gKO4RAa6ZX3uVtJJr6EjiVaUVw==
```

**Example Response:**

```json
{
    "object": {
        "blobId": "55C2535A215617074C457F4BD84051A6",
        "blob": "65794a68624763694f694a465a45525451534a392e65794a4159323975644756346443493657794a6f64485277637a6f764c336433647935334d793576636d63764d6a41784f43396a636d566b5a57353061574673637939324d534973496d68306448427a4f693876647a4e705a433576636d63766332566a64584a7064486b76596d4a7a4c335978496c3073496d68766247526c63694936496d52705a44703661575136596d45305a6a466d593259324f44677a4d574531597a59344f57526d595745794d546b315a47457859544e684e324d7a4e7a6b7a4d4449794f4759344f4459324d54466d4f544d325a6d566b4d47526d4e6a5a694f534973496e5235634755694f6c7369566d567961575a7059574a735a5642795a584e6c626e526864476c7662694a644c434a325a584a705a6d6c68596d786c51334a6c5a47567564476c68624349365733736951474e76626e526c654851694f6c73696148523063484d364c79393364336375647a4d7562334a6e4c7a49774d54677659334a6c5a47567564476c6862484d76646a45694c434a6f64485277637a6f764c33637a6157517562334a6e4c334e6c59335679615852354c324a69637939324d534973496d68306448427a4f6938766447567a644331756232526c4c6e706c64484a706543356a623230765a32563051574e6a623356756445316c64474645595852685032466b5a484a6c63334d39576c52594d32466b526d35616358566c5558525a53314a56546d525864323434536c526c4e6a6457643052684d55567951535a725a586b39644756746347786864475666583252705a447036615751364d5759335a5759325a57526b4e5445324e5459784d7a67345a6a67314d7a59794d4449335a6a45785a544669595751324f4455794e44426d5a6d457759545668596d55775a445934596a51354e6a4a6b5a546c6b4e794a644c434a6a636d566b5a5735306157467355335669616d566a6443493665794a705a434936496d52705a44703661575136596d45305a6a466d593259324f44677a4d574531597a59344f57526d595745794d546b315a47457859544e684e324d7a4e7a6b7a4d4449794f4759344f4459324d54466d4f544d325a6d566b4d47526d4e6a5a694f534973496e426863334e7762334a305457467359586c7a615745694f6e73695957646c496a6f694d6a45694c434a6a6247467a63794936496b4979496977695a3256755a475679496a6f69545746735a534973496d3568625755694f694a4262476b67596d6c754945466964534a3966537769615751694f694a6b61575136656d6c6b4f6d466a5a6d557a4e544e6a597a63794d5441304d5459785a44646d4e6a46694f5451784d44466c59575a6c596a6b32596a41784d5759784d47466c4e546779596a55304f4449314d7a4d315a575a6959545134593245694c434a7063334e315a5849694f694a6b61575136656d6c6b4f6a426b4e4467354d545a6c4f4749775a6a45774d6a6b33596a41794d3249324f44426d596a6468596a497a4e6a646b4e5756694d444e684d574532596d49344e7a5a6b4e6a4e6b4d5745355a6a55324e7a646b593249694c434a77636d39765a6949365733736959334a6c5958526c5a434936496a49774d6a55744d4463744d6a52554d4463364e5451364d6a59754e5463794e6a51774e54417757694973496e42796232396d554856796347397a5a534936496d467a6332567964476c76626b316c644768765a434973496e42796232396d566d4673645755694f694a3161485a6d4e7a513356335236626d4a54635856365956645a576d7849616a4258634774556445704b557a4179616b356d625639564f47746b6348466d555746756456564c55557777543368425245685a6255565651304670566d633057457044574642734f5551784f48424a64476c59626b52705248687355326c66516e4250615568454e6d56485a6e5669636b566f58314e575a304630646b70575157705155486c7155444254624568564c533079576b784861315278593370365a6b3936555464545832396e4969776964486c775a534936496b4a6963304a7363314e705a323568644856795a5449774d6a41694c434a325a584a705a6d6c6a595852706232354e5a58526f623251694f694a6b61575136656d6c6b4f6a426b4e4467354d545a6c4f4749775a6a45774d6a6b33596a41794d3249324f44426d596a6468596a497a4e6a646b4e5756694d444e684d574532596d49344e7a5a6b4e6a4e6b4d5745355a6a55324e7a646b5932496a5a4756735a5764686447564c5a586b744e434a394c48736959334a6c5958526c5a434936496a49774d6a55744d4463744d6a52554d4463364e5451364d6a59754e5463324e6a4d304f54417757694973496d703363794936496d5635536d686952324e7054326c4b526c7046556c525255306f354c6d5635536b465a4d6a6c315a4564574e47524453545a58655570765a45685364324e3662335a4d4d32517a5a486b314d3031354e585a6a62574e3254577042654539444f57706a62565a72576c63314d474658526e4e6a65546b7954564e4a63306c746144426b53454a3654326b34646d5236546e4261517a56325932316a646d4d79566d706b574570775a456872646c6c74536e704d4d316c3453576c3361574649556a426a5345303254486b354d467059546a424d567a5632576b645664575674566a426a625777305447314f646d4a544f57356157464a4357544a4f646d52584e5442555631597757565653614752485256395a56314a7259323157656d4e364d574657526d64365756645352324a736348686b56315a535a455a7354465673566b3961526d517a596d706f53315a4856544a4f4d566f7a556b644665464a59536b4a4b625852735a5651784d4670584d58646952305977576c59355a6c704862477450626e4277576b5276654670715a477861616c7073576b64524d5531555754464f616b56365430526f625539455658704f616b6c335457706b62553155526d784e5630706f576b525a4e4535555354424e523170745756524361453558526d6c6156454a72546d706f61553545617a4a4e62564a73543164524d306c734d484e4a62553535576c645362474a75556e425a563368555a46644b63567058546a424a616e41335357317361306c7162326c6152327872543235776346704563476c5a56464a7454566461616c7071575452505245313457565257616b35715a7a56615231706f5756524a65453955566d745a56455a6f54544a464d316c3654544e50564531335457704a4e4670715a7a524f616c6c345456645a4e553136576d316156314633576b645a4d6b35745354564a6158647059306447656d4d7a516e5a6a626c4a4f5756643461475659546e425a55306b325a586c4b61466f7956576c5061556c3554564e4a63306c74546e4e5a57453536535770766156467153576c4d51307075576c63316131705953576c506155704f5756643462456c7064326c6962555a30576c4e4a4e6b6c72526e4e6855304a70595663305a314658536a464a626a453554454e4b6346704453545a4a62564a77576b52774e6d465855545a5a56303574576c524e4d553079546d704f656b6c345455525265453571526d744f4d6c6b795456644a4e5535455258644e56315a6f576d315761553955576d6c4e52455634576d704664316c585654465052457070546c52524e4531715658704e656c5a73576d314b614535456147705a55306c7a53573173656d4d7a566d786a61556b32535731536346704563445a6856314532545564524d4539456133684f62565530575770436255315551586c50564752705455524a656c6c715754524e52317070546a4a476155317154544a4f4d6c4578576c644a643030795258685a564670705757706e4d30357455544a4e4d6c4634575652736255355557544e4f4d6c4a7157576c4a63306c75556a566a523156705432787a61565a74566e6c68563170775756644b63317056546e6c6156314a73596d355363466c5864326c4d5130705255565a4f564656464f564e5751304a4f55565634516c6457546b70525530706b54454e4b4d6c6c586548426152567035596a49776155397053586c4e52456b785446524265457855515868575245463354327042643039715158645861556c7a5357356161474a4862477457567a5577595664336155397053586c4e52456b795446524265457855515868575245463354327042643039715158645861556f354c6b317161455a4f61305a43546c525a4d6b355554544a52656d63775456524f516b35715258644f61315635556c52724d464a5653544e5052555a45556b52424e5531565354524e52453078556d7046656c4a56556b645052456b31556b564e4d453955556b565052476434556d7057513031465258704f61306c365558706b5256465562454e4f656b4579546a42524d553545576b4e5256456b30556d706e656b353657545252565646345456526e656b3545617a4e4e565646345556525a656c4a456245565356565a45545770426555314657544a5256455a46546e706a4e55314559794973496e42796232396d554856796347397a5a534936496d467a6332567964476c76626b316c644768765a434973496e5235634755694f694a465a4449314e54453555326c6e626d463064584a6c4d6a41794d434973496e5a6c636d6c6d61574e6864476c76626b316c644768765a434936496d52705a447036615751364d4751304f446b784e6d5534596a426d4d5441794f5464694d44497a596a59344d475a694e3246694d6a4d324e3251315a5749774d32457859545a69596a67334e6d51324d32517859546c6d4e5459334e32526a59694e6a62323530636d39736247567953325635496e31644c434a306558426c496a7062496c5a6c636d6c6d6157466962475644636d566b5a57353061574673496977695545465455314250556c51675455464d51566c545355456958537769646d467361575247636d3974496a6f694d6a41794e5330774d5330774d5651774d446f774d446f774d466f694c434a32595778705a46567564476c73496a6f694d6a41794e6930774d5330774d5651774d446f774d446f774d466f6966563139"
    },
    "messages": []
}
```

_(For readability, the blob content is shown as a snippet. In practice, `blob` will be a JSON string or structure containing the Verifiable Presentation with the proof of the credential. The proof inside the blob at this stage is likely a **BbsBlsSignatureProof2020** (or similar) that contains the derived proof from the BBS+ signature, which shows consistency of hidden attributes and correctness of revealed ones. The holder’s own signature is not yet included.)_

**Error Cases:**

* `400 Bad Request` – Missing or invalid input (e.g., no `vcObject` provided, or the credential object is malformed; requesting to reveal an attribute that isn’t in the credential; rangeProof parameters invalid such as min > max).
* `401 Unauthorized` – If the holder is not authorized to create a VP for the given credential (e.g., the credential’s holder DID does not match the authenticated user). Typically, the holder should only present their own credential.
* `404 Not Found` – If a `vcId` was expected instead of `vcObject` and not found (not applicable in our assumed interface where `vcObject` is given).
* `500 Internal Server Error` – The server encountered an error during proof generation (could be due to cryptographic library issues or memory constraints if the credential is large). This could also happen if the credential’s proof is not valid (e.g., if the BBS+ signature in `vcObject` failed verification against issuer’s public key, meaning the credential is suspect).

### Submit VP Signed Data

**(Non-encrypted) Endpoint:** POST `/cred/v1/vp/submit`

**(Encrypted) Endpoint:** POST `/cred/v1/vp/enc/submit`

**Description:** Completes the verifiable presentation by incorporating the holder’s signature (or proof of holder) into the presentation blob. The holder calls this after **Create VP Blob**, providing their signature over the blob (usually covering the digest of the presentation and the verifier’s challenge). This produces the final Verifiable Presentation that can be given to a verifier. Essentially, this step adds a layer of proof that the presentation is furnished by the holder in possession of the credential, preventing anyone else from reusing the presentation.

**(Non-encrypted) Request Body:** _(JSON)_

* `blobId` (string): The identifier of the presentation blob from the create step.
* `ed25519SignData` (string): Signature of the credential data generated with the issuer’s ED25519 private key.
* `ed25519PubKey` (string): The issuer’s ED25519 public key used for verifying the credential signature.

**(Encrypted) Request Body:** _(JSON)_

* Header: `secretKey` :  Encrypted ECDH key.
* Encrypted VP data {`blobId,` `ed25519SignData,` `ed25519PubKey`} . Refer to Create and Verify VP Process.

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
    "blobId": "DE629ACA66639EE4E37A5A2B93A37BF7",
    "ed25519SignData": "0B2BD318A79E5A676ACDEFCF6625DF1347A0C6DDEA45CFAA1EDD3E0D06096CE1BF8866CA3619E10153342C65C08A8F20DE798D0EF37834C9BF1C7844F9A53305",
    "ed25519PubKey": "b001ba4f1fcf68831a5c689dfaa2195da1a3a7c37930228f886611f936fed0df66b94a10ec51"
}
```

_(In this example, `signature` might be an Ed25519 signature in JWS format or raw base64. The `publicKey` is a placeholder for the holder’s public key. In practice, the holder’s DID and key could be resolved by the server, but it’s provided here for verification.)_

**(Encrypted) Example request:**

**Header:**

| Key         | Value                                                                                                                                                                                                                                                                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `secretKey` | dyv1KFrmUzcWA\_dtytgYQmFP2vuFK9k\_3gj9tFvJc8W4nfEU628FpQPoDnhLgR\_ep5uBGgAECWUW-uiBWYZL5YfOc42IgZh46FtrNd2sy6gfVG8jzSdikpb5iwdN6Ptfpb3bSTq\_SIIPvHuPrzFhb7IXePXX8pPSLE1qQ0vZ9hT43ISb6d5jIIpBYTIZApuACKYcMngpU7neUSwgjd\_48Py4wF-grVnJon5pZYbhIfrzDR-IHckaXjHH4iHHCNamANaBxxZCmFV4UHMnKHKuSkhMDUZwpyd707CRbnAVgF\_6NqBlmlDgE\_04CmI-ztK-M9EmenTjmgKEw0ZL3pWZyg== |

**Request Body:** _(JSON)_

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

**(Non-encrypted) Endpoint:** POST `/cred/v1/vp/verify`

**(Encrypted) Endpoint:** POST `/cred/v1/vp/enc/verify`

**Description:** Verifies a verifiable presentation. A verifier (or any relying party) uses this endpoint to check the authenticity and validity of a presented credential. This involves verifying the holder’s signature, the credential’s BBS+ proof, and checking the credential’s status (whether it’s revoked or expired) and that any disclosed data satisfies requirements (including range proofs). The endpoint returns whether the presentation is valid and may include the disclosed data.

**(Non-encrypted) Request Body:** _(JSON)_

* `vp` (object): The Verifiable Presentation to verify, provided as a JSON object. This should be the full presentation as obtained from the holder (for example, exactly what was returned by the **Submit VP** step or what the holder’s wallet produced). It contains both the credential proof and the holder’s proof. The service will use this object to perform all necessary verifications.\
  &#xNAN;_(Note: In some contexts, the VP might be passed as a base64 string or a JWT. Here we assume JSON for simplicity, which aligns with the earlier endpoints.)_

**(Encrypted) Request Body:** _(JSON)_

* Header: `secretKey` :  Encrypted ECDH key.
* Encrypted VP data {`vp`} . Refer to Create and Verify VP Process.

**Response Body:** _(JSON)_

* `verified` (boolean): `true` if the presentation and the credential within it are cryptographically valid and not revoked/expired; `false` if any verification step fails.
* `vcDetails` (object): If `verified` is true, this object contains the attributes from the credential that were revealed and verified. It will map attribute keys to their values as presented. For example: `"fullName": "Alice Chen", "expiryDate": "2030-12-31"`. These are the pieces of information the verifier is allowed to see and trust. Attributes that were not revealed will not appear here. For range proofs, the verifier might not get the exact value but knows the range condition was satisfied; the response might include a note like `"licenseNumber": "Verified in range 900000000-999999999"` or similar to indicate a successful range check (the exact format may vary, but including the fact of verification is useful).
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

**Header:**

| Key         | Value                                                                                                                                                                                                                                                                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `secretKey` | dyv1KFrmUzcWA\_dtytgYQmFP2vuFK9k\_3gj9tFvJc8W4nfEU628FpQPoDnhLgR\_ep5uBGgAECWUW-uiBWYZL5YfOc42IgZh46FtrNd2sy6gfVG8jzSdikpb5iwdN6Ptfpb3bSTq\_SIIPvHuPrzFhb7IXePXX8pPSLE1qQ0vZ9hT43ISb6d5jIIpBYTIZApuACKYcMngpU7neUSwgjd\_48Py4wF-grVnJon5pZYbhIfrzDR-IHckaXjHH4iHHCNamANaBxxZCmFV4UHMnKHKuSkhMDUZwpyd707CRbnAVgF\_6NqBlmlDgE\_04CmI-ztK-M9EmenTjmgKEw0ZL3pWZyg== |

**Request Body:** _(JSON)_

```postman_json
hxtxXa3VqAS51w6K6CIDyztFPhEc52-pljUIpjxYLY2AgtAZfZSSbnn9AfTM30cOISXfupnYryXoSljpw-z-FbScRZTzuzXbp8f3r6d-C2myBiGQsAYjubnmmwYIVWZfzg2lq-aFmgXJjlisP0tbV-1gDB2OGCc8lNM-KeIL9_vxaxzXm7dfCZDrQFmTdTL4W6BUbKUpo2E0as63D4o5M5aAL0HzR9TccnRAN2C4Qe3JMi_x6ElUrN_qyxs_fyoaEf6jUlMAGnFMpv5GK_tutuMiRJfv0cUeyeG3lvJ53h86GQtWE6lpAJZcBLCkF9q-QSGQZ-AiRu1_1UVW2mgiTs5LdrttOBPfQRHMF8dkbD_A71gt-ZMNAQRoaJeFF5SN5syliqY_zAehpem3IfsoTcMd0DKvS2bhYCEBsVnLuQ-4Z2tyIda0kCditlCwg-2FinBSGc8BquVrRFB1Q9XGbZjR8kPPLbFx4rH9q6kCToYrRsvXzcLue5Viis0xX3_IEq4pv3PSTNcG9JDolAZ6tFU-fHBTzhb8LEJkan_oX54dmEdaKUXnnBWqfq3s7tYHH894v8PTHkJgxPnEMWQsSvHXkGXaYsiMLRopFeuME3XZOCeb1E6dQ1MfEyDzMpOMXqG2Y_0F9RJcIQcHm1Ky5ujJPtu8Byx1sIvAjUWjJCMfpNafhcJxpzjM3bhRj9OLKYW3Sg6-mCWyQGAvzPFVWnjvHzPVkUmJ4oXDwKjr5CICGFs6kQL5CD1dlbWzL_TL4zHRw4J0OVCnlDxsHD_87wnf0c9xY8DMsQ0PIB5MGlVtx80gxndbKbuTIUTMmsE5YtfQGzyRMEeyu8GrsIJvXOzF_odFjuZ2cur_sXRmfFdkE0hk8ycuB_qJiZZPLe-mDrAo2DHwPsI5vCNDvPMYSVFF2gzx6CztRTb4elRH9S72q6Y2-1R29w0261k_jvR4--UWKpijdkpX0UxFsc_uyF0GYHXxDQQsZTaVVHO0P1cQ43WAxUfYDrpDEDJ04uC7Z-uAK8erJ-jfByQ8QiP3mVWWQOvJq4vmPPUG_5CsubFvKxAVsF8pqaKCeWVQfZ5H6VCwIMtDTIy6aks0tzdKAabbsml7JXPqPMKmaZd-67bhWWPMpWIeS13_zfbRqaGHfuyf1p9EE5q4vwYXRMlyXcC5SvsVVW5INe8ezIAGQ7KiqQVgjrbyiZeq4HiFShL5yLq9NAnm7vTCcDtUwCVU6m1sTU4wlo114UNF_PmUXoDVteJLYrj65XkpB3mRCu728gzWXsM0oUm3PWecaqRJDWm-KqMA0ljhgmrCyGPv8n-sf2udy-SUzD7nwYl5E6TKkkqcwwbYh4Y647RU6eXpg8FyBdCFZLyWaGUXQE-f8S8ihwc8oRgb9088A4JRO3GnjtCEAiQkK6L8nf-eQZRAEw9Z_3CghKuU0V_2Hg8d4es2XbODvM8M0L1oW3LEVPcejPYvoTCS6Rv-9BpV-hoDcpuYtkXHZlUbM5IU34U9C5D4Gb-v9CXUtS6SfEw3DI6G72dkJmjh37Xpf6kmUEoG4iwogdUcfpe9zQBTsKFWreNX1RHYe4Cy7o1ShpZJlmuzGRstFt-h6Qp8YrsnVYLJERfaMslnRKDaD_FbGTZsi_NE85sX1IPWbQGfBRLWB84KCtPDKmBU1UbHS4nMCELXB1XEYIj7v2-_b7oyLlsT8aEC2TBaaDmgQjdjFVXQ1n3r_x_65UkI6DBEFvWGBlARZzqYWKd2wLgjuPhyglri1HqK1Nbe1hyftrefSeyJ7PpIrv2ZJo1OYfKSXhEFR5qPaT5DrfwJTXc4mySXf4ld3mz7bVMG1wTSWXlGT6Cg1IiO5OuigL8DQbqR082Ycb3n1S-vcxBGJ_pVoKR8r87P_80dggZBwfPWOFNcbkq4gL2VprVTI1mjU9W8qh3IW6JiBTLZbkw-YrgAU4--ng8woJs0dCqwPOLMcwcSE-JA9jlpGifBa1p0pmVyRuufUQl-BGVRUQ-5B79s3RRbJ5j0jMt5T6wOsAKUtNx5ZZdqUl5TQFsW1kOtl7ikq97qIAajzytbWyBzna8cifE8QGmuhT0AXZWdzVXW_f8LliOLy6Y6EkyPU3RmawzuzNKfJ6gLQznL1fTeKKdc9BJiXEJoWSJpI-NZ-YnNDykinQz7T4gGyQypHN-spqqsX_buy1PS3XfRCbybv26IHW_7mDrzr5iX1QAb5ToOBZsCKh-VxbfgyX1-vAj6p75wBVMl6Fy7T_vNId3u9gZW4ouU-eErL84qcGcfZYFEC35x1omLli5Gm3FrJ1hdk5wX1qkbFuImqGwJqSChLQhcRLBHE-1XKXSDwHb2O7LjnJIcKhjWTYNvlTG4wwiZsFxLUIuPUDaVdFHCNETAg0j1lXOKXt3_qWMGfxc7lWwXu1cgwVJDAozfuByKJ_8t9Lcbn2zbYVPB6QYoB9z5Mp9zL6eVv71ZeogWlhy4MUlQTRfgJaDNTaTy5h01neqtCG5Rt2ZG5YY9I_3EvcXFbJ773czIzsA6AJnISLmUqNduQo44zFupiJ8MNC-HfcObOpcGM1nJwBvIb16X0WD20va6fs_rPfIHfDHSOF-Q2avFhTJDWb20kZVhjVhZl5IBi_b49P2CZWAjzUETgCah1KPlKGVFOWvqzRI2mHBaVbDbu4NULy-XGVJilbsdBHklJz_byU5WdmZyx2iONGtpMEavvqxYoYiafOb_tPS8qNGT6rYposqkILjsXeCwj7Jy2Xj8AJovtQSgT8xC3s3jrFfu99TNumRN1vCGmTeoZdCLq1IGwsP5BVnodICOPTvBaY_WKdkcRj99V31idCBaTtBSFPIIfB60HJ5gCbVoLDZCZqouJd59wcSoPox-sT_5OEdsKWy3Kj7VKpeIDLFpt1RokZ4fiXKv9yP-66XtSgvNE8XIWNpNYf4GqELPlnlrNiazNYy7gPrYshAd1j2cR26GxaLVAM489ykVtGU0xV0x5-QtwHRr0_p-MATvNXXKTwIXtLSuBHQoiYDzBxR78pzuq-DR3XCMWfQuUhOc1RV4-AjaKcr0VEVfWHAEuEP5BiRIXOMmnWfQbBuL9qHuVIqokVK7W_eig9_i_J7RiewFYhO5GBuNVfjbGElyEnBqHPHDCCPi3351UEJFC_jga_2AV-UpZJefbs1yiFAL-rfeZOVczkGdUX5I4BeTe7ONxWOS_iDj-e9zwlkZtuwWamvlPZvbRsJcbyLoRhMV4khDLGGynr3dLgFzDxBoArDIxu8HrSDG6L6oZlgYMw2J58HGJlQ2vY6MeFn5r6Xjpd0wk2ZkFzfLjpjgZ_lVVbw6UspfmIQChngGzOb6wXcJS6TeYBIQ8o_8AO3edBnjmE1pRWYWR8BeDnunPAln1FCMlexCYSijLMLX98kIGtTuo_n03L9DujDhJdiAIEOJAnkI_z1louBOyUMQkP8NuOGBQ9SKFYkVNDn_qM7Boelxy3Z-unNfMuQJRHHKg2opRvEZ2IWdXFcZ5ow4Rr6FzlMO0SqWOCsA3K6ZQZxUZVj4kyCfvb0Ph7XJH-LDyRY3_67435QWoUPBfEypsF3zfY9zvugtNdVEosxNvLYLa7iX8Q-jlpHq7mfGwmnTCUii3-TP8ZyNxdu8Y4zdfZHDpgl3aA5xL3D7ZyFPl4x2WKqffLqsVgbqTe5uAy3D89QqsFT7JHqwxV2_DX12m7uohD34MExDm17eHBovy2wuy6IUN78V88LkxnS6Axt28m4dG7dpypgvE5zHK8jkPTVnZeQf9arTltAJKcOXUt9jQVBmc-UZ2PUNTzOjbV3TTnmA7gSOXI2XlHY_lpJ_XxUPdIbTQslW4FmERiUKSg4O11m8SBs5KP5pzpoDJejdKarnTQlayLacjbW4835SNKIJyfQwPIHV2i5Xj9r3oYvJPwVO-uygiSM0bevGJZ1gzL6rE0ykZ7GBBoygve5BrV_VaL12riwHlbs9U788_evc-OrJASA5kw2I28jXIGtMUKoGZK02tcW_ZgAQHwbteXH0ymweqlgTlnbRtuvFzBGLrtUWoRVrFkuOuRzf7zUESq6tUQue_6CnsLJYD-cG2--9R6DeqnfLmtIgAQ_qTzl7hJQvahrlRHp5NAs3u3Dit8ZDtIPyMFpyjY30Qr9SnHr3ZgLh_xlx_1dbixf1dTmbfVG85tR0RSI50nZ0_vU4fuuZ4OtSZly_0b7YlU06NFZ6uRYKOZJudTeGgdaCVx_3Ao74aCv2Ssxsfs0g9CCC0TsM_0nQrF2aUi8ddI1n0sjylfdS0AkR_d7XiwcLNqZcIftaB7nsBxxq0_TLpaZl3AAfwSemIJYkVP33QFOAeY5inqUlU615jCoTAVSSmXXuSL-s-zpXOOSr__C510gfMc76DlbomxrdzM-xI-o8ScYK5RvvvH62VGjOy4if_SNhvrfK77bVqMbzWwQsoIt-ExXomKZGSZL8kZaOI37ae83zM_a6CPzut-qIvj_v5zoNn-JEbCT4w49xQGj9HNAxLjbKz35IGdQpdgzKvligfzKHdGhe_04JxMVIbdttdtbazFuenzEcU-vAj1ENqDF6oNKKCWxfQTOoohIrfwuuiEovaLdIRhd9nUqWLBSRQQG8faDi1tcdini-mXe420zAQibkGWkIH4tIvMGZV8LuxpKgTHQijYZdv59XZZixOCrTaveLXydOqPCHf40N3x5bdmnmsfaKINO9JPkX_xG1i7W8CAiK4X0YYsxKFpNv_mwToam0qJdiO6JE8fKpLpBcD9Se-T43IjiNB3OW2lRJxgk1Gbq7Qt9Z63vUwTIbWRoeQtLjFynFwNMCWIzx028OpWrM9j7RqEqZUaXoPl7CmxoqSJvPbsC8o4ab-D6Yye4yfJ7ukku4XMv7f6-UkPauxV4aBjS4MIfg6rH4LJyh_qXl_XfPcc906ppi_I4OXat7L2xsmVoyCyjbcGTawg1OgAIwN8uso-Z1glZJUhTZXznk180yK8ZaS_eEnGSliT0Lnont5eTjuwqcI-lnjvNkHII7xuPad87lCyqJTDk_U0ABaJD17LIYvhdCMCREl1_uJk7kHf2eHP1m3jVp3RKsSLlnOl6tYHm0TnUgTniRi1QCzaf-faotaodUdTJrKOF4TzBng5SywjfhXnq1Bx3Am7iub-NqEhDuKqZU23DkNbsLlAawRQpFN4jcMk465OR5cRceB57HIN0NmYICfAxyfAh3onLvnM4bYsbPN4-2R4xDfsFMwLCrq2l3fS5bC5HybXv7PGY_6kilsB0qnK-SIv8OhY4LvA0gbE7RvV3YOzpImtvMUdA1DCX0t9DgfSZLcHn8dlVKVWNaXRqbCmxBGGn6oqFi2nV2INt1hXuKQi11DE6vkDS1HsSk8vENd0hfc7XXpBkGN9mZ7zBydabPNarUpMpQlaUaCcSK5itd3s0I-hafml05N9iB7_Y77_QGgmnT434wP-9VDpmobuzmaiqvb-p4F6ZN1zD6N3nlrF4t5M4L9-TewouSqVG3CoQa7diN_e2vwYuxcY6Jh3hYWKFrJXoh2jZ9TH181T9QBnK_B_FabPqfrt7aoK9xlvyEJPvtkpfoqhhoLaeUaMmsHUqYalULnF4s3Hvm04iH7Zz4gVQBwYUtuMFJyyb05i-J3iQuNVSNar5ALKeOq2bZ7PDbM11N6AyHvhqucuGfCNGEcbhD70aB9mAORX-OvxjJTHMZoZE9eQzyVnOi4G2d_tYPm9sYEwnc2I63MHPIamPafKRo2MtpgBM-ow6i1hgMHm3umCyahHouQz-vFxS-gkNpedUJ7FNjKaVHxA4oj8B2rzG47ujLtIk2-wILdfWEpuRyU9KMT9d8WQJ56JNQGJ6vK6MxENDaapwv7UyMzUsDTEqmdpgmAEMTy1MlIdwfZVac3JuhG8srYCxPpeIBVyFXTAmkBOZQMCH4JE5Tzy5x5QraJ2aW6fYgqIKD3Ly43P3PG2t4XY7SvcIi1L4lTRXGo_V5hcYKDLXOhSFPn8oYM3DpQK75Ex7s596kONQsvFqbmKaavdaU6OQCd8RHPPFrS8YfbUvTrG6Q6IvMhtMglliW_XtgywvPnQh4jUb9Zk4tgHAyCYzXvxibvh8u4ipXKPSchMVQKsdDVKgZwYeSH0xngHEq1v6MIHPXUwgHGwrPghvuEuLQfFoADMNvOZ67qCPhEcy2PicnXvnPnB4hUsbNRDoSS26ewdNW73j10rfl38dMFm71E4VXVb1jc5IK7uSEr7o0TFPYcFdp8NW8mdlRn31mrWBlCsRDaiYEVc4oV7i5WOhXH1Ua-zO_evuyyBoFvC5hjXCluCYPzm6bxIovCb-VsTPKr-NT_3jMaKXXeBrB4qA1Lf_M9hBpJ65BjwcrhR0c9CeMfhLaymuLZh0c15-KtKMlB0tgwfjW28Bw406z3pSoywnU1Wem40K5jNTKqkUNdSprscjRsIoqIN2BntJAyX5Q2ARXj2F4wJYVNt06QfARc4ZkXvmWCWoAwjro5aRcpKqD2siAqbBGE5brpk9gQBrt9n-SRzxRoMOyU_TYCE_qoikSKBN5MkDVnyFxZGtlGV6q3wPqpYTs6fpzHsbXyG1RZYfGTlvhQSEaW0Rdl-whi4Zovvr7jqIx-qLZo_bI0FyGkxtEK1upmsRnqMuEYlPFUmRY5DCF80eGRtGicZPUtjTBmGTxV6Mq5SuZ2PeVjUMpVicjAQ-UG3SrKcdNUqhXf2PLpsqz6aFTcEzoZoTIORhyTWAz-_gPx7yQXzArm7NWFsxlTXYK8a-6NX4zsWl0_NnwOjotJiI-h-KT8o4wmWm9zpEreuWbgP_WovLSMLpKTJhxKvSfZ_c1iWheEnR3LTTODGSl9cZYhbV4b7kcq6jHrU-UrodMhVREe4ao8hShH9rx7UgA43HH6k5He_8-TETuaaKdiomcLM96vc5Olvh_E2_vxUjQoPq4ylHUlRp3d3La6BuaYdvuXKsywUKqr5X4zIlKdHsnOI6N4M2Prvf7yFPGIIO0vNlvKYr2QRoESzI-a7j1fUv4NMY9kv_RpWE5FQ9qyw_MFs2jMYVqsxBJ3wcTE7ueTPXajIT4LaL9UxzLi_zmLhvY1a-bLfnIJrYIj7jiSv4TZbZdTZInByTMghnovaD4JqxsTTw2XwloSExlEQa5tly964XZPTBP4GQcWAHjWzd0m2i5HB9YmTYKH9W_pnoIgW7fKh4wJpvMwBbPNrXhQf7a8SG3alXHjeBfsThAaWDMpioMywJ4SgSVC7_5bOPP89k_niI7iQ2xSghUnavWgFqXp3KxMNyi2dRz6ZePtuUb9fXTnu2M-ueWoKI4J8xRCgkRd5czuTbFBjZXkB2kAU3PsFj2drM2uwaJuKS_lxXvEE-IkANZWphlQdeA49xqO1JzA5DNdJT_hW0Orrjk7NjrHVAZMebJ85HaK9vSVxoIdA-9M60lfx295mXJgSwguO8X1wuJPbc55JRLph2q9CzlTVc1UXovzaokspgNBaMhDeE0lBXLITvJUC9Ydt-1n7aJHHdpG1qY5AQiA_Bcb_QJQ438L7FclzSLSPQ7CMHzFQg_p6eMb7st8yP-F-4pyJ9c3HU-zrOsZeWi4-WlUdP4UMuqZudq36ELhtVZplHAHFSaZ0_S1tF8xxvPiknFnaVaNyncsihAB8x5DxEb5Q-01r8ZezEKC4nGds20Mr4dhbHoi2DhYWWVuTPCbuam8y0zkavANfgRnzjJmHsJ11S3FMJhLW6xTiqVJGlrXB24OXVbGV4ftpO12wQ-8kBrbGWHpzAv7GZyzCraUs5RBLdv5RnucNlDAYuMJPlox1HkEHxsiJ4XqR7RyMKME0MkXcnWjNwpuzt7lP2TUqZI-NCizJzRBPfiOZ7FFBYwRIaxyxzL-bcHtI1bDtcbByWryoujl6Wu3d6T1R6LM_XoIT8H_UmEd5A8P9S_FVTWK_LTVDvEjNwjzbSvX7Oh480xgD8NFI_geTMGBu1lpmjygwaIofOi8tH28jLgupSQHEoIpLcvLQRsBNim7D7mKe0H2tw82ruk7yYfnmlovu5yqPMKa4u2XwxtUJkkRd9OCEnhvPh-WYXkUVOeBNrsF5ffYSr_Yd4oyBYE8wg0vo2qo2a4erQoYIvAqfTc70FIRYV9EOHYhddJevVyxD4pKxgThTetmBJ51bAHyUESHB_Z56yyNgEyWMtorUmaHSPrf7-gEpHoMo6hXmCrfg4uZfEE4mA4DUsF9bwUVOdmAWCH2VHDl4_rEE36nwv0Rkno_LoD5iTdY0-nZogmndhs5Vvl9DbVUWr3DtzSnq3QpkAGNnmrTbBHC58qQF3CL0Y7BWV2Xs14GoZclzVKRa94QaVphOMSD_alysDncSjo0SEgynSoDQyZiss9j4nceWNLEmbBSHk0qoTVheuYNM4-XI2D9ndZFlcxDP-EbJJD4nr4_LKq9jGfXytdYD0TvONQMyo3HYKkK2ARl1_9xaIGUcqdUU7r5JgUsyrqoLGfi8UL_-fWGaKGlmrFzlo5FeLpg4Zi2B0GUbh5AS-OE_dfNPQfJYa7KLyOAuLeR0pQI2PNwxd3f1MTfRspmx0gH90BEil5t0Pssvn4eywoOa68xzt0axTkePd1SeZjvCIhskS1xrI=
```

**Example Response (verified case):**

```json
{
    "object": {
        "errMsg": null,
        "vcDetail": [
            {
                "id": "did:zid:acfe353cc72104161d7f61b94101eafeb96b011f10ae582b54825335efba48ca",
                "issuer": "did:zid:0d48916e8b0f10297b023b680fb7ab2367d5eb03a1a6bb876d63d1a9f5677dcb",
                "validFrom": "2025-01-01T00:00:00Z",
                "validUntil": "2026-01-01T00:00:00Z",
                "credentialSubject": {
                    "id": "did:zid:ba4f1fcf68831a5c689dfaa2195da1a3a7c37930228f886611f936fed0df66b9",
                    "passportMalaysia": {
                        "name": "Ali bin Abu",
                        "class": "B2",
                        "age": "21",
                        "gender": "Male"
                    }
                }
            }
        ],
        "verified": true
    },
    "messages": []
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
