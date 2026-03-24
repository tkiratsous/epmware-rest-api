# Get Users Groups

This action allows to fetch the list of security groups assigned to the given user.

## Endpoint Details

### URL Structure



```
http(s)://<EPMWARE_URL>/service/api/security/users/groups?userName=<user_name>
```

### Method

`GET`

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `userName` | Query | Yes | All | Provide username to fetch asisgned security groups |


## Request Examples

### Using curl

```bash

curl GET 'https://demo.epmwarecloud.com/service/api/security/users/groups?userName=AKSHAY' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

```

## Response Format

### Successful Response

```json
{
    "userName": "AKSHAY",
    "groups": [
        "Approver",
        "Reviewer"
    ]
}
```

