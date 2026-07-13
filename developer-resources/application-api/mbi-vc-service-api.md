---
description: Integrate your application with the MBI Verifiable Credential Service.
---

# MBI VC Service API

## MBI VC Service API

The MBI VC Service API supports external partner integrations with the MBI Verifiable Credential Service. Use it to connect your application to the service through a documented API interface.

### Who should use it

This API is for partner teams that consume MBI VC Service capabilities in their applications. It provides the information needed to implement and maintain the integration.

**Use case:** NIISE integrates with the MBI VC Service through this API.

### Integration overview

An integration typically follows this flow:

1. Your application authenticates with its assigned partner credentials.
2. It submits a request to the MBI VC Service.
3. It processes the service response in its credential workflow.

The required endpoints and payloads depend on the capability you integrate.

### Before you begin

Prepare the following before implementation:

* Partner access and required authentication credentials
* A test environment for validating requests and responses
* Secure storage for credential-related data and service credentials

Apply least-privilege access to credentials. Do not place secrets in source control or client-side applications.

### Production considerations

Validate all responses before acting on credential-related data. Handle failed or delayed requests safely within your application workflow.

Record request and response identifiers for troubleshooting and reconciliation. Use monitoring to identify authentication failures and unexpected response patterns.

### Get started

The detailed guide covers the available endpoints, authentication, request formats, and integration requirements.

[View the MBI VC Service API documentation](https://docs.zetrix.com/mbi-vc-api)

<a href="https://docs.zetrix.com/mbi-vc-api" class="button primary">Open MBI VC Service API documentation</a>
