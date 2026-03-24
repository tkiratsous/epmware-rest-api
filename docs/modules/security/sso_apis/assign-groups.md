# Assign Groups To SSO User

The Assign Groups API allows you to add SSO users to one or more security groups in EPMware, granting them the associated permissions and access rights.


!!! info "Group Assignment"
    - Users can belong to multiple groups simultaneously
    - Group permissions are cumulative - users get all permissions from all assigned groups
    - Changes take effect immediately upon successful assignment

## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/security/users/sso/assign_groups
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userName` | String | Yes | Username to assign to groups |
| `groupNames` | Array | Yes | List of group names to assign |

## Request Examples

Add SSO user “SSO_USER1” to Security groups: Approver, Reviewer, Admin

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/security/users/sso/assign_groups' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "SSO_USER1",
    "groupNames": ["Approver", "Reviewer", "Admin"]
  }'
```


## Response Format

### Successful Response

```json
{
  "value": "User successfully assigned to the specified groups"
}
```


## Response Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `200` | Groups assigned successfully | Operation complete |
| `401` | Unauthorized - Invalid token | Verify authentication |



## Related Operations

- [Unassign Groups](unassign-groups.md) - Remove users from groups
- [Create User](create.md) - Create new users
- [Update User](update.md) - Modify user details
- [Assign Groups](assign-groups.md) - Manage user groups

