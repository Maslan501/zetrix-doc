# Reward Extraction Flow

The following sequence diagram shows the two-phase daily reward process.(Daily 18:00)

```mermaid
sequenceDiagram
    participant JOB as ValidatorExtractJob
    participant CACHE as Cache
    participant SVC as On-Chain Service
    participant BC as Blockchain Service
    participant CHAIN as DPOS Contract
    participant HB as Heartbeat Contract
    participant DB as Database

    JOB->>CACHE: Reset heartbeat statistics
    CACHE-->>JOB: Cache cleared

    Note over JOB,CHAIN: Phase 1 — Trigger On-Chain Reward Calculation

    participant API as Backend API
    participant NODE as Zetrix Node

    JOB->>SVC: doExtract()
    SVC->>BC: Generate extract() transaction blob
    BC->>API: Build extract() blob
    API-->>BC: blob
    BC-->>SVC: blob
    SVC->>SVC: Sign blob with admin key
    SVC->>BC: Submit signed transaction
    BC->>API: Submit TransactionEnv{extract()}
    API->>NODE: POST /submitTransaction
    NODE-->>API: tx hash
    API->>NODE: Poll getTransactionByHash() until COMPLETE
    NODE-->>API: tx confirmed
    API-->>BC: tx_hash + COMPLETE
    BC-->>SVC: tx_hash + COMPLETE
    Note over CHAIN,HB: extract() runs on-chain — getOnlineNodeList()
    CHAIN->>HB: contractQuery getNodeList(rate)
    Note right of HB: Count per-validator appearances across all stored timestamps.<br/>Include if appearances/total * 100 >= rate (e.g. 80%)
    HB-->>CHAIN: online validator address list
    CHAIN->>HB: clearRecord() — wipe all timestamp records
    Note right of CHAIN: calculate() distributes reward only to online validators,<br/>cross-referenced against validator candidates list
    SVC->>DB: Log tx hash + timestamp

    Note over JOB,CHAIN: Phase 2 — Distribute Rewards to Validator Accounts

    JOB->>SVC: doExtractTransfer()
    SVC->>NODE: GET validator fund addresses (DPOS contract metadata)
    NODE-->>SVC: validator fund address list

    loop For each batch of 10 validators
        SVC->>SVC: Wait for nonce increment
        SVC->>BC: Generate extractTransfer(batch) blob
        BC->>API: Build extractTransfer(list) blob
        API-->>BC: blob
        BC-->>SVC: blob
        SVC->>SVC: Sign blob with admin key
        SVC->>BC: Submit signed transaction
        BC->>API: Submit TransactionEnv{extractTransfer(list)}
        API->>NODE: POST /submitTransaction
        NODE-->>API: tx confirmed
        API-->>BC: tx_hash + COMPLETE
        BC-->>SVC: tx_hash + COMPLETE
        SVC->>DB: Log batch tx hash
        Note right of CHAIN: Contract transfers reward tokens<br/>directly to each validator fund address
    end

    SVC-->>JOB: All rewards distributed
    Note over JOB: Cycle complete. Next extraction at 18:00 tomorrow
```

The reward flow has two distinct phases.

**Phase 1: calculate eligibility and reward amounts**

The `extract()` call causes the DPOS contract to query the Heartbeat contract for the validators that met the configured online-rate threshold across the stored hourly snapshots.

After the eligible list is returned:

* reward calculation is performed on-chain
* only validators satisfying the heartbeat-based policy are considered eligible
* heartbeat records are cleared to prepare for the next cycle

**Phase 2: transfer rewards to fund addresses**

The `extractTransfer(list[])` calls distribute the already calculated rewards to validator fund addresses. Batching is used to control transaction size and nonce handling.

The two-minute delay between batches is operationally significant because it reduces nonce-collision risk when multiple transactions are submitted in sequence.
