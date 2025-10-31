# Parameters Reference

Comprehensive guide to all parameters used across EPMware REST API endpoints.

## Parameter Types

### Path Parameters

Included in the URL path:
```
/service/api/task/get_status/{task_id}
```

### Query Parameters

Appended to the URL:
```
/service/api/export/get_execution_details?name=Report&latest_only=Y
```

### Body Parameters

Sent in the request body as JSON:
```json
{
  "userName": "JSMITH",
  "email": "jsmith@company.com"
}
```

## Common Parameters

### Authentication

| Parameter | Type | Location | Required | Description |
|-----------|------|----------|----------|-------------|
| `Authorization` | String | Header | Yes | Token authentication: `Token <token>` |

### Pagination

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `page` | Integer | 1 | Page number |
| `pageSize` | Integer | 100 | Items per page |
| `sortBy` | String | - | Sort field |
| `sortOrder` | String | ASC | Sort order (ASC/DESC) |

### Filtering

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `startDate` | Date | Filter from date | 2025-01-01 |
| `endDate` | Date | Filter to date | 2025-01-31 |
| `status` | String | Filter by status | SUCCESS |
| `name` | String | Filter by name | Report_* |

## Module-Specific Parameters

### Task Module

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `task_id` | Yes | String | Unique task identifier |
| `includeLog` | No | Boolean | Include log in response |

### ERP Module

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `profileName` | Yes | String | Import profile name |
| `file` | Yes | Binary | File to upload |
| `validate` | No | Boolean | Validate before import |

### Export Module

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `name` | Yes | String | Export name |
| `format` | No | String | Output format |
| `compress` | No | Boolean | Compress output |
| `latest_only` | No | String | Return latest only (Y/N) |

### Security Module

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `userName` | Yes | String | Username |
| `email` | Conditional | String | Email address |
| `groups` | No | Array | Security groups |
| `enabled` | No | Boolean | User status |

---

[← Back to Reference](../) | [Response Codes →](../response-codes/)
