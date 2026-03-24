# Enable SSO User

The Enable APIs allow to activate or enable existing SSO user account.

## Enable User

### Endpoint Details

#### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/security/users/sso/enable/<UserName>
```

#### Method

`PATCH`

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `USERNAME` | Path | Yes | Username to enable |

### Request Examples

#### Using curl

Enable  SSO user for  username : “SSO_USER1”

```bash
curl --request PATCH 'https://demo.epmwarecloud.com/service/api/security/users/sso/enable/SSO_USER1' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

### Response Format

#### Successful Response

```json
{
  "value": "User status enabled successfully"
}
```


## Related Operations

- [Create User](create.md) - Create new users
- [Update User](update.md) - Modify user details
- [Assign Groups](assign-groups.md) - Manage user groups
