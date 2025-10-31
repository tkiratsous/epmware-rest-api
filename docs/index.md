# EPMware REST API Guide

Welcome to the EPMware REST API Guide. This comprehensive reference provides detailed documentation for integrating with EPMware through RESTful web services, enabling automation and external system integration.

## About This Guide

The EPMware REST API enables developers to programmatically interact with EPMware modules, automate processes, and integrate with external applications. This guide is designed for developers, system integrators, and administrators responsible for:

- Integrating EPMware with external systems
- Automating EPMware processes through API calls
- Building custom applications that interact with EPMware
- Managing security and user provisioning programmatically
- Orchestrating deployment and export operations

!!! info "Version Information"
    This guide covers EPMware REST API version 1.3, updated May 2025

## Key Features

### RESTful Architecture
The EPMware REST API follows RESTful principles, providing a consistent and predictable interface for all operations.

### Module-Based Organization
APIs are organized by functional modules:
- **Task** - Monitor and manage task execution
- **ERP** - Handle ERP import operations
- **Deployment** - Execute and monitor deployments
- **Export** - Run exports and retrieve results
- **Security** - Manage users and security groups

### Token-Based Authentication
Secure token-based authentication ensures authorized access to API endpoints while maintaining session state.

## Quick Start

New to EPMware REST API? Follow these steps to get started:

1. **[Generate Authentication Token](getting-started/authentication.md)** - Create an API token for your user
2. **[Understand URL Structure](getting-started/url-structure.md)** - Learn the API endpoint patterns
3. **[Make Your First Call](getting-started/response-formats.md)** - Execute a simple API request
4. **[Handle Responses](getting-started/error-handling.md)** - Process success and error responses
5. **[Explore Examples](examples/)** - Review working code samples

## API Modules Overview

| Module | Purpose | Common Use Cases |
|--------|---------|------------------|
| **Task** | Task management and monitoring | Get task status, retrieve logs |
| **ERP** | ERP data import operations | Upload files, trigger imports |
| **Deployment** | Deployment execution | Run deployments, get execution details |
| **Export** | Data export operations | Execute exports, download results |
| **Security** | User and group management | Create users, assign permissions |

## Prerequisites

Before using this guide, ensure you have:

- Access to an EPMware environment (cloud or on-premise)
- Administrator privileges to generate API tokens
- Basic understanding of REST API concepts
- Familiarity with HTTP methods (GET, POST, PUT, PATCH)
- A tool for making API calls (curl, Postman, or programming language)

!!! warning "Important Security Note"
    Always store API tokens securely and never commit them to version control systems. Rotate tokens regularly and follow security best practices.

## Making Your First API Call

Here's a simple example to get you started:

```bash
# Get task status
curl GET 'https://demo.epmwarecloud.com/service/api/task/get_status/244591' \
  -H 'authorization: Token YOUR-TOKEN-HERE'
```

**Response:**
```json
{
  "status": "S",
  "percentCompleted": "100",
  "message": "Task completed successfully",
  "taskId": "244591"
}
```

## Support & Resources

- **Technical Support**: support@epmware.com
- **Phone**: 408-614-0442
- **Website**: [www.epmware.com](https://www.epmware.com)
- **API Support**: [https://support.claude.com](https://support.claude.com)
- **API Documentation**: [https://docs.claude.com](https://docs.claude.com)

---

## Documentation Sections

Explore the complete REST API documentation:

<div class="grid cards" markdown>

-   :material-rocket-launch:{ .lg .middle } **Getting Started**

    ---

    Authentication, URL structure, and basic concepts

    [:octicons-arrow-right-24: Get started](getting-started/)

-   :material-api:{ .lg .middle } **API Modules**

    ---

    Detailed documentation for all API modules

    [:octicons-arrow-right-24: Explore modules](modules/)

-   :material-book-open-page-variant:{ .lg .middle } **API Reference**

    ---

    Complete endpoint reference and parameters

    [:octicons-arrow-right-24: View reference](reference/)

-   :material-card-account-details:{ .lg .middle } **Task Management**

    ---

    Monitor and manage task execution

    [:octicons-arrow-right-24: Task APIs](modules/task/)

-   :material-database-import:{ .lg .middle } **ERP Integration**

    ---

    Upload files and run ERP imports

    [:octicons-arrow-right-24: ERP APIs](modules/erp/)

-   :material-rocket-launch-outline:{ .lg .middle } **Deployment**

    ---

    Execute and monitor deployments

    [:octicons-arrow-right-24: Deploy APIs](modules/deployment/)

-   :material-database-export:{ .lg .middle } **Export Operations**

    ---

    Run exports and download results

    [:octicons-arrow-right-24: Export APIs](modules/export/)

-   :material-shield-account:{ .lg .middle } **Security Management**

    ---

    Manage users and security groups via API

    [:octicons-arrow-right-24: Security APIs](modules/security/)

-   :material-code-tags:{ .lg .middle } **Code Examples**

    ---

    Working examples in multiple languages

    [:octicons-arrow-right-24: View examples](examples/)

-   :material-lightbulb:{ .lg .middle } **Best Practices**

    ---

    Guidelines for optimal API usage

    [:octicons-arrow-right-24: Learn more](best-practices/)

-   :material-book-multiple:{ .lg .middle } **Appendices**

    ---

    Additional resources and references

    [:octicons-arrow-right-24: Resources](appendices/)

</div>