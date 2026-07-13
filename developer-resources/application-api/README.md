---
description: Partner integration APIs for applications that connect with Zetrix services.
---

# Application API

## Application API

Application APIs let external partners integrate their applications with Zetrix services. Each API provides a documented interface for a specific service and partner workflow.

Use these APIs when your application needs to exchange data or invoke Zetrix service capabilities. The detailed API guides define the supported operations, authentication requirements, and request formats.

### Available APIs

#### JMYR API

The **JMYR API** supports partner integrations with JMYR services.

**Use case:** IPayEasy integrates with JMYR through this API.

#### MBI VC Service API

The **MBI VC Service API** supports partner integrations with the MBI Verifiable Credential Service.

**Use case:** NIISE integrates with the MBI VC Service through this API.

### Integration lifecycle

Partner integrations generally follow these stages:

1. **Plan the workflow**\
   Identify the business operation your application needs to support. Select the API that provides the required capability.
2. **Set up access**\
   Obtain the required partner credentials. Store credentials securely and keep them outside source control.
3. **Build and test**\
   Implement requests using the detailed API reference. Validate successful and failed responses in a test environment.
4. **Deploy and monitor**\
   Release the integration with monitoring, logging, and retry handling. Review failures and response changes regularly.

### Integration principles

#### Secure access

Treat API credentials as sensitive secrets. Restrict access to authorized systems and team members. Rotate credentials according to your security policy.

#### Reliable processing

Handle failed, delayed, or repeated requests safely. Where supported, use idempotent processing to prevent duplicate outcomes during retries.

#### Traceability

Record request and response identifiers. These records help investigate failures, support reconciliation, and assist partner support teams.

### Choosing an API

Start with the API that matches your integration:

* Use the **JMYR API** for JMYR service integrations.
* Use the **MBI VC Service API** for Verifiable Credential Service integrations.

{% hint style="info" %}
Open the relevant API page in the navigation for endpoint-specific implementation details.
{% endhint %}
