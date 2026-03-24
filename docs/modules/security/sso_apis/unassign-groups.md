# Unassign Groups from SSO User

Remove users from security groups in EPMware.

## Endpoint Details


### URL Structure

```http
http(s)://<EPMWARE_URL>/service/api/security/users/sso/unassign_groups
```

### Method

`POST`




## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userName` | String | Yes | Username to remove from groups |
| `groups` | Array | Yes | List of group names to unassign |

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

## Request Examples


Remove  SSO user “SSO_USER1” from security groups : Approver, Reviewer

```bash

curl POST 'https://demo.epmwarecloud.com/service/api/security/users/sso/unassign_groups' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "SSO_USER1",
    "groupNames": ["Approver", "Reviewer"]
  }'
```


## Response


```json
{
    "value": "User successfully removed from the specified groups."
}

```

---

[← Back to Group Management](../) | [Get Groups](../get-groups/)
