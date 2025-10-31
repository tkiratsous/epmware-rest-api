# Get Group Users

Retrieve all users assigned to a specific security group.

## Endpoint Details

```http
GET /service/api/security/groups/users
```

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `groupName` | String | Yes | Name of the group |
| `includeDisabled` | Boolean | No | Include disabled users |

## Response Format

```json
{
  "status": "S",
  "groupName": "FIN_ANALYSTS",
  "users": [
    {
      "userName": "JSMITH",
      "email": "jsmith@company.com",
      "fullName": "John Smith",
      "status": "ACTIVE",
      "lastLogin": "2025-01-15T09:00:00Z"
    },
    {
      "userName": "MJONES",
      "email": "mjones@company.com",
      "fullName": "Mary Jones",
      "status": "ACTIVE",
      "lastLogin": "2025-01-14T14:30:00Z"
    }
  ],
  "totalCount": 2
}
```

## Examples

```python
def get_group_members(group_name, token):
    url = f"https://demo.epmwarecloud.com/service/api/security/groups/users?groupName={group_name}"
    headers = {"Authorization": f"Token {token}"}
    
    response = requests.get(url, headers=headers)
    return response.json()

# Get all members of FIN_ANALYSTS group
members = get_group_members("FIN_ANALYSTS", "your-token")
for user in members['users']:
    print(f"{user['userName']} - {user['fullName']}")
```

---

[← Back to Group Management](../) | [Security Module](../../)
