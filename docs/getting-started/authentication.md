# Authentication

The EPMware REST API uses token-based authentication to secure API endpoints. This page explains how to generate, use, and manage authentication tokens.

## Overview

All REST API calls require authentication using a token that must be included in the request header. This token identifies and authorizes the user making the API call.

!!! warning "Security Best Practice"
    Treat API tokens like passwords. Never share tokens, commit them to version control, or expose them in client-side code.

## Generating a Token

### Prerequisites

- Valid EPMware user account
- Appropriate permissions to access API endpoints
- Access to EPMware Security Module

### Step-by-Step Token Generation

1. **Navigate to Security Module**
   - Log into EPMware application
   - Click on the Security Module

2. **Access User Management**
   - Select "Users" from the navigation menu
   - Locate your user account in the list

3. **Generate Token**
   - Right-click on the user record
   - Select "Generate token" from the context menu
   - Copy the generated token immediately

![Token Generation Process](../assets/images/token-generation.png)
*Figure: Generating an API token in EPMware*

!!! info "Token Format"
    Tokens are typically UUID format: `15388ad5-c9af-4cf3-af47-8021c1ab3fb7`

## Using the Token

### Authentication Header

Include the token in every API request using the `Authorization` header:

```
Authorization: Token <your-token-here>
```

### Example with curl

```bash
curl GET 'https://demo.epmwarecloud.com/service/api/task/get_status/244591' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

### Example with Python

```python
import requests

headers = {
    'Authorization': 'Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7',
    'Content-Type': 'application/json'
}

response = requests.get(
    'https://demo.epmwarecloud.com/service/api/task/get_status/244591',
    headers=headers
)
```

### Example with PowerShell

```powershell
$headers = @{
    "Authorization" = "Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
}

$response = Invoke-RestMethod `
    -Uri "https://demo.epmwarecloud.com/service/api/task/get_status/244591" `
    -Method Get `
    -Headers $headers
```

### Example with JavaScript (Node.js)

```javascript
const axios = require('axios');

const config = {
  headers: {
    'Authorization': 'Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
  }
};

axios.get('https://demo.epmwarecloud.com/service/api/task/get_status/244591', config)
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error('Error:', error);
  });
```

## Token Management

### Token Lifecycle

| Aspect | Description |
|--------|-------------|
| **Generation** | Created on-demand through Security Module |
| **Expiration** | Tokens do not expire by default |
| **Revocation** | Can be revoked by regenerating a new token |
| **Rotation** | Recommended to rotate tokens periodically |

### Best Practices

#### 1. Secure Storage

- Store tokens in environment variables
- Use secure credential management systems
- Never hardcode tokens in source code

**Environment Variable Example:**
```bash
# Set environment variable
export EPMWARE_API_TOKEN="15388ad5-c9af-4cf3-af47-8021c1ab3fb7"

# Use in script
curl GET 'https://demo.epmwarecloud.com/service/api/task/get_status/244591' \
  -H "Authorization: Token $EPMWARE_API_TOKEN"
```

#### 2. Token Rotation

Regularly rotate tokens to maintain security:

1. Generate new token
2. Update all applications with new token
3. Test with new token
4. Revoke old token

#### 3. Access Control

- Create dedicated service accounts for API access
- Grant minimal required permissions
- Use different tokens for different environments

## Error Handling

### Authentication Errors

| Status Code | Description | Resolution |
|------------|-------------|------------|
| `401 Unauthorized` | Missing or invalid token | Verify token is correct and included |
| `403 Forbidden` | Token lacks permissions | Check user permissions in Security Module |
| `404 Not Found` | Invalid endpoint URL | Verify URL structure and module/action |

### Common Authentication Issues

#### Missing Authorization Header

**Error Response:**
```json
{
  "status": "E",
  "message": "Authentication required"
}
```

**Solution:** Include the Authorization header in your request

#### Invalid Token Format

**Error Response:**
```json
{
  "status": "E",
  "message": "Invalid token format"
}
```

**Solution:** Ensure token format is: `Token <space> <your-token>`

#### Expired or Revoked Token

**Error Response:**
```json
{
  "status": "E",
  "message": "Token is invalid or has been revoked"
}
```

**Solution:** Generate a new token through the Security Module

## Security Considerations

### Do's âœ…

- Use HTTPS for all API calls
- Store tokens securely (environment variables, secrets management)
- Implement token rotation policies
- Monitor API usage for suspicious activity
- Use dedicated service accounts for automation

### Don'ts âŒ

- Share tokens between users or systems
- Commit tokens to version control
- Use tokens in client-side JavaScript
- Log tokens in plain text
- Use the same token across all environments

## Testing Authentication

### Quick Test Script

Use this script to verify your token is working:

```bash
#!/bin/bash

# Set your values
EPMWARE_URL="https://demo.epmwarecloud.com"
API_TOKEN="your-token-here"
TASK_ID="244591"

# Test authentication
echo "Testing EPMware API Authentication..."

response=$(curl -s -o /dev/null -w "%{http_code}" \
  -H "Authorization: Token $API_TOKEN" \
  "$EPMWARE_URL/service/api/task/get_status/$TASK_ID")

if [ "$response" = "200" ]; then
  echo "âœ… Authentication successful!"
else
  echo "âŒ Authentication failed with status: $response"
fi
```

## Next Steps

Once you have successfully authenticated:

- [Understand URL Structure](url-structure.md) - Learn how to construct API endpoints
- [Make API Calls](../modules/) - Explore available API modules
- [Handle Responses](response-formats.md) - Process API responses

## Related Topics

- [Security Module APIs](../modules/security/)
- [Error Handling](error-handling.md)
- [Best Practices](../best-practices/security.md)