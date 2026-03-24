# Disable SSO User

The APIs allow you to manage SSO user account status in EPMware, controlling user access without deleting accounts.

## Overview

These endpoints provide a way to temporarily or permanently control user access to EPMware. Disabled users cannot log in but their data and configurations are preserved.

    

## Disable User

### Endpoint Details

#### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/security/users/sso/disable/<UserName>
```

#### Method

`PATCH`

#### Parameters

| Parameter  | Required | Description |
|-----------|----------|-------------|
| `USERNAME` | Yes | Username to disable |

### Request Examples

#### Using curl

```bash
curl --request PATCH 'https://demo.epmwarecloud.com/service/api/security/users/sso/disable/SSO_USER1' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

### Response Format

#### Successful Response

```json
{
  "value": "User status disabled successfully"
}
```

## Response Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `200` | Status changed successfully | Operation complete |
| `401` | Unauthorized - Invalid token | Verify authentication |

#
## Related Operations

- [Create User](create.md) - Create new users
- [Update User](update.md) - Modify user details
- [Assign Groups](assign-groups.md) - Manage user groups

