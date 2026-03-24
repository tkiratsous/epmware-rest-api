# Get Groups

Retrieve all security groups present in the EMPWare application.

## Endpoint Details

```http
http(s)://<EPMWARE_URL>/service/api/security/groups
```

### Method

`GET`

## Query Parameters

No parameters required.

## Request Examples

### Using curl

```bash

curl GET 'https://demo.epmwarecloud.com/service/api/security/groups' \
  -H 'authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb9'

```

## Response Format

### Successful Response

```json
[
    {
        "name": "Approver",
        "description": "Approver Group",
        "enable": "Y"
    },
    {
        "name": "Reviewer",
        "description": "Reviewer Group",
        "enable": "Y"
    },
    {
        "name": "Requestor",
        "enable": "Requestor"
    }
]
```
---

[← Back to Group Management](../) | [Get Group Users](../get-group-users/)
