# Mainet Release

### Release 1: Increase transaction queue

**Completion Date:** 4 August 2026\
**Status:** Completed

**Description:** This release increases transaction queue capacity and timeout limits to handle higher load, with the transaction timeout extended to 24 hours, the total queue limit raised to 100,000, and per-account transaction limit increased to 64,000. It also updates the system to Ledger version 1002 and Chain version 1.0.3.

**Release Info**

#### Changes:

* Increased `QUEUE_TRANSACTION_TIMEOUT` to 24 hours
* Increased `queue_limit` to 100000
* Increased `queue_per_account_txs_limit` to 64000

#### Update info

* Ledger version : 2002
* Chain version : 1.0.3

### Release 2: Disables multiQuery API and hardens the V8 contract runtime

**Completion Date:** 4 August 2026\
**Status:** Completed

**Description:** This release rolls out to mainnet the disabling of the multiQuery API, whose unbounded recursive calls could cause out-of-memory conditions and impact validator availability, together with the hardening of the V8 contract runtime against non-deterministic execution by removing Intl, disabling Function-constructor code generation, and undefining locale-sensitive String/Number prototype methods. It updates the system to Ledger version 2002 and Chain version 1.0.3.

**Mainnet Release Info**

#### Changes:

1. Disable multiQuery API (unbounded recursive calls could cause OOM and impact validator availability)
2. Harden V8 contract runtime against non-deterministic execution (remove Intl, disable Function-constructor code generation, undefine locale-sensitive String/Number prototype methods)

#### Update info

* Ledger version : 2002
* Chain version : 1.0.3
