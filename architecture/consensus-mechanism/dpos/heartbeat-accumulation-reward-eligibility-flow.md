# Heartbeat Accumulation → Reward Eligibility Flow

The following diagram shows how heartbeat signals become reward eligibility input.

```mermaid
flowchart LR
    subgraph SG_NODE["Zetrix Node (monitor :19336 — Conn 2)"]
        PING["Node sends WS PING every 15s\nconnect_time_out_(60s) / 4\nnetwork.cpp NeedPing()"]
        SYS["Node responds with Monitor.SystemStatus\n(cpu · memory · discs)"]
    end

    subgraph SG_DC["BaaS Data Center"]
        PONG["IoServiceImpl.nodePong()\ncount PINGs — every N=3 PINGs (~45s)\nsend MONITOR_MSGTYPE_SYSTEM (type 34) request"]
        RCV["SystemAction.system()\nParse Monitor.SystemStatus"]
        HBCNT["SystemAction.doStatisticValidatorHeartbeat()\nredisUtil.incr(validatorAddress)\nkey = validator address string"]
    end

    subgraph SG_HR["Every 1 Hour"]
        STAT["ValidatorsHeartStatisticJob"]
        FILTER{"Count >= 47?"}
        ONLINE["Add to onlineList"]
        SKIP["Exclude from onlineList"]
        PUSH["DposOnChainServiceImpl.doPushStatus()\nBC.generatePushStatusBlob() then BC.submitTx()\nHTTP :43300 CoChainTxService (Feign)\npushStatus({onlineList, onlineTimestamp})\nstatisticMonitorKeyStore (operator-gated)"]
        DBINS["Insert HeartbeatStatistic\nrow in DB"]
        RESET["Reset Redis counters"]
    end

    subgraph SG_HB["Heartbeat Contract (ZTX3T9M7...)"]
        HB_STORE["timestamp_list: {ts→0, ...}\ntimestamp_node_{ts}: {addr→0, ...}\nunion-merged per onlineTimestamp slot"]
    end

    subgraph SG_DAY["Daily 18:00"]
        EXTRACT["DPOS extract()\ngetNodeList({rate: monitor_rate=80})\n→ keep validators in ≥80% of hourly slots\npayCoin clearRecord() → wipe all records\ncalculate() reward distribution"]
        TRANSFER["DPOS extractTransfer()\nChain.payCoin() to fund addresses"]
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

Eligibility is determined in two stages.

**Stage 1: hourly inclusion**

Within each hour, a validator must accumulate at least 47 heartbeat hits in Redis to be included in that hour's online list.

This is a local operational threshold used before any on-chain reward logic is applied.

**Stage 2: cross-hour qualification**

During `extract(),` the Heartbeat contract evaluates how often each validator appeared across stored hourly slots. A validator qualifies only if it appears in at least the configured percentage of those slots, for example 80%.

This means hourly inclusion alone does not guarantee reward. Reward depends on sustained presence across the full aggregation period.
