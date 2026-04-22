# ZTP-20

## Simple Summary

A standard interface for tokens.

## Abstract

The following standard allows for the implementation of a standard API for tokens within smart contracts. This standard provides basic functionality to transfer tokens, as well as allow tokens to be approved so they can be spent by another on-chain third party.

## Motivation

A standard interface allows any tokens on Zetrix to be re-used by other applications: from wallets to decentralized exchanges.

## Specification Token Methods

## Metadata

### name

Returns the name of the token - e.g. `"MyToken"`

OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.

```javascript
self.name = function(paramObj) {
    let name = ...;
    return name;
};
```

**Query Example**

To get the token name, use the following request and expect the response below:

**Request:**

```json
{
    "method": "name"
}
```

**Response:**

```json
{
    "name": "MyToken"
}
```

**Sample** `query` **Function:**

```javascript
function query(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "name") {
                ret = { "name": self.name(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

### symbol

Returns the symbol of the token. E.g. `“MYTKN”`

OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.

```javascript
self.symbol = function(paramObj) {
    let symbol = ...;
    return symbol;
};
```

**Query Example**

To get the token symbol, use the following request and expect the response below:

**Request:**

```json
{
    "method": "symbol"
}
```

**Response:**

```json
{
    "symbol": "MYTKN"
}
```

**Sample** `query` **Function:**

```javascript
function query(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "symbol") {
                ret = { "symbol": self.symbol(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

### decimals

Returns the number of decimals the token uses - e.g. `6`, means to divide the token amount by `1000000` to get its user representation.

```javascript
self.decimals = function(paramObj) {
    let decimals = ...;
    return decimals;
};
```

**Query Example**

To get the token decimals, use the following request and expect the response below:

**Request:**

```json
{
    "method": "decimals"
}
```

**Response:**

```json
{
    "decimals": "6"
}
```

**Sample** `query` **Function:**

```javascript
function query(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "decimals") {
                ret = { "decimals": self.decimals(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

## Core

### totalSupply

Returns the total token supply.

```javascript
self.totalSupply = function(paramObj) {
    let totalSupply = ...;
    return totalSupply;
};
```

**Query Example**

To get the total token supply, use the following request and expect the response below:

**Request:**

```json
{
    "method": "totalSupply"
}
```

**Response:**

```json
{
    "totalSupply": "1000"
}
```

**Sample** `query` **Function:**

```javascript
function query(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "totalSupply") {
                ret = { "totalSupply": self.totalSupply(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

### balanceOf

Returns the account balance of another account with address `account`.

```javascript
self.balanceOf = function(paramObj) {
    let balance = ...;
    return balance;
};
```

**Query Example**

To get the balance of an account, use the following request and expect the response below:

**Request:**

```json
{
    "method": "balanceOf",
    "params": { "address": "ZTX3abc123..." }
}
```

**Response:**

```json
{
    "balance": "500"
}
```

**Sample** `query` **function:**

```javascript
function query(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "balanceOf") {
                ret = { "balance": self.balanceOf(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

### transfer

Transfers `value` amount of tokens to address `to`. The function SHOULD throw an exception if the message caller’s account balance does not have enough tokens to spend.

```javascript
self.transfer = function(paramObj) {
    let transfer = ...;
    return transfer;
};
```

**Submit** `transfer` **Example**

To transfer tokens, use the following request and expect the response below:

**Request:**

```json
{
    "method": "transfer",
    "params": { "to": "ZTX3to...", "value": "100" }
}
```

**Response:**

```json
{
   "results" : [
      {
         "error_code" : 0,
         "error_desc" : "",
         "hash" : "caa80.......395f9"
      }
   ],
   "success_count" : 1
}
```

**Sample** `main` **Function:**

```javascript
function main(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "transfer") {
                ret = { "transfer": self.transfer(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

### transferFrom

Transfers `value` amount of tokens from address `from` to address `to`.

The `transferFrom` method is used for a withdraw workflow, allowing contracts to transfer tokens on your behalf. This can be used for example to allow a contract to transfer tokens on your behalf and/or to charge fees in sub-currencies. The function SHOULD throw an exception unless the `from` account has deliberately authorized the sender of the message.

```javascript
self.transferFrom = function(paramObj) {
    let transferFrom = ...;
    return transferFrom;
};
```

**Submit** `transferFrom` **Example**

To transfer tokens from another account, use the following request and expect the response below:

**Request:**

```json
{
    "method": "transferFrom",
    "params": { "from": "ZTX3from...", "to": "ZTX3to...", "value": "100" }
}
```

**Response:**

```json
{
   "results" : [
      {
         "error_code" : 0,
         "error_desc" : "",
         "hash" : "caa80.......395f9"
      }
   ],
   "success_count" : 1
}
```

**Sample** `main` **Function:**

```javascript
function main(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "transferFrom") {
                ret = { "transferFrom": self.transferFrom(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

### approve

Allows `spender` to withdraw from your account multiple times, up to the `value` amount. If this function is called again it overwrites the current allowance with `value`.

```javascript
self.approve = function(paramObj) {
    let approve = ...;
    return approve ;
};
```

**Submit** `approve` **Example**

To approve a spender, use the following request and expect the response below:

**Request:**

```json
{
    "method": "approve",
    "params": { "spender": "ZTX3spender...", "value": "100" }
}
```

**Response:**

```json
{
   "results" : [
      {
         "error_code" : 0,
         "error_desc" : "",
         "hash" : "caa80.......395f9"
      }
   ],
   "success_count" : 1
}
```

**Sample** `main` **Function:**

```javascript
function main(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "approve") {
                ret = { "approve": self.approve(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

### allowance

Returns the amount which `spender` is still allowed to withdraw from `owner`.

```javascript
self.allowance = function(paramObj) {
    let allowance = ...;
    return allowance;
};
```

**Query** `allowance` **Example**

To check the allowance for a spender, use the following request and expect the response below:

**Request:**

```json
{
    "method": "allowance",
    "params": { "owner": "ZTX3owner...", "spender": "ZTX3spender..." }
}
```

**Response:**

```json
{
    "allowance": "200"
}
```

**Sample** `main` **Function:**

```javascript
function main(input_str) {
        let inputObj = JSON.parse(input_str);
        let ret = {};
        if (inputObj.method === "allowance") {
                ret = { "allowance": self.allowance(inputObj.params) };
        }
        return JSON.stringify(ret);
}
```

## Extension

### Permit

The `permit` function enables **gasless approvals** for ZTP20 tokens by allowing an owner to approve a spender via an **off-chain signature**, instead of an on-chain `approve` transaction. This design is inspired by **EIP-2612**, adapted for the Zetrix / ZTP20 execution environment and cryptographic primitives.

The `permit` mechanism allows a token holder to approve an allowance via an off-chain signature, which may subsequently be submitted on-chain by any relayer that pays the associated transaction fee.

### Function Signature

```javascript
function permit(owner, spender, value, deadline, p, s){
  let nonce = useNonce(owner);
  let hash = Utils.sha256(PERMIT_TYPEHASH + owner + spender + value + nonce.toString() + deadline, 1);
  let validateSign = Utils.ecVerify(s, p, hash);
  let validatePubKey = Utils.toAddress(p) === owner;
  let allowKey = makeAllowanceKey(owner, spender);
  Chain.store(allowKey, value);
  Chain.tlog('Approval', owner, spender, value);
  Chain.tlog('Permit', owner, spender, value, deadline);
  return true;
}
```

### Parameters

| Parameter  | Type             | Description                         |
| ---------- | ---------------- | ----------------------------------- |
| `owner`    | `address`        | Token holder granting the allowance |
| `spender`  | `address`        | Address receiving spending rights   |
| `value`    | `int64 (string)` | Allowance amount to approve         |
| `deadline` | `int64 (string)` | Expiration timestamp for the permit |
| `p`        | `bytes`          | Public key of the signer            |
| `s`        | `bytes`          | Signature generated by the owner    |

**Invoke Example**

The `permit` function is a main contract function (transaction/invoke), not a query. It is used to submit a transaction that enables gasless approval for a spender using an off-chain signature.

**Sample** `main` **function call:**

```javascript
if (input.method === 'permit') {
        permit(
                input.params.owner,
                input.params.spender,
                input.params.value,
                input.params.deadline,
                input.params.p,
                input.params.s
        );
}
```

**Sample request:**

```json
{
    "method": "permit",
    "params": {
        "owner": "ZTX3WeinXtt28YMyr4vUZ14ddTgEMGeuc1e6b",
        "spender": "ZTX3MVqzyhcYZJBnLa9do5ZqzHEUtT8KdhCsT",
        "value": "1000",
        "deadline": "1680000000",
        "p": "04bfc...e2a1",  // public key (hex)
        "s": "3045...0221"   // signature (hex)
    }
}
```

**Sample response:**

```json
{
   "results" : [
      {
         "error_code" : 0,
         "error_desc" : "",
         "hash" : "caa80.......395f9"
      }
   ],
   "success_count" : 1
}
```

## [Zetrix Ecosystem Proposals](https://github.com/Zetrix-Chain/zetrix-protocol)

***
