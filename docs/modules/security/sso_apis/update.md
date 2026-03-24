# Update SSO User

Update existing SSO user details in EPMware.

## Endpoint Details

### URL Structure

```http
http(s)://<EPMWARE_URL>/service/api/security/users/sso/update/<UserName>
```

### Method

`PUT`

## Path Parameters

Include parameters which needs to be updated in json body.

| Parameter  | Required | Description |
|-----------|----------|-------------|
| `username`  | NO | User name |
| `firstName` | NO | First Name of user |
| `lastName`  | NO | Last Name of user|
| `email`  | NO | email id of user |
| `description`  | NO | description details |

## Request Body

To Update details of SSO user , username : “SSO_USER1”

```bash
curl -–request PUT 'http://demo.EPMWAREcloud.com/service/api/security/users/sso/update/SSO_USER1' \
  -H 'authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'content-type: application/json' \
  -d '{"userName" : "SSO_USER1",
       "firstName" : "SSOUPD",
       "LastName" : "USER1UPD",
       "email" : "SSO_USER1_UPDATED@example.com",
       "description" : "Updating Details" 
       }'
```

## Response

### Success Response (200 OK)

```json

  {  
    "id": "586",
    "createDate": "2025-06-06T13:13:54Z",
    "updateDate": "2025-06-06T14:10:26Z",
    "createdBy": "330",
    "updatedBy": "330",
    "userName": "SSO_USER1",
    "firstName": "SSOUPD",
    "lastName": "USER1",
    "email": "SSO_USER1_UPDATED@example.com",
    "description": "Updating Details",
    "enable": "Y",
    "samlEnabled": "false" 
  }
```



---

[← Back to Security Module](../../) | [Enable/Disable Users](../enable-disable/)
