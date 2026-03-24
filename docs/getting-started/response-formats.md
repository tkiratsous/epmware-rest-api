# Response Formats

Understanding EPMware REST API response formats is crucial for properly handling API responses and implementing robust error handling in your applications.

## Overview

All EPMware REST API responses follow consistent JSON formatting patterns, making it easy to parse and process responses across different endpoints.

## Standard Response Structure

### Successful Response

Most successful API responses include these common fields:

```json
{
  "status": "S",
  "message": "Operation completed successfully",
  "data": {
    // Response-specific data
  }
}
```



## Response Examples


Immediate responses for operations that complete instantly:

```json
{
    "status": "S",
    "taskId": "11442",
    "message": "Export ID : [2473] - Export Complete",
    "percentCompleted": "100"
}
```


Error Response 

```json
{
    "status": "E",
    "taskId": "0",
    "message": "Error: Invalid Export Profile Name : [TEST_CVE_EXPORT55]. "
}
```


## Status Codes

### HTTP Status Codes

| Code | Meaning | When Used |
|------|---------|-----------|
| `200` | OK | Successful GET, PUT, PATCH |
| `201` | Created | Successful POST creating resource |
| `204` | No Content | Successful DELETE |
| `400` | Bad Request | Invalid parameters or request body |
| `401` | Unauthorized | Missing or invalid authentication |
| `403` | Forbidden | Insufficient permissions |
| `404` | Not Found | Resource doesn't exist |
| `409` | Conflict | Resource conflict (e.g., duplicate) |
| `500` | Internal Server Error | Server-side error |
| `503` | Service Unavailable | Service temporarily unavailable |

### Application Status Codes

Within the response body, the `status` field indicates:

| Code | Meaning | Description |
|------|---------|-------------|
| `S` | Success | Operation completed successfully |
| `E` | Error | Operation failed |





## Next Steps

- [API Reference](../reference/) - Complete endpoint documentation
- [Examples](../examples/) - See response handling in action