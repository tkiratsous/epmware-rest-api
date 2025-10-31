# Unassign Groups

Remove users from security groups in EPMware.

## Endpoint Details

```http
POST /service/api/security/users/sso/unassign_groups
```

## Request Body

```json
{
  "userName": "JSMITH",
  "groups": [
    "FIN_ANALYSTS",
    "REPORT_VIEWERS"
  ]
}
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userName` | String | Yes | Username to remove from groups |
| `groups` | Array | Yes | List of group names to unassign |

## Response

### Success Response (200 OK)

```json
{
  "status": "S",
  "message": "Groups unassigned successfully",
  "data": {
    "userName": "JSMITH",
    "removedGroups": ["FIN_ANALYSTS", "REPORT_VIEWERS"],
    "remainingGroups": ["BASIC_USERS"]
  }
}
```

## Examples

```python
def unassign_user_groups(username, groups, token):
    url = "https://demo.epmwarecloud.com/service/api/security/users/sso/unassign_groups"
    headers = {"Authorization": f"Token {token}"}
    
    payload = {
        "userName": username,
        "groups": groups
    }
    
    response = requests.post(url, json=payload, headers=headers)
    return response.json()
```

---

[← Back to Group Management](../) | [Get Groups](../get-groups/)
