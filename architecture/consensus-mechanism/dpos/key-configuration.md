# Key Configuration

| Parameter             | Value                  | Purpose                                                                                                                                                                                  |
| --------------------- | ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Block lag threshold   | 2 blocks               | Maximum block sequence lag before a validator is frozen                                                                                                                                  |
| Heartbeat threshold   | 47 hits/hour           | Minimum cache heartbeat increments per hour to qualify as online. Math: PING every 15s, incremented every 3 PINGs (\~45s) → \~80 max/hr. 47/80 ≈ 58.75% uptime required within the hour. |
| Online rate threshold | 80%                    | Minimum percentage of hourly push slots a validator must appear in to qualify for daily reward                                                                                           |
| Forecast depth        | 3 blocks               | Number of blocks forecasted ahead for early freeze detection                                                                                                                             |
| Admin validator list  | (configured addresses) | Protected validators that are never frozen                                                                                                                                               |
| Freeze master switch  | configurable           | Enables or disables all freeze operations system-wide                                                                                                                                    |

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
