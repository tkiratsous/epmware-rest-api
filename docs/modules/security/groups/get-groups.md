# Get Groups

Retrieve all security groups or groups for a specific user.

## Endpoint Details

```http
GET /service/api/security/groups
```

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userName` | String | No | Get groups for specific user |
| `type` | String | No | Filter by group type |

## Response Format

```json
{
  "status": "S",
  "groups": [
    {
      "groupName": "FIN_ANALYSTS",
      "description": "Financial Analysts",
      "memberCount": 25,
      "createdDate": "2024-01-01",
      "permissions": ["VIEW_REPORTS", "EDIT_DATA"]
    },
    {
      "groupName": "ADMIN_USERS",
      "description": "System Administrators",
      "memberCount": 5,
      "createdDate": "2024-01-01",
      "permissions": ["FULL_ACCESS"]
    }
  ]
}
```

## Examples

```python
def get_all_groups(token):
    url = "https://demo.epmwarecloud.com/service/api/security/groups"
    headers = {"Authorization": f"Token {token}"}
    
    response = requests.get(url, headers=headers)
    return response.json()

def get_user_groups(username, token):
    url = f"https://demo.epmwarecloud.com/service/api/security/groups?userName={username}"
    headers = {"Authorization": f"Token {token}"}
    
    response = requests.get(url, headers=headers)
    return response.json()
```

---

[← Back to Group Management](../) | [Get Group Users](../get-group-users/)
