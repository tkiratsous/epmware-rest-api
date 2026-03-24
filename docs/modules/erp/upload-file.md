# Upload File for ERP Import

This API will upload a local metadata file for a given ERP Import interface and return Task ID which can be monitored for its status.

## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/erp/upload_file
```

### Method

`POST`


### Content Type

`multipart/form-data`

## Parameters

| Parameter  | Required | Description | Example |
|----------- |----------|-------------|---------|
| `file`  | Yes | The metadata file to upload | "/C:/Users/Downloads/aso_account.csv" |
| `name`  | Yes | ERP Import configuration name | `ASOALL_Account` |
| `delimiter_char` | Yes | Column delimiter character | `,` (comma) |
| `enclosing_char` | No | Character enclosing field values | `"` (double quote) |


This API call will return TASK_ID in return. User will use this TASK_ID to probe its status.



!!! info "File Requirements"
    - File size limitations may apply based on your Epmware global setting 
       Setting name : Maximum file size in MB for ERP Import
    - Ensure the file structure matches the configured ERP Import mapping

### API Structure

```bash
curl POST 'https://<EPMWARE_URL>/service/api/erp/upload_file' \
       -H 'Authorization: Token <TOKEN>' \
       -H 'Content-Type: multipart/form-data' \
       -F 'file=@"<FILE_WITH_PATH>"' \
       -F 'name="<ERP_IMPORT_NAME>"' \
       -F 'delimiter_char="<COLUMN_DELIM_CHAR>"' \
       -F 'enclosing_char="<COLUMN_ENCLOSED_BY_CHAR>"'
```


## Request Examples

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/erp/upload_file' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: multipart/form-data' \
  -F 'file=@"c:\test\ASOALL_Account.csv"' \
  -F 'name="ASOALL_Account"' \
  -F 'delimiter_char=","' \
  -F 'enclosing_char=""'
```


### Response Examples

```json
{
    "status": "S",
    "taskId": "13784",
    "message": "Task Submitted Succesfully."
}

```

### Response Fields

| Field | Description |
|---------|-------------|
| `taskId`  | Task ID for monitoring the import progress |
| `status`  | Initial status (`S` for submitted successfully) |
| `message`  | Descriptive message about the submission |




## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Invalid token | Verify authentication token |
| `400 Bad Request` | Missing parameters | Check all required parameters are provided |
| `404 Not Found` | Invalid configuration name | Verify ERP Import configuration exists |
| `413 Payload Too Large` | File too large | Reduce file size or split into batches |
| `500 Internal Server Error` | Server issue | Check server logs or contact support |

### Error Response Example

```json
{
    "status": "E",
    "taskId": "0",
    "message": "Error: Invalid ERP Import Name : [AV_GENERIC_CVE_E4]. "
}
```

## Related Operations

- [Run ERP Import](run-import.md) - Execute scheduled ERP imports
- [Get Task Status](../task/get-status.md) - Monitor task progress
- [Get Log File](../task/get-log-file.md) - Retrieve detailed logs

## Next Steps

After successfully uploading a file:

  1. Monitor the task using the returned Task ID
  2. Retrieve logs if needed
  3. Proceed with dependent operations once import completes