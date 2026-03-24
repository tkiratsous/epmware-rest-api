# Run ERP Import



This action allows the user to run ERP Import interface task on demand. 

!!! warning "Prerequisites"
    - ERP Import must be configured with "One Time Schedule" type
    - ERP Import Service must be running in EPMware (Adminstrator -> Services)
    - Data should already be loaded in the interface table (via upload API)

## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/erp/run
```

### URL Example

```
https://demo.epmwarecloud.com/service/api/erp/run
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter  | Required | Default | Description |
|----------- |----------|---------|-------------|
| `name`  | Yes | - | ERP Import configuration name |
| `timeout_min`  | No | 60 | Minutes to wait for execution completion |

This call will return TASK_ID in return. User will use this TASK_ID to probe its status.


### API Structure

```bash
curl POST 'http://<EPMWARE_URL>/service/api/erp/run' \ 
     -H 'authorization: Token <TOKEN>' \
     -H 'content-type: application/json' \  
     -d '{ "name" : "<ERP_IMPORT_NAME>",
           "timeout_min" : <WAIT_MINUTES>
         }'
```


## Request Examples

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/erp/run' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "ASOALL_Account",
    "timeout_min": 60
  }'
```


## Response Format

### Successful Response

```json
{
  "status": "S",
  "taskId": "13784"
}
```


## Monitoring and Logging

```bash

curl GET 'http://demo.EPMWAREcloud.com/service/api/task/get_log_file/13784' \
      -H 'authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' > ew_task_13784_log.txt


```

### Import Execution Log Example

output of ew_task_13784_log.txt will be as below : 

```
23-MAR-2026 13: 09: 52 --> Task 13784 [ERP Import REST API
] started
23-MAR-2026 13: 09: 53 --> Stage # 1 AV_GENERIC_CVE_E - file id : 15002
23-MAR-2026 13: 09: 53 -->  Load File and Execute ERP Import for Interface : AV_GENERIC_CVE_E
23-MAR-2026 13: 09: 53 -->  Process file : bu_ISM_Sept_av_generic.csv
23-MAR-2026 13: 09: 53 -->  Load File and Execute ERP Import for Interface : AV_GENERIC_CVE_E
23-MAR-2026 13: 09: 53 -->  Column Separator   : ,
23-MAR-2026 13: 09: 53 -->  Column Enclosed By : "23-MAR-2026 13: 09: 53 -->  File Parsing Status : S -  Message : 23-MAR-2026 13: 09: 53 -->  # of Lines : 3
23-MAR-2026 13: 09: 53 -->  Columns Count : 6
23-MAR-2026 13: 09: 53 -->  Action mapped to ACTION_CODE
23-MAR-2026 13: 09: 53 -->  Name mapped to MEMBER_NAME
23-MAR-2026 13: 09: 53 -->  Parent mapped to PARENT_MEMBER_NAME
23-MAR-2026 13: 09: 53 -->  Task status : Success
23-MAR-2026 13: 09: 53 --> Task ID : 13784 [ERP Import REST API
] Completed Successfully

```
## Related Operations

- [Upload File](upload-file.md) - Upload files for ERP Import
- [Get Task Status](../task/get-status.md) - Monitor import progress
- [Get Log File](../task/get-log-file.md) - Retrieve detailed logs

## Next Steps

After successfully running an import:

  1. Monitor the task using the returned Task ID
  2. Retrieve and review the execution log
  3. Validate imported data in target systems (Epmware) 
  4. Proceed with dependent operations