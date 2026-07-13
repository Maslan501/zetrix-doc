---
description: Integrate JMYR services with your external partner application.
---

# JMYR API

## JMYR API

The JMYR API supports external partner integrations with JMYR services. Use it to connect your application through a documented API interface.

### Who should use it

This API is for partner teams building integrations with JMYR. It provides the information needed to implement and maintain the connection.

**Use case:** IPayEasy integrates with JMYR through this API.

### Integration overview

An integration typically follows this flow:

1. Your application authenticates with the required partner credentials.
2. It sends requests to JMYR for the required service operation.
3. It validates the response and updates its own workflow.

The exact endpoints and payloads depend on the operation you integrate.

### Before you begin

Prepare the following before implementing the integration:

* Partner access and the required authentication credentials
* A test environment for validating requests and responses
* Error-handling and monitoring for failed or delayed requests

Keep credentials outside source control. Rotate them according to your security policy.

### Implementation guidance

Start with one end-to-end workflow. Confirm successful responses and expected error handling before expanding the integration.

Use idempotent handling where an operation might be retried. Record request and response identifiers to support troubleshooting and reconciliation.

### Get started

The detailed guide covers the available endpoints, authentication, request formats, and integration requirements.

<a href="https://docs.zetrix.com/jmyr-api" class="button primary">Open JMYR API documentation</a>
