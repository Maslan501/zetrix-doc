# Key Configuration

| Property                               | Value                                   | Purpose                                                                                                                                                                                                                                             |
| -------------------------------------- | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `contract.dpos.address`                | `ZTX3ePNZQhndgGzKLmg1SFfno3N42mLhPYJMN` | DPOS smart contract                                                                                                                                                                                                                                 |
| `contract.heartbeat.statistic.address` | `ZTX3T9M7YJ5v8VU1i37oDcrcie376cfki9NWV` | Heartbeat contract                                                                                                                                                                                                                                  |
| `statistic.seq.threshold`              | `2`                                     | Max block lag before freeze                                                                                                                                                                                                                         |
| `statistic.heartbeat.threshold`        | `47`                                    | Stage 1 — BaaS DC: min Redis heartbeat hits/hour to include validator in `onlineList` sent to `pushStatus`. Math: PING every 15s → `redisUtil.incr` every 3 PINGs (\~45s) → \~80 max increments/hr. 47/80 ≈ 58.75% uptime required within the hour. |
| `monitor_rate` (DPOS contract config)  | `80`                                    | Stage 2 — Heartbeat Contract: min % of hourly push slots a validator must appear in to qualify for reward                                                                                                                                           |
| `dpos.forecast.seq.height`             | `3`                                     | Number of blocks to forecast ahead                                                                                                                                                                                                                  |
| `validator.not.freeze.admin.list`      | (addresses)                             | Protected validators — never frozen                                                                                                                                                                                                                 |
| `validator.freeze.enable`              | `true`                                  | Master switch for freeze operations                                                                                                                                                                                                                 |
| `seq.close.time.threshold`             | `60s`                                   | Block time gap indicating missed block                                                                                                                                                                                                              |

The heartbeat threshold uses a two-layer interpretation.

* Local hourly threshold: 47 hits in Redis within an hour.
* On-chain daily threshold: appearance in at least 80% of hourly snapshots.

Given a PING every 15 seconds and a Redis increment every third PING, the system can produce around 80 increments per hour. Therefore:

$$
\frac{47}{80} \approx 58.75\%
$$

This means a validator must be present for roughly 58.75% of the hour to enter the hourly online list, and then still satisfy the higher daily multi-slot threshold for reward.

### Operational Notes

#### Freeze policy

Freeze decisions are not based on a single signal source. The system can freeze validators due to forecast-based risk or due to actual sequence lag seen during the reconciliation job.

#### Protected validators

The admin exclusion list prevents certain validators from being frozen automatically. This is an explicit policy exception and should be reviewed carefully because it changes the behavior of automated health enforcement.

#### Reward qualification is not equivalent to active status

A validator may move between ACTIVE and FROZEN during the cycle yet still qualify for reward if its hourly online participation satisfies the contract threshold for the relevant period.

#### Record clearing is part of settlement

The heartbeat contract clears its stored timestamp records after extract() consumes them. That means the monitoring dataset is intentionally cyclical and aligned to the reward interval.

#### Batching and nonce handling

The extractTransfer(list\[]) phase uses batches of 10 validators and waits two minutes between batches. This indicates that operational safety around transaction sequencing is a deliberate part of the design.

### Summary

Zetrix DPOS operations are built around a layered control model:

* fast validator health monitoring,
* periodic online-state recording,
* contract-based reward qualification, and
* batched daily reward transfer.

The design separates monitoring evidence, validator state enforcement, and reward settlement while still connecting them through well-defined contract calls and scheduled jobs.
