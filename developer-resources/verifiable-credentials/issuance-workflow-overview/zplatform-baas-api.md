# ZPlatform (BaaS API)

## Introduction

This preliminary process for an organization to establish its operational presence on the Zetrix Platform. The Zetrix Platform ([https://platform.zetrix.com](https://platform.zetrix.com/)) serves as the official subscription and API management portal for all Zetrix Baas services, including the Verifiable Credential (VC) ecosystem.

Completion of the procedures described herein is a prerequisite for accessing Zetrix's blockchain-based VC issuance functionalities.

<figure><img src="../../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

## Process Overview

The onboarding workflow consists of three sequential stages: Registration, Subscription, and API Key Provisioning. This process formally identifies the Issuer, grants service entitlements, and provides secure access credentials.

### **Organizational Registration**

To formally register the Issuing organization on the Zetrix Platform, establishing a verified identity and granting access to the administrative dashboard.

#### **Procedure**

1. The Issuer initiates account creation on the Zetrix Platform.
2. The Issuer provides all required organizational details as stipulated by the platform.
3. The system creates a secure and authenticated profile for the Issuer.
4. Upon successful registration, the Issuer is granted access to the service management dashboard, enabling subscription and API management.

### **Service Subscription**

To enroll the Issuer in a Verifiable Credential Subscription Plan, defining the scope, limits, and privileges of their service entitlement.

#### **Subscription Plan Scope**

The selected plan governs the Issuer's operational parameters, which include but are not limited to:

* **API Usage Quotas:** The volume of API calls permitted over a defined period.
* **Rate Limits:** The frequency at which API requests can be made.
* **Access Privileges:** Specific VC issuance functions enabled for the Issuer.
* **Operational Bounds:** General service-level boundaries and capabilities.

### **API Key Provisioning**

To generate and provide a secure authentication credential, enabling the user to interface programmatically with Zetrix BaaS APIs.

### **Registration and Key Management**

#### Access Registration Page

Click **"Create an account"** or Navigate to the ZPlatform registration page:

* [https://platform.zetrix.com/#/register ](https://platform.zetrix.com/#/register)&#x20;

The **Sign Up Here** page will be displayed.

<figure><img src="../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

#### Complete Registration Form

All fields marked with (\*) are mandatory.

* **Username**\
  Unique identifier for your ZPlatform account.
* **Email**\
  Valid email address used for authentication and system notifications.
* **Phone**\
  Select the appropriate country code and enter a valid mobile number.
* **First Name**\
  Your given name.
* **Last Name**\
  Your family name.
* **Password**\
  Password must comply with the following security requirements:
  * Minimum of **8 characters**
  * At least **one uppercase letter**
  * At least **one number, symbol, or whitespace character**

#### Accept Terms and Conditions

* Select **“I agree to the privacy policy & terms”** to proceed.

#### Submit Registration

* Click **SIGN UP** to create your account.
* Upon successful registration, you may proceed to log in.

> Note: If you already have an account, select **Sign in instead** to access the login page.

***

### Account Login

#### Access Login Page

Navigate to:

* [https://platform.zetrix.com/#/login](https://platform.zetrix.com/#/login)

#### Enter Login Credentials

* **Username / Email**\
  Enter your registered username or email address.
* **Password**\
  Enter your account password.

Optional:

* Enable **Remember me** to retain login session on the device.

#### Login

* Click **LOGIN** to access the ZPlatform dashboard.

<figure><img src="../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

***

### API Management Overview

After successful login:

1. From the left navigation menu, select **Manage API**.
2. The **Manage your API** page will be displayed.

<figure><img src="../../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

The API management table provides the following information:

* **Key Name** – User-defined identifier for the API key
* **API Key (Hashed)** – Encrypted representation of the API key
* **Expiry** – API key expiration date and time
* **Status** – Current state of the API key (Active / Revoked)
* **Actions** – Available actions (Revoke, Delete)

***

### Creating an API Key

#### Initiate API Key Creation

* On the **Manage API** page, click **CREATE**.

A dialog titled **Create your API keys** will appear.

<figure><img src="../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

#### Configure API Key

Complete the following fields:

* **Key Name**\
  A unique and descriptive name for the API key (e.g., `Billing-Service`, `Sandbox-Testing`).
* **Expiration**\
  Select the expiration date for the API key. The key will automatically become invalid after this date.
* **Password**\
  Enter your ZPlatform account password to confirm the action.

#### Generate API Key

* Click **GENERATE TOKEN** to create the API key.

> Important: The API key is a sensitive credential. Store it securely and restrict access appropriately.

***

### API Key Management

Existing API keys can be managed from the **Manage API** page.

#### Key Status

* **Active** – API key is valid and can be used for API requests.
* **Revoked** – API key access is disabled and cannot be used.

#### Available Actions

* **Revoke**\
  Temporarily disables the API key without deleting it.
* **Delete**\
  Permanently removes the API key. This action is irreversible.

***

## Account Balance

* The current **Account Balance** is displayed at the top of the dashboard (e.g., RM 0.00).
* Select **RELOAD** to top up the account if required for more API usage.

***

## Security Best Practices

To ensure secure usage of ZPlatform APIs:

* Do not embed API keys in client-side or publicly accessible code
* Store API keys using secure configuration or secret management tools
* Rotate API keys periodically
* Use separate API keys for development, testing, and production environments
* Immediately revoke compromised or unused API keys

***

## Support and Assistance

For technical assistance or account-related issues:

* Select **Contact Support** from the navigation menu, or
* Use **Forgot Password?** on the login page for credential recovery
