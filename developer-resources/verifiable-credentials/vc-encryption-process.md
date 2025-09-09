# VC Encryption Process

<figure><img src="../../.gitbook/assets/Encryption Colour 1.png" alt=""><figcaption></figcaption></figure>

## Apply VC Process

This process defines the secure exchange of data between a Holder and an Issuer in the Verifiable Credential (VC) system. The encryption model uses a **hybrid scheme**, combining asymmetric RSA encryption for key exchange and symmetric AES/GCM encryption (with HMAC-SHA256) for securing data. Additionally, a shared key is established using the **X25519 Diffie-Hellman key exchange protocol** to ensure end-to-end confidentiality between the Holder and Issuer.

**Steps Overview**

1. **ECDH Key Generation (Holder):**\
   The Holder generates an Elliptic Curve Diffie-Hellman (ECDH) key using the Issuer’s public key and the Holder’s private key.
2. **Application Data Encryption:**\
   The Holder encrypts the VC application data using AES with the derived ECDH key.
3. **ECDH Key Encryption (RSA):**\
   The Holder encrypts the ECDH key using the Issuer’s RSA public key for secure transmission.
4. **Submit Encrypted Data:**\
   The Holder sends the encrypted application data, encrypted ECDH key, signed data, metadata, public key, and X25519 public key to the MS Credential service via a `POST /vc/apply` request.
5. **ECDH Key Decryption (Server):**\
   The MS Credential service decrypts the ECDH key using its RSA private key.
6. **Application Data Decryption:**\
   Using the decrypted ECDH key, the service decrypts the encrypted application data.
7. **Template Metadata Validation:**\
   The service validates the metadata against the template to ensure integrity.
8. **Data Storage (Redis):**\
   The service stores the encrypted application data along with the Holder’s DID and X25519 public key in Redis as an APPLIED record.
9. **Issuer Fetch Request:**\
   The Issuer retrieves the Holder’s DID and X25519 public key by calling `GET /vc?vclId=`.
10. **ECDH Key Generation (Issuer):**\
    The Issuer generates its ECDH key using the Holder’s public key and its own private key to establish the shared secret.

<figure><img src="../../.gitbook/assets/Encryption Colour 2.png" alt=""><figcaption></figcaption></figure>

## Issue and Download VC Process&#x20;

This process defines how a Verifiable Credential (VC) is securely issued by the Issuer and later downloaded by the Holder. Like the Apply VC Process, this stage uses **hybrid encryption** combining RSA (for exchanging ECDH keys), AES/GCM with HMAC-SHA256 (for encrypting data), and Diffie-Hellman with **X25519** for shared key generation. It also introduces **ED25519** and **BBS+ signatures** for signing and verification.

### **Fetching and Key Setup**

1. The Issuer fetches the Holder’s **X25519 public key and DID** (`GET /holder?vcld=`).
2. The Issuer generates an ECDH key using the Holder’s public key and the Issuer’s private key.
3. Issuance data is encrypted using AES with the ECDH key.
4. The ECDH key itself is encrypted using the Holder’s RSA public key.

### **Sending Encrypted Issuance Data**

5. The Issuer sends the encrypted issuance data and encrypted ECDH key to the MS Credential service     (POST  `/vc/create`).
6. &#x20;The service decrypts the ECDH key with its RSA private key.
7. The service decrypts the issuance data with the ECDH key.
8. The service also decrypts the Holder’s earlier apply data.&#x20;
9. Metadata from Holder and Issuer are merged.&#x20;
10. The template metadata is validated.&#x20;
11. A VC blob is created and returned to the Issuer.

### **Signing and Submitting VC Data**

12. The MS Credential service updates the encrypted apply VC data as **PENDING\_SIGN**.&#x20;
13. The Issuer signs the blob with **ED25519** and **BBS+ keys**.&#x20;
14. The Issuer encrypts the post VC data using AES with the ECDH key.&#x20;
15. The Issuer encrypts the ECDH key using the Holder’s RSA public key.&#x20;
16. The Issuer submits encrypted VC data, including the VC ID, ED25519 public key, ED25519 signature,      BBS public key, BBS signature, expiry, and the encrypted ECDH key (`POST /vc/submit`).

### **Final VC Issuance**

17. MS Credential decrypts the ECDH key with its RSA private key.&#x20;
18. It decrypts the submitted VC data using AES with the ECDH key.&#x20;
19. It creates and verifies the final VC.&#x20;
20. The VC is encrypted again using AES with the ECDH key.&#x20;
21. The encrypted VC is stored in Redis as **ISSUED**.

### **Download by Holder**

21. The Holder downloads the encrypted VC using POST `/vc/download` with payload including VC ID, Issuer ID, and signed VC ID.&#x20;
22. The Holder generates the ECDH key using the Issuer’s public key and the Holder’s private key.&#x20;
23. The Holder decrypts the VC using AES with the derived ECDH key and stores it locally.



<figure><img src="../../.gitbook/assets/Encryption Colour 3.png" alt=""><figcaption></figcaption></figure>

## Create and Verify VP Process&#x20;

This process enables a Holder to generate a **Verifiable Presentation (VP)** from their issued **Verifiable Credential (VC)** and securely share it with a Verifier. It uses a combination of **RSA** for key exchang&#x65;**, AES/GCM** with **HMAC-SHA256** for encryption, **X25519 ECDH** for shared keys, and **ED25519** for signing.

### VP Creation by Holder

1. The Holder generates an ECDH key using their private key and the Verifier’s public key.
2. The Holder encrypts the VC using AES with the generated ECDH key.
3. The ECDH key is encrypted using the Verifier’s RSA public key.
4. The Holder sends the encrypted VC, reveal attributes, and range proof to MS Credential (`POST /vp/create`).

### Processing at MS Credential

5. MS Credential decrypts the ECDH key using its RSA private key.
6. MS Credential decrypts the VC using AES with the ECDH key.
7. MS Credential creates a blob and returns the blob to the Holder.

### Holder Preparing VP Submission

8. The Holder signs the blob with their ED25519 private key.
9. The Holder encrypts the ECDH key again using the Verifier’s RSA public key.
10. The Holder sends the encrypted VP, including `blobId`, signed blob, ED25519 public key, and encrypted ECDH key, to MS Credential (POST `/vp/submit`).

### Storing VP in Redis

11. MS Credential decrypts the ECDH key using its RSA private key.
12. MS Credential creates the VP and validates its parameters.
13. MS Credential encrypts the VP using AES with the ECDH key.
14. MS Credential stores the encrypted VP in Redis and returns a UUID to the Holder.

### Sharing with Verifier

15. The Holder shares the UUID and their public key in a QR Code with the Verifier.

### Verifier Verification Process

16. The Verifier generates an ECDH key using its private key and the Holder’s public key.
17. The Verifier encrypts the ECDH key using the Holder’s RSA public key.
18. The Verifier fetches the encrypted VP from Redis using the UUID.
19. The Verifier sends the VP data to the verification API with the UUID and encrypted ECDH key (POST `/vp/verify`).

### Final Verification by MS Credential

20. MS Credential fetches the encrypted VP from Redis.
21. MS Credential decrypts the encrypted VP using its RSA private key and the ECDH key.
22. MS Credential verifies the VP and returns the verification status to the Verifier.
