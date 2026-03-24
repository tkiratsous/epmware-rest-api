# URL Structure

Understanding the EPMware REST API URL structure is essential for making successful API calls. 

## Base URL Format

All EPMware REST API endpoints follow this pattern:

```
https://<EPMWARE_URL>/service/api/<module>/<action>
```

The EPMWARE_URL is different for every client and every instance. 



## Module Endpoints

### Available Modules

| Module | Purpose | Base Path |
|--------|---------|-----------|
| `task` | Task management | `/service/api/task` |
| `erp` | ERP operations | `/service/api/erp` |
| `deployment` | Deployment execution | `/service/api/deployment` |
| `export` | Export operations | `/service/api/export` |
| `security` | User & Group management | `/service/api/security` |

## URL Examples

### Task Module

```bash
# Get task status
https://development.epmwarecloud.com/service/api/task/get_status/11442

# Get task log file
https://development.epmwarecloud.com/service/api/task/get_log_file/10754
```


## Path Parameters vs Query Parameters

### Path Parameters

Used in the URL path to identify specific resources:

```
/service/api/task/get_status/{TASK_ID}
                              ^^^^^^^^
                           Path Parameter
```

**Example:**
```bash
https://demo.epmwarecloud.com/service/api/task/get_status/244591
```

### Query Parameters

Added after `?` for filtering or options:

```
/service/api/security/users?userType=SAML
                        ^^^^^^^^^^^^^^^^
                         Query Parameter
```

**Example:**
```
https://development.epmwarecloud.com/service/api/security/groups/users?groupName=Admin
```


## Next Steps

- [Authentication](authentication.md) - Set up API authentication
- [Response Formats](response-formats.md) - Understand API responses