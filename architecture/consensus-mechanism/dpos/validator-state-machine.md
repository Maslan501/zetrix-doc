# Validator State Machine

The lifecycle is split into three operational time scales:

* short interval monitoring for validator safety and rotation,
* hourly aggregation for online-presence recording, and
* daily extraction for reward settlement.

This separation matters because not every operational decision is made from the same signal. Sequence lag drives freeze decisions, while accumulated heartbeat presence drives reward eligibility.

### Validator State Model

The validator state machine below captures the functional states used by the system.

<figure><img src="../../../.gitbook/assets/Wallet &#x26; Holdings — Sequence Diagram-2026-05-13-081208.png" alt=""><figcaption></figcaption></figure>

#### State semantics

**ACTIVE**

The validator is considered healthy and able to participate in block production. It may also accumulate heartbeat presence that contributes to reward qualification.

**FROZEN**

The validator has been marked abnormal, typically because it is lagging behind the expected chain sequence or failed a forecast-based health check. Frozen validators are excluded from normal participation until recovery conditions are met.

**REWARDED**

This is not a long-lived operational state in the same sense as ACTIVE or FROZEN. It represents the reward-settlement phase in which accumulated eligibility is materialized into token transfers.
