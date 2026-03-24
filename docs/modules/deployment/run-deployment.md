# Run Deployment

This action allows user to Deployment task on demand. 

!!! warning "Prerequisites"
    - Deployment must be configured with "One Time Schedule" type
    - Deployment Service must be running


## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/deployment/run
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter  | Required | Default | Description |
|----------- |----------|---------|-------------|
| `name` |  Yes | - | Deployment configuration name |
| `timeout_min` | No | 60 | Minutes to wait for execution completion |

This call will return TASK_ID in return. User will use this TASK_ID to probe its status.

## Request Examples

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/deployment/run' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "ASOALL",
    "timeout_min": 60
  }'
```

## Response Format

### Successful Response

```json
{
  "status": "S",
  "taskId": "244596"
}
```

### Response Fields

| Field  | Description |
|------- |-------------|
| `taskId` | Task ID for monitoring deployment progress |
| `status` | Initial submission status (`S` for success) |


## Related Operations

- [Get Task Status](../task/get-status.md) - Monitor deployment progress
- [Get Log File](../task/get-log-file.md) - Retrieve detailed logs

## Next Steps

After successfully running a deployment:

  1. Monitor the task using the returned Task ID
  2. Retrieve and review the execution log
  3. Validate changes in target applications
