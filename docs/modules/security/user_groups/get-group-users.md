# Get Group Users

Retrieve all users assigned to a specific security group.

## Endpoint Details

### URL Structure
```http
## Use URL below to retrieve all security groups along with their assigned list of users.
http(s)://<EPMWARE_URL>/service/api/security/groups/users

## Use URL below to retrieve list of users assigned to passed security group.
http(s)://<EPMWARE_URL>/service/api/security/groups/users?groupName=<group_name>

```

### Method

`GET`


## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `groupName` | String | Optional | Name of the group |


## Examples

Get list of users assigned to Approver group.

```bash
curl GET 'https://demo.epmwarecloud.com/service/api/security/groups/users?groupName=APPROVER' \
  -H 'authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb9'
```


## Response Format

```json
[
    {
        "groupName": "Admins",
        "users": [
            "ADMIN",
            "TONY",
            "DEVEN"
        ]
    }
]
```


---

[← Back to Group Management](../) | [Security Module](../../)
