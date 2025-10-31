# Update User

Update existing SSO user details in EPMware.

## Endpoint Details

```http
PUT /service/api/security/users/sso/update/{username}
```

## Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `username` | String | Yes | Username to update |

## Request Body

```json
{
  "email": "updated.email@company.com",
  "firstName": "John",
  "lastName": "Smith",
  "displayName": "John Smith",
  "department": "Finance",
  "title": "Senior Analyst",
  "manager": "MANAGER01",
  "phoneNumber": "+1-555-0100",
  "location": "New York",
  "attributes": {
    "costCenter": "CC100",
    "employeeId": "EMP12345"
  }
}
```

## Response

### Success Response (200 OK)

```json
{
  "status": "S",
  "message": "User updated successfully",
  "data": {
    "userName": "JSMITH",
    "email": "updated.email@company.com",
    "lastModified": "2025-01-15T10:30:00Z"
  }
}
```

## Examples

### Python Example

```python
import requests

def update_user(username, updates, token):
    url = f"https://demo.epmwarecloud.com/service/api/security/users/sso/update/{username}"
    headers = {
        "Authorization": f"Token {token}",
        "Content-Type": "application/json"
    }
    
    response = requests.put(url, json=updates, headers=headers)
    return response.json()

# Usage
updates = {
    "email": "john.smith@newdomain.com",
    "title": "Finance Manager",
    "department": "Corporate Finance"
}

result = update_user("JSMITH", updates, "your-token")
```

---

[← Back to Security Module](../../) | [Enable/Disable Users](../enable-disable/)
