# Platform (BaaS API)

## Introduction

This preliminary process for an organization to establish its operational presence on the Zetrix Platform. The Zetrix Platform ([https://platform.zetrix.com](https://platform.zetrix.com/)) serves as the official subscription and API management portal for all Zetrix Baas services, including the Verifiable Credential (VC) ecosystem.

Completion of the procedures described herein is a prerequisite for accessing Zetrix's blockchain-based VC issuance functionalities.

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

To generate and provide a secure authentication credential, enabling the Issuer to interface programmatically with Zetrix VC APIs.

### **Procedure and Key Management**

1. Upon activation of a valid subscription, the platform automatically generates a unique API Key.
2. This API Key serves as the primary authentication mechanism for all system to system interactions between the Issuer's infrastructure and Zetrix VC services.
3. The Issuer is solely responsible for safeguarding the API Key. It must be stored and transmitted securely, as it provides direct access to credential issuance functionalities. Compromise of the key may lead to unauthorized access and service usage.

The successful completion of the Registration, Subscription, and API Provisioning process establishes the Issuer's authenticated identity and grants the necessary permissions to participate in the Zetrix VC ecosystem. This foundational step ensures the Issuer is fully equipped with the correct service entitlement and secure API access to proceed with subsequent technical configurations, including identity setup, credential template creation, and the execution of end-to-end VC lifecycle processes.
