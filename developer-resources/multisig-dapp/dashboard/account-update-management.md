# Account Update Management

Account Update Management allows authorized users to modify governance, identity, and participant structure of a multisignature (multisig) account. These updates help organizations adapt to operational changes, team restructuring, growth, or enhanced security needs. All updates are securely governed through the multisig approval process, ensuring that no single user can modify account governance unilaterally.

***

<figure><img src="../../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

### **Change Account Name**

#### **Purpose**

Allows updating the display name of the multisig account.

#### **Details**

* This change is **purely cosmetic**
* It does **not** affect security, signers, approvals, balances, or contract logic
* Helps clearly identify accounts in environments managing multiple wallets

#### **When Useful**

* Renaming for clarity or organization alignment
* Reflecting new project names or ownership changes
* Improving dashboard readability

#### **Result**

* New name appears immediately after execution
* Account identity becomes easier to recognize
* No impact on functionality or governance

***

### **Change Weightage**

#### **Purpose**

Modify the approval weight assigned to each signer within the multisig account.

#### **Details**

* This is a **critical governance function**
* Directly affects how approvals are counted
* Changes the level of authority of each signer
* Can rebalance organizational control

#### **When Useful**

* Governance restructuring
* Role promotion or responsibility changes
* Risk management adjustments
* Compliance and corporate policy evolution

#### **Security Impact**

* Requires multisig approvals
* Prevents unilateral power shifts
* Ensures controlled governance realignment

#### **Result**

* Updated weights apply immediately after execution
* New approval distribution governs future transactions

***

### **Add Signer**

#### **Purpose**

Add a new authorized participant to the multisig account.

#### **Details**

* Expands the group of individuals managing the account
* New signer may be assigned weight and incorporated into governance rules

#### **Common Use Cases**

* Team expansion
* New department or partner involvement
* Organizational scaling
* Compliance requirements demanding more oversight

#### **Security Behavior**

* Requires approvals before addition becomes valid
* Ensures existing signers consent to expansions

#### **Result**

* New signer becomes officially recognized
* Gains approval capabilities according to assigned weight

***

### **Remove Signer**

#### **Purpose**

Safely remove an existing signer from the multisig account.

#### **Details**

* Used when a signer should no longer participate in decision-making

#### **Common Scenarios**

* Signer leaves the organization
* Security risk or compromised wallet detected
* Role restructuring or reassignment

#### **Safety Enforcement**

* Requires multisig approvals before removal
* System prevents governance from being weakened to zero or invalid state
* Ensures secure and controlled member removal

#### **Result**

* Signer is removed from the authorization list
* No longer able to approve or execute transactions

***

### **Confirmation & Completion**

#### **Approval Process**

Every account update must pass through the multisig approval workflow.

#### **Execution Behavior**

Once the required approval threshold is met:

* Blockchain executes the final update
* Account configuration is securely updated
* System ensures immutability and transparency

#### **User Experience**

* Dashboard automatically refreshes
* Updated governance or account structure becomes visible
* Final confirmation is displayed to users
