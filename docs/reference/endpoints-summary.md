# Endpoints Summary

Complete reference of all EPMware REST API endpoints, organized by module with quick access to methods, parameters, and responses.

## Quick Reference Table

| Module | Endpoint | Method | Description |
|--------|----------|--------|-------------|
| **Task** | `/service/api/task/get_status/{task_id}` | GET | Get task status |
| **Task** | `/service/api/task/get_log_file/{task_id}` | GET | Download task log |
| **ERP** | `/service/api/erp/upload_file` | POST | Upload file for import |
| **ERP** | `/service/api/erp/run` | POST | Execute ERP import |
| **Deployment** | `/service/api/deployment/run` | POST | Execute deployment |
| **Deployment** | `/service/api/deployment/get_execution_details` | GET | Get execution history |
| **Export** | `/service/api/export/run` | POST | Execute export |
| **Export** | `/service/api/export/download_file/{task_id}` | GET | Download export file |
| **Export** | `/service/api/export/get_execution_details` | GET | Get execution history |
| **Security** | `/service/api/security/users/sso/create` | POST | Create SSO user |
| **Security** | `/service/api/security/users/sso/update/{username}` | PUT | Update user |
| **Security** | `/service/api/security/users/sso/enable/{username}` | PATCH | Enable user |
| **Security** | `/service/api/security/users/sso/disable/{username}` | PATCH | Disable user |
| **Security** | `/service/api/security/users/sso/assign_groups` | POST | Assign groups |
| **Security** | `/service/api/security/users/sso/unassign_groups` | POST | Remove groups |
| **Security** | `/service/api/security/users` | GET | List users |
| **Security** | `/service/api/security/groups` | GET | List groups |
| **Security** | `/service/api/security/users/groups` | GET | Get user groups |
| **Security** | `/service/api/security/groups/users` | GET | Get group users |

## Task Module Endpoints

### GET /service/api/task/get_status/{task_id}

Get the current status of a task.

**Parameters:**
- `task_id` (path, required): Task identifier

**Response:**
```json
{
  "taskId": "244591",
  "status": "S",
  "percentCompleted": "100",
  "message": "Task completed successfully"
}
```

**Status Codes:**
- `200`: Success
- `404`: Task not found
- `401`: Unauthorized

---

### GET /service/api/task/get_log_file/{task_id}

Retrieve the log file for a task.

**Parameters:**
- `task_id` (path, required): Task identifier

**Response:**
- Content-Type: `text/plain`
- Body: Log file content

**Status Codes:**
- `200`: Success
- `404`: Log not found
- `401`: Unauthorized

## ERP Module Endpoints

### POST /service/api/erp/upload_file

Upload a file for ERP import processing.

**Headers:**
- `Content-Type: multipart/form-data`

**Parameters:**
- `file` (form-data, required): File to upload
- `name` (form-data, required): ERP Import configuration name
- `delimiter_char` (form-data, required): Column delimiter
- `enclosing_char` (form-data, optional): Field enclosing character

**Response:**
```json
{
  "status": "S",
  "taskId": "244659",
  "message": "File uploaded successfully"
}
```

**Status Codes:**
- `200`: Success
- `400`: Bad request
- `404`: Configuration not found
- `413`: File too large

---

### POST /service/api/erp/run

Execute an ERP import process.

**Headers:**
- `Content-Type: application/json`

**Request Body:**
```json
{
  "name": "ASOALL_Account",
  "timeout_min": 60
}
```

**Parameters:**
- `name` (required): ERP Import configuration name
- `timeout_min` (optional, default: 60): Timeout in minutes

**Response:**
```json
{
  "status": "S",
  "taskId": "244659"
}
```

**Status Codes:**
- `200`: Success
- `404`: Configuration not found
- `503`: Service unavailable
- `409`: Import already running

## Deployment Module Endpoints

### POST /service/api/deployment/run

Execute a deployment task.

**Headers:**
- `Content-Type: application/json`

**Request Body:**
```json
{
  "name": "ASOALL",
  "timeout_min": 60
}
```

**Parameters:**
- `name` (required): Deployment configuration name
- `timeout_min` (optional, default: 60): Timeout in minutes

**Response:**
```json
{
  "status": "S",
  "taskId": "244596"
}
```

**Status Codes:**
- `200`: Success
- `404`: Deployment not found
- `503`: Service unavailable
- `409`: Deployment already running

---

### GET /service/api/deployment/get_execution_details

Get deployment execution history.

**Query Parameters:**
- `name` (required): Deployment name
- `latest_only` (optional, default: Y): Return only latest execution

**Response:**
```json
{
  "vEWVDeploymentExecutions": [
    {
      "id": "12345",
      "deploymentName": "ASOALL",
      "startDate": "2025-01-15T02:00:00Z",
      "endDate": "2025-01-15T02:45:30Z",
      "statusMeaning": "Success"
    }
  ]
}
```

**Status Codes:**
- `200`: Success
- `404`: Deployment not found
- `401`: Unauthorized

## Export Module Endpoints

### POST /service/api/export/run

Execute an export profile or book.

**Headers:**
- `Content-Type: application/json`

**Request Body (Profile):**
```json
{
  "name": "PBCS"
}
```

**Request Body (Book):**
```json
{
  "book_name": "PBCS_BOOK"
}
```

**Parameters:**
- `name` (required*): Export profile name
- `book_name` (required*): Export book name

*Either `name` OR `book_name` required

**Response:**
```json
{
  "status": "S",
  "taskId": "244596"
}
```

**Status Codes:**
- `200`: Success
- `404`: Export not found
- `409`: Export already running

---

### GET /service/api/export/download_file/{task_id}

Download the exported file.

**Parameters:**
- `task_id` (path, required): Task identifier

**Response:**
- Content-Type: `application/octet-stream` or specific file type
- Headers: `Content-Disposition: attachment; filename="export.csv"`
- Body: File content

**Status Codes:**
- `200`: Success
- `404`: File not found
- `410`: File expired

---

### GET /service/api/export/get_execution_details

Get export execution history.

**Query Parameters:**
- `name` (required): Export name
- `latest_only` (optional, default: Y): Return only latest execution

**Response:**
```json
{
  "vEWVExportExecutions": [
    {
      "id": "67890",
      "exportName": "PBCS",
      "startDate": "2025-01-15T02:00:00Z",
      "endDate": "2025-01-15T02:15:30Z",
      "outputFileName": "PBCS_export.csv",
      "outputFileSize": 15234567,
      "statusMeaning": "Success"
    }
  ]
}
```

## Security Module Endpoints

### POST /service/api/security/users/sso/create

Create a new SSO user.

**Headers:**
- `Content-Type: application/json`

**Request Body:**
```json
{
  "userName": "SSO_USER1",
  "firstName": "John",
  "lastName": "Smith",
  "email": "john.smith@example.com",
  "description": "Finance user"
}
```

**Required Fields:**
- `userName`: Unique username
- `firstName`: First name
- `email`: Email address

**Response:**
```json
{
  "id": "586",
  "userName": "SSO_USER1",
  "email": "john.smith@example.com",
  "enable": "Y"
}
```

**Status Codes:**
- `200`: Created
- `409`: User already exists
- `400`: Invalid data

---

### PUT /service/api/security/users/sso/update/{username}

Update an existing SSO user.

**Parameters:**
- `username` (path, required): Username to update

**Request Body (all fields optional):**
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "description": "Updated description"
}
```

**Response:**
```json
{
  "id": "586",
  "userName": "SSO_USER1",
  "updateDate": "2025-06-06T14:10:26Z"
}
```

**Status Codes:**
- `200`: Updated
- `404`: User not found
- `400`: Invalid data

---

### PATCH /service/api/security/users/sso/enable/{username}

Enable an SSO user account.

**Parameters:**
- `username` (path, required): Username to enable

**Response:**
```json
{
  "value": "User status enabled successfully"
}
```

**Status Codes:**
- `200`: Enabled
- `404`: User not found

---

### PATCH /service/api/security/users/sso/disable/{username}

Disable an SSO user account.

**Parameters:**
- `username` (path, required): Username to disable

**Response:**
```json
{
  "value": "User status disabled successfully"
}
```

**Status Codes:**
- `200`: Disabled
- `404`: User not found

---

### POST /service/api/security/users/sso/assign_groups

Assign user to security groups.

**Request Body:**
```json
{
  "userName": "SSO_USER1",
  "groupNames": ["Approver", "Reviewer", "Admin"]
}
```

**Response:**
```json
{
  "value": "User successfully assigned to the specified groups"
}
```

**Status Codes:**
- `200`: Success
- `404`: User or group not found

---

### POST /service/api/security/users/sso/unassign_groups

Remove user from security groups.

**Request Body:**
```json
{
  "userName": "SSO_USER1",
  "groupNames": ["Approver", "Reviewer"]
}
```

**Response:**
```json
{
  "value": "User successfully removed from the specified groups"
}
```

---

### GET /service/api/security/users

List all users or filter by type.

**Query Parameters:**
- `userType` (optional): Filter by NATIVE or SAML
- `pageSize` (optional, default: 10000): Results per page

**Response:**
```json
[
  {
    "userName": "john.smith",
    "firstName": "John",
    "lastName": "Smith",
    "email": "john@example.com",
    "enable": "Y",
    "userType": "NATIVE"
  }
]
```

---

### GET /service/api/security/groups

List all security groups.

**Response:**
```json
[
  {
    "name": "Developers",
    "description": "Developer group",
    "enable": "Y"
  }
]
```

---

### GET /service/api/security/users/groups

Get groups assigned to a user.

**Query Parameters:**
- `userName` (required): Username

**Response:**
```json
{
  "userName": "JOHN",
  "groups": ["Developers", "Reviewers"]
}
```

---

### GET /service/api/security/groups/users

Get users in a security group.

**Query Parameters:**
- `groupName` (optional): Specific group name

**Response:**
```json
[
  {
    "groupName": "Approver",
    "users": ["TONY", "DEVEN"]
  }
]
```

## Common Response Formats

### Success Response

```json
{
  "status": "S",
  "message": "Operation completed successfully",
  "data": {}
}
```

### Error Response

```json
{
  "status": "E",
  "message": "Error description",
  "errorCode": "ERR_001",
  "details": {}
}
```

### Task Response

```json
{
  "status": "S",
  "taskId": "244596",
  "message": "Task submitted successfully"
}
```

## HTTP Status Codes

| Code | Meaning | Common Causes |
|------|---------|---------------|
| `200` | OK | Successful operation |
| `201` | Created | Resource created |
| `204` | No Content | Successful with no response body |
| `400` | Bad Request | Invalid parameters |
| `401` | Unauthorized | Missing/invalid token |
| `403` | Forbidden | Insufficient permissions |
| `404` | Not Found | Resource doesn't exist |
| `409` | Conflict | Resource conflict |
| `413` | Payload Too Large | File/request too large |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Server error |
| `503` | Service Unavailable | Service down |

## Rate Limiting

Most endpoints have rate limits:
- Standard endpoints: 100 requests/minute
- File upload endpoints: 10 requests/minute
- Bulk operations: 5 requests/minute

## Authentication

All endpoints require authentication via token in header:

```
Authorization: Token YOUR_TOKEN_HERE
```

## Base URL Format

```
https://{instance}.epmwarecloud.com/service/api/{module}/{action}
```

## Next Steps

- [Parameters Reference](parameters-reference.md) - Detailed parameter documentation
- [Response Codes](response-codes.md) - Complete response code reference
- [Examples](../examples/) - Working code examples