# Utility Tools

### Create New Cryptographic Keypair(**`zetrix_create_keypair`)**

Generate new key pair (testing only).

**Purpose:** Quick keypair generation for testing.

**Security:** Suitable for testing, use crypto tools for production.

**Recommendation:** Use `zetrix_crypto_generate_keypair` for production.

***

### Execute Multiple Blockchain Queries in a Single Request(**`zetrix_multi_query`)**

Execute multiple API queries simultaneously.

**Purpose:** Batch multiple blockchain queries efficiently.

**Advantages:**

* Parallel execution
* Reduced latency
* Single response

**Limits:** Maximum 10 queries per batch

**Performance:** 3-5x faster than sequential queries

***
