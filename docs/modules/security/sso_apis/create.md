# Create SSO User

The Create SSO User API allows you to programmatically create new Single Sign-On (SSO) users in EPMware. 

!!! info "SSO vs Native Users"
    This API creates SSO users only. Native users must be created through the EPMware UI.

## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/security/users/sso/create
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter  | Required | Description | Example |
|-----------|----------|-------------|---------|
| `userName`  | Yes | Unique username | `SSO_USER1` |
| `firstName`  | Yes | User's first name | `John` |
| `lastName`  | No | User's last name | `Smith` |
| `email`  | Yes | Email address | `john.smith@example.com` |
| `description`  | No | User description | `Finance department user` |

## Request Examples

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/security/users/sso/create' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "SSO_USER1",
    "firstName": "SSO",
    "lastName": "USER1",
    "email": "SSO_USER1@example.com",
    "description": "New user creation"
  }'
```



## Response Format

### Successful Response

```json
{
  "id": "586",
  "createDate": "2025-06-06T13:13:54Z",
  "updateDate": "2025-06-06T13:13:54Z",
  "createdBy": "330",
  "updatedBy": "330",
  "userName": "SSO_USER1",
  "firstName": "SSO",
  "lastName": "USER1",
  "email": "SSO_USER1@example.com",
  "description": "New user creation",
  "enable": "Y",
  "samlEnabled": "false"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | Unique user ID |
| `createDate` | DateTime | User creation timestamp |
| `updateDate` | DateTime | Last update timestamp |
| `createdBy` | String | ID of user who created this user |
| `updatedBy` | String | ID of user who last updated |
| `userName` | String | Username |
| `firstName` | String | First name |
| `lastName` | String | Last name |
| `email` | String | Email address |
| `description` | String | User description |
| `enable` | String | Enable status (Y/N) |
| `samlEnabled` | String | SAML status |

## Response Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `200` | User successfully created | Continue with user setup |
| `401` | Unauthorized - Invalid token | Verify authentication |



#

## Related Operations

- [Update User](update.md) - Modify existing user details
- [Disable User](disable.md) - Manage user status
- [Enable User](enable.md) - Manage user status
- [Assign Groups](assign-groups.md) - Add users to security groups
- [Unassign Groups](unassign-groups.md) - Remove users from security groups


