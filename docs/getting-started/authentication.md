# Authentication

The EPMware REST API uses token-based authentication to secure API endpoints. This page explains how to generate, use, and manage authentication tokens.

## Overview

All REST API calls require authentication using a token that must be included in the request header. This token identifies and authorizes the user making the API call.

!!! warning "Security Best Practice"
    Treat API tokens like passwords. Never share tokens, commit them to version control, or expose them in client-side code.

## Generating a Token

### Step-by-Step Token Generation

   1. Log into EPMware application
   2. Click on the Security Module
   3. Select "Users" from the navigation menu
   4. Locate your user account in the list
   5. Right-click on the user record
   6. Select "Generate token" from the context menu
   7. Copy the generated token immediately

![Token Generation Process](../assets/images/token-generation.png)<br/>
*Figure: Generating an API token in EPMware*

!!! info "Token Format"
    Tokens are typically UUID format: `15388ad5-c9af-4cf3-af47-8021c1ab3fb7`

## Using the Token

### Authentication Header

Include the token in every API request using the `Authorization` header:

```
Authorization: Token <your-token-here>
```

### Example

```bash
curl GET 'https://demo.epmwarecloud.com/service/api/task/get_status/244591' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

## Token Management

### Token Lifecycle

| Aspect | Description |
|--------|-------------|
| **Generation** | Created on-demand through Security Module |
| **Expiration** | Tokens do not expire by default |
| **Revocation** | Can be revoked by regenerating a new token |
| **Rotation** | Recommended to rotate tokens periodically |



## Next Steps

Once you have successfully authenticated:

- [Understand URL Structure](url-structure.md) - Learn how to construct API endpoints
- [Make API Calls](../modules/) - Explore available API modules
- [Handle Responses](response-formats.md) - Process API responses

## Related Topics

- [Security Module APIs](../modules/security/)
