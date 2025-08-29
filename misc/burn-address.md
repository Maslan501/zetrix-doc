# Burn Address

A burn address is a special account used to permanently remove tokens from circulation. Any token sent to this address becomes irretrievable, effectively reducing the total supply.

In the Zetrix blockchain, the designated burn account is preconfigured and consistent across both Mainnet and Testnet environments.

```
ZTX3Tvw4xiQMT7ennbXvw7L1i2TKP4nii3LYM
```

#### Burn Address Mechanism

In a normal account, the master weight must be more than or equal to the tx threshold. In this case, the master weight is 1 and the tx threshold is 1. Hence, the transaction can be made by using this account. Data shown below can be depicted from the account metadata in Explorer.

```
"priv": {
      "master_weight": 1,
      "thresholds": {
        "tx_threshold": 1
      }
}
```

However, for the burn account, the master weight is set to 0. Therefore, it does not comply with the requirement to exceed the tx threshold which is 1. Hence, the transaction will always fail when initiated by the burn account. When the master weight is 0, it is not shown in the metadata in Explorer.

```
"priv": {
      "thresholds": {
        "tx_threshold": 1
      }
}
```

