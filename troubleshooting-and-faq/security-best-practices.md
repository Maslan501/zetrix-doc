# Security Best Practices

Securing smart contracts is critical, as any vulnerabilities can result in substantial financial loss, data breaches, and a decline in user confidence. When building JavaScript-based smart contracts on Zetrix or any compatible platform it is essential to follow established security best practices. The following provides a detailed overview of these key practices.

### 1. **Understand the Smart Contract Environment**

Before beginning to code, it's essential to gain a clear understanding of the blockchain environment in which you're developing:

* Blockchain Specifics: Familiarize yourself with the Zetrix blockchain’s architecture, consensus mechanism (DPoS), and how it handles smart contracts.
* Execution Model: Understand how smart contracts are executed, including gas costs, transaction handling, and state management.
* Security Model: Grasp the security model of the blockchain, including permission levels, access controls, and typical attack vectors.

### 2. **Follow Secure Coding Standards**

Adhering to established coding standards helps prevent common vulnerabilities:

*   Use Strict Mode: Enable strict mode in JavaScript (`'use strict';`) to catch common coding bloopers, preventing the use of undeclared variables and other errors.

    ```javascript
    'use strict';
    ```
* Consistent Coding Style: Maintain a consistent coding style using tools like ESLint with security-focused plugins (e.g., `eslint-plugin-security`).
* Modular Code Structure: Break down your code into smaller, reusable, and testable modules to enhance readability and maintainability.

### 3. **Input Validation and Sanitization**

Consistently validate and sanitize all inputs to safeguard against malicious data that could trigger unintended or harmful behavior in the contract.

*   **Type Checking:** Ensure that inputs are of the expected type (e.g., numbers, strings, addresses).

    ```javascript
    function transferFrom(from, to, value) {
      Utils.assert(Utils.addressCheck(from) === true, 'Arg-from is not a valid address.');
      Utils.assert(Utils.addressCheck(to) === true, 'Arg-to is not a valid address.');
      Utils.assert(Utils.stoI64Check(value) === true, 'Arg-value must be alphanumeric.');
      Utils.assert(Utils.int64Compare(value, '0') > 0, 'Arg-value must be greater than 0.');
      Utils.assert(from !== to, 'From cannot equal to address.');
        // Transfer logic
    }
    ```
*   **Boundary Checks:** Implement checks for input ranges to avoid integer overflows or underflows.

    ```javascript
    function checkBalance(amount <= balances[msg.sender], "Insufficient balance");
    ```
* **Avoid Trusting External Inputs:** Do not trust data coming from outside the contract without proper validation.

### 4. **Access Control and Authorization**

Implement robust access control mechanisms to restrict sensitive functions:

*   **Role-Based Access Control (RBAC):** Define roles (e.g., admin, user) and restrict function access based on these roles.

    ```javascript
    const roles = {
        ADMIN: "admin",
        USER: "user",
    };

    function setAdmin(newAdmin) {
        // Check if the current user has the ADMIN role
        if (hasAdminRole()) {
            // Set the new admin
            admin = newAdmin;
        } else {
            // Handle error or unauthorized access
            console.error("Only admins can set the admin");
        }
    }

    // Function to check if the current user has the ADMIN role
    function hasAdminRole() {
        // Implement logic to check the user's role based on your authentication and authorization mechanism
        // For example, you might use a token or session to store the user's role
        return true; // Replace with your actual implementation
    }
    ```
*   **Modifiers:** Use function modifiers to enforce access restrictions.

    ```javascript
    function onlyAdmin(fn) {
        return function() {
            if (msg.sender !== admin) {
                throw new Error("Caller is not admin");
            }
            return fn.apply(this, arguments);
        };
    }

    function sensitiveFunction() {
        // Sensitive operations
    }

    // Apply the modifier
    sensitiveFunction = onlyAdmin(sensitiveFunction);
    ```
* **Least Privilege Principle:** Grant the minimum necessary permissions to each role or function.

### 5. **Prevent Reentrancy Attacks**

Reentrancy is a prevalent attack method where an external contract exploits a vulnerability by invoking the original contract **before its previous execution has finished**, potentially manipulating its state or draining assets.

*   **Use Checks-Effects-Interactions Pattern:** Perform all necessary checks, update state variables, and then interact with external contracts.

    ```javascript
    function withdraw(amount, account) {
      // Check if the account has sufficient balance
      if (balances[account] < amount) {
        throw new Error("Insufficient balance");
      }

      // Update account balance
      balances[account] -= amount;

      // Simulate transfer (assuming a separate function for handling transfers)
      transferFunds(account, amount);
    }

    function transferFunds(account, amount) {
      // Implement logic to transfer funds to the recipient (e.g., using a payment gateway)
      console.log(`Transferred ${amount} to account ${account}`);
    }
    ```
*   **Reentrancy Guards:** Implement mutexes or reentrancy guards to prevent multiple simultaneous calls.

    ```javascript
    let locked = false;

    function noReentrancy(fn) {
      return function() {
        if (locked) {
          throw new Error("No reentrancy");
        }

        locked = true;

        try {
          return fn.apply(this, arguments);
        } finally {
          locked = false;
        }
      };
    }

    function withdraw(amount) {
      // Withdrawal logic
    }

    // Apply the modifier
    withdraw = noReentrancy(withdraw);
    ```

### 6. **Secure Handling of ZETRIX and Tokens**

Manage the transfer and receipt of Zetrix or tokens securely:

*   **Use Safe Transfer Methods:** Prefer using `Chain.payCoin` for transferring ZETRIX, or use safe token transfer functions provided by Zetrix Standard.

    ```javascript
    function safeTransferFrom(paramObj) {

      Utils.assert(checkAssetExsit(id), 'Check Token not exist.');

      let owner = getAssetOwner(id);
      Utils.assert(owner === from, 'Token owner not equal from.');
      Utils.assert(owner === Chain.msg.sender || getApproveSingle(id) === Chain.msg.sender || getApproveAll(owner, Chain.msg.sender), 'No privilege to trans.');

      saveAssetUserCount(from, Utils.int64Sub(getAssetUserCount(from), '1'));
      saveAssetUserCount(to, Utils.int64Add(getAssetUserCount(to), '1'));

      saveAssetOwner(id, to);

      _approve(owner, id, '');

      Chain.tlog('Transfer', owner, to, id);

      return;
    }
    ```

### 7. **Implement Proper Error Handling**

Ensure that your smart contract gracefully handles errors:

*   **Use Require, Revert, and Assert Appropriately:**

    * **`require`** for input validation and conditions.
    * **`revert`** for manual error throwing with messages.
    * **`assert`** for invariants and conditions that should never be false.

    ```javascript
    javascriptCopy coderequire(condition, "Error message");
    ```
* **Descriptive Error Messages:** Provide clear and descriptive error messages to aid in debugging and transparency.

### 8. **Optimize for Gas Efficiency**

Although not strictly a security measure, optimizing gas consumption helps prevent **denial-of-service (DoS) attacks** that exploit **out-of-gas exceptions**, ensuring more reliable and efficient contract execution.

*   **Efficient Code:** Write gas-efficient code to minimize transaction costs and prevent attackers from exploiting high gas consumption.

    ```javascript
    javascriptCopy code// Inefficient
    for (len i = 0; i < array.length; i++) {
        // Operations
    }
    // Optimized

    let len = array.length;

    for (let i = 0; i < len; i++) {

        // Operations

    }
    ```
* **Avoid Unbounded Loops:** Limit the number of iterations in loops to prevent excessive gas consumption.

### **9. Conduct Regular Security Audits and Penetration Testing**

Regularly assess the security posture of your smart contracts:

* **Scheduled Audits:** Plan periodic security audits, especially after significant code changes or updates.
* **Bug Bounties:** Consider implementing bug bounty programs to incentivize external security researchers to identify vulnerabilities.
* **Automated Testing:** Integrate automated security testing into your continuous integration/continuous deployment (CI/CD) pipeline.

### **10. Implement Time Locks and Pausable/Freezable Contracts**

Provide mechanisms to mitigate potential vulnerabilities post-deployment:

*   **Time Locks:** Introduce delays for critical operations to allow for oversight and intervention if malicious activity is detected.

    ```javascript
    function setTimelock(_delay){
        timelock = block.timestamp + _delay;
    }

    function criticalFunction(){
        require(block.timestamp >= timelock, "Timelock not expired");
        // Critical operations
    }
    ```
* **Pausable/Freezable Contracts:** Allow the contract to be paused in case of emergencies to prevent further damage.

```javascript
function freezed(paramObj) {
  Utils.assert(paramObj.id !== undefined && paramObj.id.length > 0, 'Param obj has no id.');
  let freezedObj = {};
  freezedObj.freezed = getAsset(paramObj.id).freezed;
  return freezedObj;
}
```

### Conclusion

Creating secure JavaScript-based smart contracts on the Zetrix blockchain or any blockchain platform demands a holistic approach that includes secure coding standards, extensive testing, routine audits, and ongoing monitoring. Following the best practices detailed above helps minimize security risks and strengthens the resilience and dependability of your smart contracts.
