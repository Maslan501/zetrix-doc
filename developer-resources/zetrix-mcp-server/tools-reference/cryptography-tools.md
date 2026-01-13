# Cryptography Tools

Official encryption library powered by [zetrix-encryption-nodejs](https://github.com/Zetrix-Chain/zetrix-encryption-nodejs).

### Generate New Keypair(**`zetrix_crypto_generate_keypair`)**

Generate new key pair with address.

**Purpose:** Create cryptographically secure keypair for new accounts.

**Algorithm:** Ed25519 (Edwards-curve Digital Signature Algorithm)

**Returns:**

```json
{
  "privateKey": "priv...",
  "publicKey": "b001...",
  "address": "ZTX..."
}
```

**Key Length:** 256-bit private key, 256-bit public key

**Security:** Industry-standard algorithm used by cryptocurrencies worldwide

**Performance:** Generates keypair in <10ms

**Offline:** Completely offline operation, no network required

***

### Public Key(**`zetrix_crypto_get_public_key`)**

Derive public key from private key.

**Purpose:** Calculate corresponding public key from private key.

**Algorithm:** Scalar multiplication on Ed25519 curve

**Mathematics:** P = s\*G where P is public key, s is private key, G is generator

**Deterministic:** Same private key always produces same public key

**Performance:** <5ms computation time

***

### Account Address(**`zetrix_crypto_get_address`)**

Get Zetrix address from public key.

**Purpose:** Derive Zetrix address from public key.

**Process:**

1. PublicKey → SHA-256 hash
2. → RIPEMD-160
3. → Checksum
4. → Base58 encoding

**Format:** Starts with "ZTX", 35 characters total

**Checksum:** Built-in error detection prevents typos

**One-way:** Cannot reverse address to get public key

***

### Validate Key Format & Integrity(**`zetrix_crypto_validate_key`)**

Validate key or address format.

**Purpose:** Check if key/address string is correctly formatted.

**Validates:**

* Format
* Prefix
* Length
* Encoding
* Checksum

**Checks Performed:**

* Private key: "priv" prefix, 64 hex chars, valid curve point
* Public key: "b001" prefix, valid encoding
* Address: "ZTX" prefix, 35 chars, valid Base58, checksum

**Performance:** <1ms validation time

***

### Generate Digital Signature(**`zetrix_crypto_sign`)**

Sign a message with private key.

**Purpose:** Create cryptographic signature proving message authenticity.

**Algorithm:** Ed25519 signature scheme (EdDSA)

**Output:** 64-byte signature (128 hex characters)

**Security:** Signature proves private key holder approved the message

**Properties:**

* Deterministic
* Non-repudiable
* Verifiable

**Performance:** \~0.5ms per signature

***

### Verify Digital Signature(**`zetrix_crypto_verify`)**

Verify signature against message.

**Purpose:** Confirm signature was created by claimed private key.

**Input:**

* Message
* Signature
* Public key

**Output:** Boolean (valid/invalid)

**Security:** Mathematically impossible to forge valid signature without private key

**Performance:** \~1ms per verification (slightly slower than signing)

***

### Encrypt Private Key(**`zetrix_crypto_encrypt_key`)**

Encrypt private key with password.

**Purpose:** Secure private key storage using password-based encryption.

**Algorithm:** AES-256-CBC (Advanced Encryption Standard)

**Key Derivation:** PBKDF2 with 10,000+ iterations and random salt

**Output:** Encrypted key + salt + IV in single string

**Password Requirements:**

* Minimum 12 characters
* Mixed case (upper and lower)
* Include numbers and symbols
* Avoid common words

**Performance:** \~100-200ms (intentionally slow for security)

***

### Decrypt Private Key(**`zetrix_crypto_decrypt_key`)**

Decrypt encrypted private key with password.

**Purpose:** Recover private key from encrypted storage.

**Process:**

1. Extract salt/IV
2. Derive key with PBKDF2
3. Decrypt with AES-256

**Validation:** Automatically verifies decryption succeeded

**Errors:** Returns error if password incorrect or data corrupted

**Performance:** \~100-200ms (same as encryption)

***
