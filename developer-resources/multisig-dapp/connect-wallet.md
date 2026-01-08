# Connect Wallet

### Function Purpose

The Connect Wallet feature is the entry point of the app. It links the DApp to your Zetrix wallet to enable:

* Account validation
* Secure transaction signing
* Identity recognition on-chain

### What Happens Technically

* DApp requests permissions to read wallet address
* Wallet confirms the user identity
* Application locks UI features until connected

### Expected Result

Once connected:

* Your wallet status becomes **Connected**
* Your address is recognized
* Multisig features become enabled

#### Common Issues

❗ Wallet not detected – ensure extension/mobile wallet installed\
❗ User cancels connection – app remains locked\
❗ Wrong network – app will notify mismatch
