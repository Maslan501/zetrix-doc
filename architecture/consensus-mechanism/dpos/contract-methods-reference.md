# Contract Methods Reference

The table below summarizes the contract methods and their operational role.

<table><thead><tr><th>Contract</th><th width="189.9090576171875">Method</th><th>Key Role</th><th>Trigger</th><th>Purpose</th></tr></thead><tbody><tr><td>DPOS Contract</td><td><code>setFreeze(freeze, validators[])</code></td><td>Admin key</td><td>Every 10s (freeze) + Every 1min (freeze or unfreeze)</td><td>Freeze or unfreeze validators</td></tr><tr><td>DPOS Contract</td><td><code>updateValidator()</code></td><td>Admin key</td><td>Every 10s</td><td>Rotate validator set</td></tr><tr><td>Heartbeat Contract</td><td><code>pushStatus({onlineList[], onlineTimestamp})</code></td><td>Operator key</td><td>Every 1 hour</td><td>Append online node set for that timestamp slot into contract storage</td></tr><tr><td>Heartbeat Contract</td><td><code>getNodeList({rate})</code></td><td>DPOS Contract (internal)</td><td>Daily 18:00, inside <code>extract()</code></td><td>Return validators that appeared in ≥rate% of stored hourly slots</td></tr><tr><td>Heartbeat Contract</td><td><code>clearRecord()</code></td><td>DPOS Contract (internal)</td><td>Daily 18:00, after <code>getNodeList()</code></td><td>Delete all timestamp records</td></tr><tr><td>DPOS Contract</td><td><code>extract()</code></td><td>Admin key</td><td>Daily 18:00</td><td>Query Heartbeat Contract, calculate reward distribution, wipe records</td></tr><tr><td>DPOS Contract</td><td><code>extractTransfer(list[])</code></td><td>Admin key</td><td>Daily 18:00</td><td>Distribute rewards to fund addresses</td></tr></tbody></table>

#### Contract interaction pattern

The DPOS contract is the decision and settlement contract. The Heartbeat contract is the historical evidence store used to derive online participation.

This separation is useful because it keeps monitoring evidence and reward execution loosely coupled while preserving on-chain verification of eligibility.
