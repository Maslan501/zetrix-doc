# Wallet Integration

In this section, we will provide sample snippets for you to use to connect to your dApp on Zetrix. Here we provide support for both browser wallet extension connection (Chromium-based browsers only) as well as mobile wallet connection.

## Browser wallet extension connection

To use the following snippet, you will have to first install the Zetrix Chrome extension wallet [here](https://www.zetrix.com/zetrix-wallet/). Once you have installed and created (or import) an account, you can initiate connection from your dApp to the browser extension wallet using the `zetrix` object within the `window` global object. The following snippet can be used:

```javascript
window.zetrix.authorize(
  { method: "changeAccounts" }, 
  (resp) => {
    if (resp.code === 0) {
      window.zetrix.authorize(
        { method: "sendRandom", param: { random: "blob" } }, 
        (resAuth) => {
          if (resAuth.code === 0) {
            // Retrieve the necessary info from resp.data and resAuth.data to retrieve the address, signData & publicKey
            sessionStorage.setItem("zetrixAccount", resp.data.address);
            sessionStorage.setItem("isLogin", "true");
          }
        }
      );
    } 
  }
);
```

## Mobile wallet connection

For your dApp to support Zetrix mobile wallet connection, you will have to install the [zetrix-connect-wallet-sdk](https://www.npmjs.com/package/zetrix-connect-wallet-sdk) into your project. In order to test your code, you will have to install the [Zetrix mobile wallet](https://www.zetrix.com/zetrix-wallet/) which are available on both [iOS](https://apps.apple.com/my/app/zetrix-wallet/id1660515587) and [Android](https://play.google.com/store/apps/details?id=com.zetrix.wallet\&pli=1). For mobile wallet connection to your dApp, the following piece of snippet can be referred (the following snippet are pieces of code written in Vue.js as part of the dApp):

```javascript
// Import the object from the zetrix-connect-wallet-sdk you have installed
import ZetrixWalletConnect from "zetrix-connect-wallet-sdk";

/* Your code in your JavaScript framework of choice */

// Initialize an instance of the object along with the options
this.zetrixWalletConnect = new ZetrixWalletConnect({
  bridge: 'wss://test-wscw.zetrix.com', // testnet websocket server 
  callMode: 'web', // 'web' for browser pages; use 'webView' when inside Zetrix webview
  qrcode: true, // recommended for PC so the SDK can generate a QR for scanning
  appType: 'zetrix' // pixa OR myid OR muma
});

// Use the connect() function to connect to your mobile wallet
this.zetrixWalletConnect
  .connect()
  .then((res) => {
    this.zetrixWalletConnect
      .auth()
      .then((res) => {
        // Handle the response. The following are some sample code to handle the response:
        sessionStorage.setItem("zetrixAccount", res.data.address);
        sessionStorage.setItem("isLogin", this.isLogin);
        window.location.href = "/";
      })
      .catch(() => {
        return false;
      });
  })
  .catch(() => {
    return false;
  });


```

For a complete and latest documented coverage of the functionalities, please refer to the [zetrix-connect-wallet-sdk](https://www.npmjs.com/package/zetrix-connect-wallet-sdk) package documentation.&#x20;
