# Reward Extraction Flow

The following sequence diagram shows the two-phase daily reward process.(Daily 18:00)

```mermaid
sequenceDiagram
    participant JOB as ValidatorExtractJob
    participant REDIS as Redis Cache
    participant SVC as DposOnChainServiceImpl
    participant BC as BlockChainService
    participant CHAIN as DPOS Contract<br/>(ZTX3ePNZ...)
    participant HB as Heartbeat Contract<br/>(ZTX3T9M7...)
    participant DB as MySQL DposLog

    JOB->>REDIS: resetHeartbeatStatisticInCache()
    REDIS-->>JOB: Cache cleared

    Note over JOB,CHAIN: Phase 1 — Trigger On-Chain Reward Calculation

    participant CC as Cochain BE<br/>(:43300)
    participant NODE as Zetrix Node<br/>(:19333 HTTP)

    JOB->>SVC: doExtract()
    SVC->>BC: generateExtractBlob()
    BC->>CC: HTTP :43300 /tx/blob — build extract() blob
    CC-->>BC: blob {method:"extract", params:{}}
    BC-->>SVC: blob
    SVC->>SVC: Sign blob with extractKeyStore
    SVC->>BC: submitTx(blob, txHash, signBlob, publicKey)
    BC->>CC: HTTP :43300 /tx/submit — signed TransactionEnv{extract()}
    CC->>NODE: Zetrix SDK HTTP :19333 POST /submitTransaction
    NODE-->>CC: tx hash
    CC->>NODE: SDK poll getTransactionByHash() until COMPLETE
    NODE-->>CC: tx confirmed
    CC-->>BC: tx_hash + status=COMPLETE
    BC-->>SVC: tx_hash + status=COMPLETE
    Note over CHAIN,HB: extract() runs on-chain — getOnlineNodeList()
    CHAIN->>HB: contractQuery getNodeList({rate: monitor_rate})
    Note right of HB: count per-validator appearances across all stored timestamps<br/>include if appearances/total * 100 >= rate (e.g. 80%)
    HB-->>CHAIN: online validator address list
    CHAIN->>HB: payCoin clearRecord() — del all timestamp_node_{ts} keys, reset timestamp_list
    Note right of CHAIN: calculate() distributes reward<br/>only to online validators<br/>cross-ref against valCands list
    SVC->>DB: Log tx hash + timestamp

    Note over JOB,CHAIN: Phase 2 — Distribute Rewards to Validator Accounts

    JOB->>SVC: doExtractTransfer()
    SVC->>NODE: HTTP :19333 GET /getAccountMetaData (DPOS contract + validator_candidates key)<br/>VerifyNodeSearchServiceImpl.getAllValidatorFundAddress()
    NODE-->>SVC: validator fund address list

    loop For each batch of 10 validators
        SVC->>SVC: Sleep 2 minutes for nonce increment
        SVC->>BC: generateExtractTransferBlob(batch)
        BC->>CC: HTTP :43300 /tx/blob — build extractTransfer(list) blob
        CC-->>BC: blob {method:"extractTransfer", params:{list:[addr1..addr10]}}
        BC-->>SVC: blob
        SVC->>SVC: Sign blob with extractKeyStore
        SVC->>BC: submitTx(blob, txHash, signBlob, publicKey)
        BC->>CC: HTTP :43300 /tx/submit — signed TransactionEnv{extractTransfer(list)}
        CC->>NODE: Zetrix SDK HTTP :19333 POST /submitTransaction
        NODE-->>CC: tx confirmed
        CC-->>BC: tx_hash + status=COMPLETE
        BC-->>SVC: tx_hash + status=COMPLETE
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
