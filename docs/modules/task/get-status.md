# Get Task Status

This API will return % Complete, Status and message. 
You may need to call this API after other APIS which upon submission returns Task ID. API will return one of the following three values for the status code. If status code is N, then it means task is not complete yet and you may either need to wait or perform this check in a loop till status becomes either S or E.

## Overview

When you submit operations through the REST API (such as ERP imports, deployments, or exports), EPMware returns a Task ID. Use this endpoint to poll the task status until completion.

## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/task/get_status/{TASK_ID}
```

### Method

`GET`



### Parameters

| Parameter  | Required | Description |
|-----------|----------|-------------|
| `TASK_ID`  | Yes | The task ID returned from a previous API call |

### Status Codes

The `status` field returns one of the following codes:

| Status | Meaning | Description |
|--------|---------|-------------|
| `N` | New | This status code is the initial status applied when the task is submitted in the queue. |
| `S` | Success | Task is completed successfully. |
| `E` | Error | Task completed but with Error. Check the “Message” value for short Error Message. For complete task details, call “get_log_file” REST API as explained in the next section. |


## Request Example


```bash
curl GET 'https://demo.epmwarecloud.com/service/api/task/get_status/244591' \
      -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```


## Response Format

### Successful Response

```json
{
  "message": "Load completed successfully",
  "percentCompleted": "100",
  "status": "S",
  "taskId": "244591"
}
```


### Error Response Example

```json
{
  "status": "E",
  "message": "Task ID not found",
  "taskId": "999999"
}
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Invalid or missing token | Verify authentication token |
| `404 Not Found` | Invalid task ID | Check task ID from original request |
| `500 Internal Server Error` | Server issue | Contact support or retry |



## Next Steps

- [Get Log File](get-log-file.md) - Retrieve detailed task logs
- [ERP Module](../erp/) - Submit ERP import tasks
- [Deployment Module](../deployment/) - Execute deployments
- [Export Module](../export/) - Run export operations