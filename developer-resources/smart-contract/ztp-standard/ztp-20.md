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
const PERMIT_TYPEHASH = Utils.sha256("Permit(owner,spender,value,nonce,deadline)", 1);

function permit(owner, spender, value, deadline, p, s) {
  // 1. Basic input validation
  Utils.assert(Utils.addressCheck(owner) === true, "Arg-owner is not a valid address.");
  Utils.assert(Utils.addressCheck(spender) === true, "Arg-spender is not a valid address.");
  Utils.assert(Utils.stoI64Check(value) === true, "Arg-value must be alphanumeric.");
  Utils.assert(Utils.int64Compare(value, "0") >= 0, "Arg-value must be >= 0.");
  Utils.assert(Utils.stoI64Check(deadline) === true, "Arg-deadline must be alphanumeric.");
  Utils.assert(typeof p === "string" && p.length > 0, "Public key is required.");
  Utils.assert(typeof s === "string" && s.length > 0, "Signature is required.");
  Utils.assert(Utils.int64Compare(deadline, Chain.block.timestamp) >= 0, "Permit signature has expired.");

  // 2. Consume the owner's nonce (replay protection)
  let nonce = useNonce(owner);

  // 3. Domain separator — binds the signature to this contract on this chain
  //    chainId should be stored at init time (or via a one-time setChainId()) —
  //    see "Domain separator" note below.
  let domainSeparator = Utils.sha256(chainId + "|" + Chain.thisAddress, 1);

  // 4. Struct hash — the data being authorised, "|" delimited to remove all ambiguity
  let structHash = Utils.sha256(
    PERMIT_TYPEHASH + "|" +
    owner           + "|" +
    spender         + "|" +
    value           + "|" +
    nonce           + "|" +
    deadline,
    1
  );

  // 5. Final digest — domain applied at the outer layer, mirrors EIP-712
  let hash = Utils.sha256(domainSeparator + "|" + structHash, 1);

  // 6. Verify signature and signer identity
  let validateSign = Utils.ecVerify(s, p, hash);
  let validatePubKey = Utils.toAddress(p) === owner;
  Utils.assert(validateSign && validatePubKey, "Invalid permit signature.");

  // 7. Apply the approval
  let allowKey = makeAllowanceKey(owner, spender);
  Chain.store(allowKey, value);
  Chain.tlog("Approval", owner, spender, value);
  Chain.tlog("Permit", owner, spender, value, deadline);
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

**Hash construction (what the owner actually signs off-chain)**

```js
PERMIT_TYPEHASH  = sha256("Permit(owner,spender,value,nonce,deadline)")
domainSeparator  = sha256(chainId + "|" + contractAddress)
structHash       = sha256(PERMIT_TYPEHASH + "|" + owner + "|" + spender + "|" + value + "|" + nonce + "|" + deadline)
hash        
```

**Domain separator — chain ID storage**

`chainId` must be available in contract storage before `permit()` can compute a domain separator. Recommended pattern:

```javascript
// init() — required for new deployments
Utils.assert(paramObj.chainId === "5687162" || paramObj.chainId === "1635227727", "Invalid chain ID.");
// ... store paramObj.chainId as part of contract_info

// setChainId() — one-time, for contracts deployed before this change adopt it
function setChainId(chainId) {
  validateOwner();
  Utils.assert(chainId === "5687162" || chainId === "1635227727", "Invalid chain ID.");
  let contractInfo = JSON.parse(Chain.load(CONTRACT_PRE));
  Utils.assert(contractInfo.chainId === undefined, "Chain ID already set.");
  contractInfo.chainId = chainId;
  Chain.store(CONTRACT_PRE, JSON.stringify(contractInfo));
  Chain.tlog("ChainIdSet", chainId);
  return true;
}
```

| Network | Chain ID     |
| ------- | ------------ |
| Mainnet | `5687162`    |
| Testnet | `1635227727` |

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
        "p": "04bfc...e2a1",
        "s": "3045...0221"
    }
}
```

**`nonces(owner)` — query current nonce**

```javascript
function nonces(owner) {
  Utils.assert(Utils.addressCheck(owner) === true, "Arg-owner is not a valid address.");
  let nonceKey = makeNonceKey(owner);
  let nonce = Chain.load(nonceKey);
  return nonce === false ? "0" : nonce;
}
```

Callers must query this **before** constructing the off-chain hash, since `nonce` is part of `structHash` but is not a `permit()` parameter.

**Security Notes**

* **Front-running is expected, not a bug.** Anyone holding a valid `(p, s)` pair can submit it, this is inherent to EIP-2612-style meta-transactions. The outcome is always the value the owner signed; a front-runner cannot steal funds, only grief a combined `permit + transferFrom` flow by consuming the signature first. Integrators should check `allowance(owner, spender)` before calling `permit()` and skip it if already sufficient.
* **Signatures are single-use** because `nonce` is consumed on every successful `permit()` call, whether or not the caller supplied it, the contract reads it internally.
* **`deadline` must be validated against `Chain.block.timestamp`** before consuming the nonce or verifying the signature, so expired signatures fail fast.
* **Changing `chainId` invalidates all outstanding signatures** signed under the old value — treat it as a one-time, owner-only operation, not something toggled routinely.

***

## [Zetrix Ecosystem Proposals](https://github.com/Zetrix-Chain/zetrix-protocol)

***
