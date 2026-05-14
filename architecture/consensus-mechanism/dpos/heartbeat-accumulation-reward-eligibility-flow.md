# Heartbeat Accumulation → Reward Eligibility Flow

The following diagram shows how heartbeat signals become reward eligibility input.

```mermaid
flowchart LR
    subgraph SG_NODE["Zetrix Node (Monitor Connection)"]
        PING["Node sends WebSocket PING every ~15s"]
        SYS["Node responds with system status<br/>(cpu · memory · disk)"]
    end

    subgraph SG_DC["BaaS Data Center"]
        PONG["Receive PING<br/>Every 3 PINGs (~45s)<br/>Request system status"]
        RCV["Parse system status response"]
        HBCNT["Increment heartbeat counter in cache<br/>key = validator address"]
    end

    subgraph SG_HR["Every 1 Hour"]
        STAT["ValidatorsHeartStatisticJob"]
        FILTER{"Count >= 47?"}
        ONLINE["Add to onlineList"]
        SKIP["Exclude from onlineList"]
        PUSH["Call pushStatus(onlineList, onlineTimestamp)<br/>on Heartbeat Contract<br/>via operator key"]
        DBINS["Insert heartbeat statistic record"]
        RESET["Reset cache counters"]
    end

    subgraph SG_HB["Heartbeat Contract"]
        HB_STORE["Stores per-timestamp validator sets<br/>Each hourly push appended to contract storage"]
    end

    subgraph SG_DAY["Daily 18:00"]
        EXTRACT["DPOS extract()<br/>getNodeList(rate = 80%)<br/>Keep validators in >= 80% of hourly slots<br/>clearRecord() wipes all records<br/>calculate() distributes reward"]
        TRANSFER["DPOS extractTransfer()<br/>Transfer rewards to fund addresses"]
    end

    PING --> PONG --> SYS --> RCV --> HBCNT
    HBCNT --> STAT
    STAT --> FILTER
    FILTER -->|Yes| ONLINE --> PUSH
    FILTER -->|No| SKIP
    PUSH --> HB_STORE
    PUSH --> DBINS --> RESET
    HB_STORE -.->|"daily contractQuery"| EXTRACT
    EXTRACT --> TRANSFER
```

#### Eligibility model explained

Eligibility is determined in two stages.

**Stage 1: hourly inclusion**

A validator must accumulate at least 47 heartbeat hits within the hour to appear in that hour's online list.

**Stage 2: cross-hour qualification**

During extract(), the Heartbeat contract checks how often each validator appeared across the stored hourly snapshots. A validator must appear in at least 80% of those slots to qualify for daily reward.
