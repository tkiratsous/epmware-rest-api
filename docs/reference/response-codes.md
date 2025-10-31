# Response Codes

Complete reference of HTTP status codes returned by the EPMware REST API.

## Success Codes (2xx)

| Code | Name | Description | Common Scenarios |
|------|------|-------------|------------------|
| **200** | OK | Request succeeded | GET requests, successful updates |
| **201** | Created | Resource created | POST creating new resource |
| **202** | Accepted | Request accepted for processing | Async operations |
| **204** | No Content | Success with no response body | DELETE operations |

## Client Error Codes (4xx)

| Code | Name | Description | Resolution |
|------|------|-------------|------------|
| **400** | Bad Request | Invalid request syntax | Check request format |
| **401** | Unauthorized | Authentication failed | Verify token |
| **403** | Forbidden | Access denied | Check permissions |
| **404** | Not Found | Resource doesn't exist | Verify resource ID |
| **405** | Method Not Allowed | HTTP method not supported | Use correct method |
| **409** | Conflict | Resource conflict | Resolve conflict |
| **422** | Unprocessable Entity | Validation failed | Fix validation errors |
| **429** | Too Many Requests | Rate limit exceeded | Retry after delay |

## Server Error Codes (5xx)

| Code | Name | Description | Action |
|------|------|-------------|--------|
| **500** | Internal Server Error | Server error | Contact support |
| **502** | Bad Gateway | Gateway error | Retry request |
| **503** | Service Unavailable | Service down | Check status page |
| **504** | Gateway Timeout | Request timeout | Retry with smaller payload |

## Response Examples

### 200 OK
```json
{
  "status": "S",
  "message": "Operation successful",
  "data": { ... }
}
```

### 400 Bad Request
```json
{
  "status": "E",
  "message": "Invalid request format",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email format"
    }
  ]
}
```

### 401 Unauthorized
```json
{
  "status": "E",
  "message": "Authentication required",
  "errorCode": "AUTH_001"
}
```

---

[← Back to Reference](../) | [Status Codes →](../status-codes/)
