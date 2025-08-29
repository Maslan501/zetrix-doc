# ZTP-20

## Simple Summary

A standard interface for tokens.

## Abstract

The following standard allows for the implementation of a standard API for tokens within smart contracts. This standard provides basic functionality to transfer tokens, as well as allow tokens to be approved so they can be spent by another on-chain third party.

## Motivation

A standard interface allows any tokens on Zetrix to be re-used by other applications: from wallets to decentralized exchanges.

## Specification Token Methods

#### name

Returns the name of the token - e.g. `"MyToken"`

OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.

```javascript
self.name = function () {
    let info = BasicOperationUtil.loadObj(CONTRACT_INFO);
    return info.name;
};
```

#### symbol

Returns the symbol of the token. E.g. `“MYTKN”`&#x20;

OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.

```javascript
self.symbol = function () {
    let info = BasicOperationUtil.loadObj(CONTRACT_INFO);
    return info.symbol;
};
```

#### decimals

Returns the number of decimals the token uses - e.g. `6`, means to divide the token amount by `1000000` to get its user representation.

```javascript
self.decimals = function () {
        let info = BasicOperationUtil.loadObj(CONTRACT_INFO);
        return info.decimals;
};
```

#### totalSupply

Returns the total token supply.

```javascript
self.totalSupply = function () {
    let info = BasicOperationUtil.loadObj(CONTRACT_INFO);
    let supply = info.supply;
    if (supply === false) {
        supply = "0";
    }
    return supply;
};
```

#### balanceOf

Returns the account balance of another account with address `account`.

```javascript
self.balanceOf = function (paramObj) {
    let balance = BasicOperationUtil.loadObj(BasicOperationUtil.getKey(BALANCES_PRE, paramObj.account));
    if (balance === false) {
        balance = "0";
    }
    return balance;
};
```

#### transfer

Transfers `value` amount of tokens to address `to`. The function SHOULD throw an exception if the message caller’s account balance does not have enough tokens to spend.

```javascript
self.transfer = function (paramObj) {
    _transfer(Chain.msg.sender, paramObj.to, paramObj.value);
    return true;
};
```

#### transferFrom

Transfers `value` amount of tokens from address `from` to address `to`.

The `transferFrom` method is used for a withdraw workflow, allowing contracts to transfer tokens on your behalf. This can be used for example to allow a contract to transfer tokens on your behalf and/or to charge fees in sub-currencies. The function SHOULD throw an exception unless the `from` account has deliberately authorized the sender of the message.

```javascript
self.transferFrom = function (paramObj) {
    let spender = Chain.msg.sender;
    self.p.spendAllowance(paramObj.from, spender, paramObj.value);
    _transfer(paramObj.from, paramObj.to, paramObj.value);
    return true;
};
```

#### approve

Allows `spender` to withdraw from your account multiple times, up to the `value` amount. If this function is called again it overwrites the current allowance with `value`.

```javascript
self.approve = function (paramObj) {
    self.p.approve(Chain.msg.sender, paramObj.spender, paramObj.value);
    return true;
};
```

#### allowance

Returns the amount which `spender` is still allowed to withdraw from `owner`.

```javascript
self.allowance = function (paramObj) {
    let allowance = BasicOperationUtil.loadObj(BasicOperationUtil.getKey(ALLOWANCES_PRE, paramObj.owner, paramObj.spender));
    if (allowance === false) {
        allowance = "0";
    }
    return allowance;
};
```

## [Zetrix Ecosystem Proposals](https://github.com/Zetrix-Chain/zetrix-protocol)

Sample ZTP20 contract can be found [here](https://github.com/Zetrix-Chain/zetrix-protocol/blob/main/ZTP/ZTP-20.js).
