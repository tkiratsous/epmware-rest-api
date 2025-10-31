# URL Structure

Understanding the EPMware REST API URL structure is essential for making successful API calls. This page explains how URLs are constructed and provides examples for each module.

## Base URL Format

All EPMware REST API endpoints follow this pattern:

```
http(s)://<EPMWARE_URL>/service/api/<module>/<action>
```

### URL Components

| Component | Description | Example |
|-----------|-------------|---------|
| `Protocol` | HTTP or HTTPS (recommended) | `https://` |
| `EPMWARE_URL` | Your EPMware instance URL | `demo.epmwarecloud.com` |
| `service/api` | Fixed API path | `service/api` |
| `module` | API module name | `task`, `erp`, `deployment` |
| `action` | Specific operation | `get_status`, `run`, `upload_file` |

## Environment URLs

Your EPMware URL varies by deployment type and environment:

### Cloud Deployments

```
https://[client-name].epmwarecloud.com
```

**Examples:**
- Production: `https://acme.epmwarecloud.com`
- Test: `https://acme-test.epmwarecloud.com`
- Development: `https://acme-dev.epmwarecloud.com`

### On-Premise Deployments

```
http(s)://[server-name]:[port]/EPMWARE
```

**Examples:**
- Internal: `https://epmware.company.local:8443/EPMWARE`
- External: `https://epmware.company.com/EPMWARE`

!!! warning "HTTPS Recommended"
    Always use HTTPS in production environments to ensure secure data transmission.

## Module Endpoints

### Available Modules

| Module | Purpose | Base Path |
|--------|---------|-----------|
| `task` | Task management | `/service/api/task` |
| `erp` | ERP operations | `/service/api/erp` |
| `deployment` | Deployment execution | `/service/api/deployment` |
| `export` | Export operations | `/service/api/export` |
| `security` | User management | `/service/api/security` |

## Complete URL Examples

### Task Module

```bash
# Get task status
https://demo.epmwarecloud.com/service/api/task/get_status/244591

# Get task log file
https://demo.epmwarecloud.com/service/api/task/get_log_file/244591
```

### ERP Module

```bash
# Upload file for ERP import
https://demo.epmwarecloud.com/service/api/erp/upload_file

# Run ERP import
https://demo.epmwarecloud.com/service/api/erp/run
```

### Deployment Module

```bash
# Run deployment
https://demo.epmwarecloud.com/service/api/deployment/run

# Get deployment execution details
https://demo.epmwarecloud.com/service/api/deployment/get_execution_details
```

### Export Module

```bash
# Run export
https://demo.epmwarecloud.com/service/api/export/run

# Download export file
https://demo.epmwarecloud.com/service/api/export/download_file/244596

# Get export execution details
https://demo.epmwarecloud.com/service/api/export/get_execution_details
```

### Security Module

```bash
# Create SSO user
https://demo.epmwarecloud.com/service/api/security/users/sso/create

# Update SSO user
https://demo.epmwarecloud.com/service/api/security/users/sso/update/SSO_USER1

# Enable/Disable user
https://demo.epmwarecloud.com/service/api/security/users/sso/enable/SSO_USER1
https://demo.epmwarecloud.com/service/api/security/users/sso/disable/SSO_USER1

# Manage groups
https://demo.epmwarecloud.com/service/api/security/users/sso/assign_groups
https://demo.epmwarecloud.com/service/api/security/users/sso/unassign_groups

# Get users and groups
https://demo.epmwarecloud.com/service/api/security/users
https://demo.epmwarecloud.com/service/api/security/groups
```

## Path Parameters vs Query Parameters

### Path Parameters

Used in the URL path to identify specific resources:

```
/service/api/task/get_status/{TASK_ID}
                              ^^^^^^^^
                           Path Parameter
```

**Example:**
```bash
https://demo.epmwarecloud.com/service/api/task/get_status/244591
```

### Query Parameters

Added after `?` for filtering or options:

```
/service/api/security/users?userType=NATIVE
                            ^^^^^^^^^^^^^^^^
                           Query Parameter
```

**Example:**
```bash
https://demo.epmwarecloud.com/service/api/export/get_execution_details?name=PBCS&latest_only=Y
```

## URL Construction Best Practices

### 1. Use Environment Variables

Store base URLs in environment variables:

```bash
# Set environment variable
export EPMWARE_BASE_URL="https://demo.epmwarecloud.com"

# Use in scripts
curl "$EPMWARE_BASE_URL/service/api/task/get_status/244591"
```

### 2. URL Encoding

Always URL-encode special characters in parameters:

```python
import urllib.parse

# Encode special characters
name = "PBCS Export (Production)"
encoded_name = urllib.parse.quote(name)
url = f"{base_url}/service/api/export/run?name={encoded_name}"
```

### 3. Build URLs Programmatically

**Python Example:**
```python
class EPMwareAPI:
    def __init__(self, base_url):
        self.base_url = base_url.rstrip('/')
    
    def build_url(self, module, action, path_params=None, query_params=None):
        # Build base URL
        url = f"{self.base_url}/service/api/{module}/{action}"
        
        # Add path parameters
        if path_params:
            url = f"{url}/{path_params}"
        
        # Add query parameters
        if query_params:
            params = urllib.parse.urlencode(query_params)
            url = f"{url}?{params}"
        
        return url

# Usage
api = EPMwareAPI("https://demo.epmwarecloud.com")
url = api.build_url("task", "get_status", path_params="244591")
print(url)  # https://demo.epmwarecloud.com/service/api/task/get_status/244591
```

**JavaScript Example:**
```javascript
class EPMwareAPI {
    constructor(baseUrl) {
        this.baseUrl = baseUrl.replace(/\/$/, '');
    }
    
    buildUrl(module, action, pathParams = null, queryParams = null) {
        let url = `${this.baseUrl}/service/api/${module}/${action}`;
        
        // Add path parameters
        if (pathParams) {
            url = `${url}/${pathParams}`;
        }
        
        // Add query parameters
        if (queryParams) {
            const params = new URLSearchParams(queryParams);
            url = `${url}?${params.toString()}`;
        }
        
        return url;
    }
}

// Usage
const api = new EPMwareAPI('https://demo.epmwarecloud.com');
const url = api.buildUrl('export', 'get_execution_details', null, {
    name: 'PBCS',
    latest_only: 'Y'
});
console.log(url);
```

## Common URL Mistakes

### âŒ Incorrect Examples

```bash
# Missing /service/api
https://demo.epmwarecloud.com/task/get_status/244591

# Wrong module name
https://demo.epmwarecloud.com/service/api/tasks/get_status/244591

# Missing action
https://demo.epmwarecloud.com/service/api/task/244591

# Extra slashes
https://demo.epmwarecloud.com//service/api/task/get_status/244591
```

### âœ… Correct Examples

```bash
# Proper structure
https://demo.epmwarecloud.com/service/api/task/get_status/244591

# With query parameters
https://demo.epmwarecloud.com/service/api/security/users?userType=NATIVE

# With multiple query parameters
https://demo.epmwarecloud.com/service/api/export/get_execution_details?name=PBCS&latest_only=Y
```

## Testing URLs

### Quick URL Validation

Use this script to validate your API URLs:

```bash
#!/bin/bash

validate_url() {
    local url=$1
    local token=$2
    
    echo "Testing: $url"
    
    response=$(curl -s -o /dev/null -w "%{http_code}" \
        -H "Authorization: Token $token" \
        "$url")
    
    if [ "$response" = "200" ]; then
        echo "âœ… URL is valid"
    elif [ "$response" = "401" ]; then
        echo "âš ï¸ URL valid but authentication failed"
    elif [ "$response" = "404" ]; then
        echo "âŒ URL not found - check module/action"
    else
        echo "âŒ Unexpected response: $response"
    fi
}

# Test your URL
validate_url "https://demo.epmwarecloud.com/service/api/task/get_status/244591" "your-token"
```

## URL Reference Table

Quick reference for all available endpoints:

| Module | Action | Method | URL Pattern |
|--------|--------|--------|-------------|
| task | get_status | GET | `/service/api/task/get_status/{task_id}` |
| task | get_log_file | GET | `/service/api/task/get_log_file/{task_id}` |
| erp | upload_file | POST | `/service/api/erp/upload_file` |
| erp | run | POST | `/service/api/erp/run` |
| deployment | run | POST | `/service/api/deployment/run` |
| deployment | get_execution_details | GET | `/service/api/deployment/get_execution_details` |
| export | run | POST | `/service/api/export/run` |
| export | download_file | GET | `/service/api/export/download_file/{task_id}` |
| export | get_execution_details | GET | `/service/api/export/get_execution_details` |
| security | create | POST | `/service/api/security/users/sso/create` |
| security | update | PUT | `/service/api/security/users/sso/update/{username}` |
| security | disable | PATCH | `/service/api/security/users/sso/disable/{username}` |
| security | enable | PATCH | `/service/api/security/users/sso/enable/{username}` |
| security | assign_groups | POST | `/service/api/security/users/sso/assign_groups` |
| security | unassign_groups | POST | `/service/api/security/users/sso/unassign_groups` |
| security | users | GET | `/service/api/security/users` |
| security | groups | GET | `/service/api/security/groups` |

## Next Steps

- [Authentication](authentication.md) - Set up API authentication
- [Response Formats](response-formats.md) - Understand API responses
- [Error Handling](error-handling.md) - Handle API errors effectively