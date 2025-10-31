# Response Formats

Understanding EPMware REST API response formats is crucial for properly handling API responses and implementing robust error handling in your applications.

## Overview

All EPMware REST API responses follow consistent JSON formatting patterns, making it easy to parse and process responses across different endpoints.

## Standard Response Structure

### Successful Response

Most successful API responses include these common fields:

```json
{
  "status": "S",
  "message": "Operation completed successfully",
  "data": {
    // Response-specific data
  }
}
```

### Common Response Fields

| Field | Type | Description | Present In |
|-------|------|-------------|------------|
| `status` | String | Operation status code | All responses |
| `message` | String | Human-readable message | All responses |
| `taskId` | String | Task identifier | Async operations |
| `data` | Object/Array | Response payload | Query operations |

## Response Types

### 1. Synchronous Responses

Immediate responses for operations that complete instantly:

```json
{
  "status": "S",
  "message": "User created successfully",
  "data": {
    "id": "586",
    "userName": "JSMITH",
    "email": "jsmith@example.com"
  }
}
```

### 2. Asynchronous Responses

For long-running operations that return a task ID:

```json
{
  "status": "S",
  "taskId": "244591",
  "message": "Task submitted successfully"
}
```

### 3. Collection Responses

When returning multiple items:

```json
{
  "status": "S",
  "count": 25,
  "data": [
    {
      "id": "1",
      "name": "Item 1"
    },
    {
      "id": "2",
      "name": "Item 2"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 25,
    "totalPages": 4,
    "totalCount": 100
  }
}
```

### 4. Binary Responses

For file downloads (Export module):

- **Content-Type**: `application/octet-stream` or specific file type
- **Content-Disposition**: Contains filename
- **Body**: Raw file bytes

## Status Codes

### HTTP Status Codes

| Code | Meaning | When Used |
|------|---------|-----------|
| `200` | OK | Successful GET, PUT, PATCH |
| `201` | Created | Successful POST creating resource |
| `204` | No Content | Successful DELETE |
| `400` | Bad Request | Invalid parameters or request body |
| `401` | Unauthorized | Missing or invalid authentication |
| `403` | Forbidden | Insufficient permissions |
| `404` | Not Found | Resource doesn't exist |
| `409` | Conflict | Resource conflict (e.g., duplicate) |
| `500` | Internal Server Error | Server-side error |
| `503` | Service Unavailable | Service temporarily unavailable |

### Application Status Codes

Within the response body, the `status` field indicates:

| Code | Meaning | Description |
|------|---------|-------------|
| `S` | Success | Operation completed successfully |
| `E` | Error | Operation failed |
| `N` | New/In Progress | Task is queued or running |
| `W` | Warning | Operation completed with warnings |

## Module-Specific Responses

### Task Module

#### Get Status Response

```json
{
  "taskId": "244591",
  "status": "S",
  "percentCompleted": "100",
  "message": "Export completed successfully",
  "startTime": "2025-01-15T10:30:00Z",
  "endTime": "2025-01-15T10:35:23Z"
}
```

### ERP Module

#### Upload File Response

```json
{
  "status": "S",
  "taskId": "244659",
  "message": "File uploaded successfully",
  "fileInfo": {
    "fileName": "accounts.csv",
    "fileSize": 15234,
    "recordCount": 450
  }
}
```

### Security Module

#### Create User Response

```json
{
  "id": "586",
  "createDate": "2025-06-06T13:13:54Z",
  "updateDate": "2025-06-06T13:13:54Z",
  "createdBy": "330",
  "updatedBy": "330",
  "userName": "SSO_USER1",
  "firstName": "John",
  "lastName": "Smith",
  "email": "jsmith@example.com",
  "description": "Finance user",
  "enable": "Y",
  "samlEnabled": "false"
}
```

### Deployment Module

#### Execution Details Response

```json
{
  "deploymentName": "PROD_DEPLOYMENT",
  "executions": [
    {
      "executionId": "12345",
      "status": "SUCCESS",
      "startTime": "2025-01-15T02:00:00Z",
      "endTime": "2025-01-15T02:45:30Z",
      "duration": 2730,
      "recordsProcessed": 15000,
      "errors": 0,
      "warnings": 3
    }
  ]
}
```

## Parsing Responses

### JavaScript/Node.js

```javascript
async function parseApiResponse(response) {
  // Check HTTP status first
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }
  
  // Parse JSON
  const data = await response.json();
  
  // Check application status
  if (data.status === 'E') {
    throw new Error(data.message || 'Operation failed');
  }
  
  // Handle warnings
  if (data.status === 'W') {
    console.warn('Warning:', data.message);
  }
  
  return data;
}

// Usage
fetch(url, options)
  .then(parseApiResponse)
  .then(data => {
    console.log('Success:', data);
  })
  .catch(error => {
    console.error('Error:', error);
  });
```

### Python

```python
import requests
import json

def parse_api_response(response):
    """Parse and validate API response"""
    
    # Check HTTP status
    response.raise_for_status()
    
    # Handle different content types
    content_type = response.headers.get('Content-Type', '')
    
    if 'application/json' in content_type:
        data = response.json()
        
        # Check application status
        if data.get('status') == 'E':
            raise Exception(f"API Error: {data.get('message', 'Unknown error')}")
        
        # Handle warnings
        if data.get('status') == 'W':
            print(f"Warning: {data.get('message')}")
        
        return data
    
    elif 'application/octet-stream' in content_type:
        # Binary data (file download)
        return response.content
    
    else:
        # Plain text or other
        return response.text

# Usage
try:
    response = requests.get(url, headers=headers)
    data = parse_api_response(response)
    
    if isinstance(data, dict):
        print(f"Task ID: {data.get('taskId')}")
        print(f"Status: {data.get('status')}")
    
except requests.HTTPError as e:
    print(f"HTTP Error: {e}")
except Exception as e:
    print(f"API Error: {e}")
```

### PowerShell

```powershell
function Parse-EPMwareResponse {
    param(
        [Parameter(Mandatory=$true)]
        $Response
    )
    
    # PowerShell automatically parses JSON with Invoke-RestMethod
    # but we need to check the application status
    
    if ($Response.status -eq 'E') {
        throw "API Error: $($Response.message)"
    }
    
    if ($Response.status -eq 'W') {
        Write-Warning "API Warning: $($Response.message)"
    }
    
    return $Response
}

# Usage
try {
    $response = Invoke-RestMethod @params
    $data = Parse-EPMwareResponse -Response $response
    
    Write-Host "Success: $($data.message)"
    
} catch {
    Write-Error "Failed: $_"
}
```

## Special Response Handling

### Pagination

When dealing with paginated responses:

```python
def get_all_pages(base_url, headers):
    """Retrieve all pages of results"""
    all_data = []
    page = 1
    
    while True:
        response = requests.get(
            f"{base_url}?page={page}&pageSize=100",
            headers=headers
        )
        
        data = response.json()
        all_data.extend(data['data'])
        
        if page >= data['pagination']['totalPages']:
            break
        
        page += 1
    
    return all_data
```

### Streaming Large Responses

For large file downloads:

```python
def download_large_file(url, headers, output_path):
    """Stream download large files"""
    
    with requests.get(url, headers=headers, stream=True) as response:
        response.raise_for_status()
        
        total_size = int(response.headers.get('Content-Length', 0))
        
        with open(output_path, 'wb') as file:
            downloaded = 0
            chunk_size = 8192
            
            for chunk in response.iter_content(chunk_size=chunk_size):
                file.write(chunk)
                downloaded += len(chunk)
                
                # Progress indicator
                if total_size > 0:
                    percent = (downloaded / total_size) * 100
                    print(f"Progress: {percent:.1f}%", end='\r')
    
    print(f"\nDownload complete: {output_path}")
```

### Handling Empty Responses

Some endpoints may return empty responses:

```javascript
function handleResponse(response) {
  // Check for 204 No Content
  if (response.status === 204) {
    return { success: true, message: 'Operation completed' };
  }
  
  // Check for empty body
  const contentLength = response.headers.get('Content-Length');
  if (contentLength === '0') {
    return { success: true, message: 'No data returned' };
  }
  
  return response.json();
}
```

## Content Types

### Supported Response Types

| Content-Type | Description | Used By |
|--------------|-------------|---------|
| `application/json` | JSON data | Most endpoints |
| `text/plain` | Plain text | Log files |
| `application/octet-stream` | Binary data | File downloads |
| `text/csv` | CSV data | Export files |
| `application/zip` | Compressed files | Bulk exports |

### Content Negotiation

Some endpoints support multiple formats:

```bash
# Request JSON response
curl -H "Accept: application/json" $URL

# Request CSV response
curl -H "Accept: text/csv" $URL
```

## Response Validation

### Schema Validation Example

```python
from jsonschema import validate, ValidationError

# Define expected schema
task_status_schema = {
    "type": "object",
    "properties": {
        "taskId": {"type": "string"},
        "status": {"type": "string", "enum": ["N", "S", "E"]},
        "percentCompleted": {"type": "string"},
        "message": {"type": "string"}
    },
    "required": ["taskId", "status", "percentCompleted"]
}

def validate_response(data, schema):
    """Validate response against schema"""
    try:
        validate(instance=data, schema=schema)
        return True
    except ValidationError as e:
        print(f"Response validation failed: {e}")
        return False

# Usage
response_data = {
    "taskId": "244591",
    "status": "S",
    "percentCompleted": "100",
    "message": "Completed"
}

if validate_response(response_data, task_status_schema):
    print("Response is valid")
```

## Best Practices

### 1. Always Check Both Status Levels

```python
# Check HTTP status AND application status
if response.status_code == 200:
    data = response.json()
    if data['status'] == 'S':
        # Success
        process_data(data)
    else:
        # Application-level error
        handle_error(data['message'])
else:
    # HTTP-level error
    handle_http_error(response.status_code)
```

### 2. Handle Timeouts Gracefully

```python
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

def create_session():
    session = requests.Session()
    retry = Retry(
        total=3,
        read=3,
        connect=3,
        backoff_factor=0.3,
        status_forcelist=(500, 502, 504)
    )
    adapter = HTTPAdapter(max_retries=retry)
    session.mount('http://', adapter)
    session.mount('https://', adapter)
    return session
```

### 3. Log Response Details

```python
import logging

def log_response(response):
    logging.info(f"Status Code: {response.status_code}")
    logging.info(f"Headers: {response.headers}")
    
    if response.headers.get('Content-Type', '').startswith('application/json'):
        logging.info(f"Response Body: {response.json()}")
    else:
        logging.info(f"Response Length: {len(response.content)} bytes")
```

## Troubleshooting Response Issues

### Common Problems and Solutions

| Problem | Possible Cause | Solution |
|---------|---------------|----------|
| Empty response body | 204 status or no content | Check status code first |
| JSON parse error | Invalid JSON or wrong content type | Check Content-Type header |
| Encoding issues | Character encoding mismatch | Specify encoding explicitly |
| Truncated response | Network timeout or size limit | Use streaming for large responses |
| Unexpected format | API version mismatch | Verify API version and endpoint |

## Next Steps

- [Error Handling](error-handling.md) - Handle errors effectively
- [API Reference](../reference/) - Complete endpoint documentation
- [Examples](../examples/) - See response handling in action