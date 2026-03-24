# Get Log File

This API will return task details (Logfile) for given task id.

## Overview

The Get Log File endpoint allows you to download comprehensive execution logs for any task. These logs contain detailed information about task execution, including:

- Step-by-step execution details
- Error messages and stack traces
- Performance metrics
- Data validation results
- System messages

## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/task/get_log_file/{task_id}
```

### Method

`GET`


### Parameters

| Parameter  | Required | Description |
|-----------|----------|-------------|
| `task_id` | Yes | Unique task identifier |



## Request Example


```bash

curl GET 'http://demo.EPMWAREcloud.com/service/api/task/get_log_file/244591' \
      -H 'authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' > ew_task_244591_log.txt


```

## Response

### Success Response

The REST API will return log details for the given task ID as standard output on the console which can be redirected to be saved into a local file as shown in the example above.

```
================================================================================
Task ID: 244591
Task Type: ERP Import
Start Time: 2025-01-15 10:00:00
User: API_USER
================================================================================

[10:00:00] INFO: Task initialization started
[10:00:01] INFO: Connecting to database
[10:00:02] INFO: Connection established successfully
[10:00:03] INFO: Starting data validation
[10:00:05] INFO: Validation completed - 10,000 records validated
[10:00:06] INFO: Beginning import process
[10:00:15] INFO: Batch 1/10 completed - 1,000 records processed
[10:00:24] INFO: Batch 2/10 completed - 2,000 records processed
...
[10:05:30] INFO: Import completed successfully
[10:05:31] INFO: Total records processed: 10,000
[10:05:31] INFO: Success: 9,998 | Failed: 2
[10:05:32] INFO: Task completed

================================================================================
End Time: 2025-01-15 10:05:32
Duration: 00:05:32
Status: SUCCESS
================================================================================
```



## Related Documentation

- [Get Task Status](../get-status/) - Check task status before log retrieval
- [Task Module Overview](../) - Understanding task lifecycle
- [Error Handling](../../../getting-started/error-handling/) - Handle API errors
- [Examples](../../../examples/task-management/) - Complete implementation examples

---

[← Back to Task Module](../) | [Get Task Status](../get-status/)
