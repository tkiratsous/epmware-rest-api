# Get Users

The Get Users API allows you to retrieve information about all users or filter by user type (NATIVE or SAML) in EPMware.

## Overview

This endpoint provides a way to list all users in the system with their details, including username, name, email, status, and type. It's useful for user management, auditing, and reporting purposes.

## Endpoint Details

### URL Structure



```
## To get details of all the users
http(s)://<EPMWARE_URL>/service/api/security/users

## To get the details of only Native users:
http(s)://<EPMWARE_URL>/service/api/security/users?userType=NATIVE

## To get the details of SAML users:
http(s)://<EPMWARE_URL>/service/api/security/users?userType=SAML

```

### Method

`GET`

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `userType` | Query | No | All | Filter by user type (NATIVE or SAML) |
| `pageSize` | Query | No | 10000 | Number of results per page |

## Request Examples

### Using curl

```bash
# Get all users
curl GET 'https://demo.epmwarecloud.com/service/api/security/users' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

# Get only NATIVE users
curl GET 'https://demo.epmwarecloud.com/service/api/security/users?userType=NATIVE' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

# Get only SAML users
curl GET 'https://demo.epmwarecloud.com/service/api/security/users?userType=SAML' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

# Get with custom page size
curl GET 'https://demo.epmwarecloud.com/service/api/security/users?pageSize=500' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

## Response Format

### Successful Response

```json
[
  {
    "userName": "john.smith",
    "firstName": "John",
    "lastName": "Smith",
    "email": "john.smith@example.com",
    "enable": "Y",
    "userType": "NATIVE",
    "description": "Finance department user"
  },
  {
    "userName": "jane.doe",
    "firstName": "Jane",
    "lastName": "Doe",
    "email": "jane.doe@example.com",
    "enable": "Y",
    "userType": "SAML",
    "description": "IT administrator"
  }
]
```

