# DPOS Lifecycle Flow

This chapter explains how Zetrix Delegated Proof of Stake (DPOS) operates from an application and contract-integration perspective.

* how validator rotation is triggered,
* how freeze and unfreeze decisions are made,
* how heartbeat statistics are accumulated and stored,
* how reward eligibility is derived, and
* how daily reward extraction and transfer are executed.

### System Overview

Zetrix uses Delegated Proof of Stake (DPOS). Validators are elected to produce blocks in rotation. Around that core block-production model, the platform runs a continuous operational loop that:

* monitors validator health,
* freezes lagging validators,
* restores recovered validators,
* records hourly online-validator status, and
* distributes rewards once per day.

The model operates on three time scales:

* every 10 seconds for rotation and forecast-based protection,
* every 1 minute for local sequence reconciliation,
* every 1 hour for heartbeat aggregation, and
* daily at 18:00 for reward settlement.

## End-to-End DPOS Lifecycle

The following diagram shows the full lifecycle from startup through continuous monitoring, hourly statistics, and daily reward extraction.

````mermaid
flowchart TD
    START([System Startup]) --> INIT_NODE

    subgraph SG_INIT["Initialisation"]
        INIT_NODE["Load keystores<br/>Load contracts<br/>Load admin validator list"]
    end

    SG_INIT --> SG_CYCLE

    subgraph SG_CYCLE["Continuous Monitoring Cycle"]

        subgraph T30["Every 10 seconds — ValidatorsRotateJob"]
            R1["Get DPOS config on-chain<br/>validator slice count + rotate interval"]
            R2["Get current max block sequence"]
            R3{"currentSeq ><br/>sliceCount + rotate?"}
            R4["Identify freeze candidates<br/>freezeToActive list"]
            R5["Call updateValidator()<br/>on DPOS Contract"]
            R6["Log rotation event"]
            R1 --> R2 --> R3
            R3 -->|No| R_SKIP["Skip rotation"]
            R3 -->|Yes| R4 --> R5 --> R6
        end

        subgraph T1A["Every 10 seconds — ValidatorsForecastCheckJob"]
            FA1["Get current max seq<br/>from blockchain"]
            FA2["Get active validators list<br/>from ledger"]
            FA3["Calculate next 3 forecasted<br/>producers via viewNum mod count"]
            FA4{"Forecasted validator<br/>local seq lagging<br/>> 2 blocks?"}
            FA5["Mark as ABNORMAL<br/>Add to freeze list"]
            FA6["Trigger freeze<br/>ABNORMAL status"]
            FA7["Call setFreeze(true)<br/>on DPOS Contract<br/>skip admin addresses"]
            FA8["Log freeze event"]
            FA1 --> FA2 --> FA3 --> FA4
            FA4 -->|No| FA_OK["Validator healthy<br/>no action"]
            FA4 -->|Yes| FA5 --> FA6 --> FA7 --> FA8
        end

        subgraph T1B["Every 1 min — ValidatorsLocalSeqCheckJob"]
            LS1["Query all validators<br/>local seq from DB"]
            LS2["Query on-chain seq<br/>from blockchain"]
            LS3{"Frozen validator<br/>seq now current?"}
            LS4["Mark NORMAL<br/>trigger unfreeze"]
            LS5{"Active validator<br/>seq lag > 2 blocks?"}
            LS6["Mark ABNORMAL<br/>trigger freeze"]
            LS7["Call setFreeze(freeze)<br/>on DPOS Contract"]
            LS8["Log state change"]
            LS1 --> LS2 --> LS3
            LS3 -->|Yes| LS4 --> LS7
            LS3 -->|No| LS5
            LS5 -->|Yes| LS6 --> LS7
            LS5 -->|No| LS_OK["No change"]
            LS7 --> LS8
        end
    end

    SG_CYCLE --> SG_HOURLY

    subgraph SG_HOURLY["Every 1 Hour — ValidatorsHeartStatisticJob"]
        H1["Query all configured<br/>validator addresses"]
        H2["Read heartbeat counts<br/>from cache"]
        H3{"Count >= threshold<br/>47 beats?"}
        H4["Add to onlineValidatorList"]
        H5["Call pushStatus(onlineList, onlineTimestamp)<br/>on Heartbeat Contract<br/>via operator key"]
        H6["Insert heartbeat statistic record"]
        H7["Reset heartbeat counters<br/>for next hour"]
        H1 --> H2 --> H3
        H3 -->|Yes| H4 --> H5
        H3 -->|No| H3_SKIP["Validator excluded<br/>from online list"]
        H5 --> H6 --> H7
    end

    SG_HOURLY --> SG_DAILY

    subgraph SG_DAILY["Daily 18:00 — ValidatorExtractJob"]
        D1["Reset heartbeat stats<br/>in cache"]
        D2["Call extract()<br/>via admin key"]
        D3["Submit extract tx to DPOS contract<br/>On-chain reward calculation triggered"]
        D4["Call extractTransfer()<br/>via admin key"]
        D5["Fetch all validator<br/>fund addresses from chain"]
        D6["Partition into batches of 10"]
        D7["Submit extractTransfer tx<br/>per batch"]
        D8["Wait between batches<br/>for nonce management"]
        D9["Rewards transferred<br/>to validator accounts"]
        D10["Log all tx hashes"]
        D1 --> D2 --> D3 --> D4 --> D5 --> D6 --> D7 --> D8
        D8 -->|next batch| D7
        D7 -->|All batches done| D9 --> D10
    end

    D10 -.->|"Next day — loop continues"| SG_CYCLE
```
````

#### Lifecycle explanation

The DPOS lifecycle is split into continuous safety checks, hourly evidence collection, and daily reward settlement. Sequence lag drives operational freeze decisions, while heartbeat inclusion drives reward eligibility.

Operational Jobs

#### `ValidatorsRotateJob`

Frequency: every 10 seconds

Purpose: rotate validator participation when the configured block slice and rotation interval require it.

Behavior summary:

* read DPOS configuration from chain,
* read the latest block sequence,
* decide whether the rotation threshold has been reached,
* identify candidates that can move from freeze-related state back to active participation,
* call updateValidator(), and
* log the rotation event.

#### `ValidatorsForecastCheckJob`

Frequency: every 10 seconds

Purpose: forecast upcoming producers and freeze validators that are already lagging.

Behavior summary:

* read current chain sequence,
* read the active validator list,
* calculate the next three expected producers,
* compare projected validators against local sequence progress,
* mark lagging validators as abnormal,
* skip protected admin validators, and
* call setFreeze(true) for the affected validators.

#### `ValidatorsLocalSeqCheckJob`

Frequency: every 1 minute

Purpose: reconcile validator state using current local and on-chain sequence numbers.

Behavior summary:

* read local validator sequence values,
* compare them with the latest on-chain values,
* restore frozen validators that have caught up,
* freeze active validators that exceed the lag threshold, and
* log any resulting state changes.

#### `ValidatorsHeartStatisticJob`

Frequency: every 1 hour

Purpose: convert raw heartbeat counts into an hourly online-validator snapshot.

Behavior summary:

* read configured validator addresses,
* read heartbeat counts from cache,
* check whether each validator reaches the hourly threshold,
* build the online validator list,
* push that list with the timestamp to the Heartbeat contract,
* persist the heartbeat statistic record, and
* reset counters for the next hour.

#### `ValidatorExtractJob`

Frequency: daily at 18:00

Purpose: finalize the daily reward cycle.

Behavior summary:

* reset heartbeat statistics in cache,
* call extract(),
* trigger on-chain reward calculation,
* read validator fund addresses,
* batch them in groups of 10,
* call extractTransfer() for each batch, and
* log the resulting transaction hashes.
