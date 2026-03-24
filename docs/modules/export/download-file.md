# Download Export File

This action allows user to download the Export Data File after the export execution has been successfully completed.


## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/export/download_file/<Task ID>
```

### Method

`GET`

### Parameters

| Parameter  | Required | Description |
|----------- |----------|-------------|
| `TASK_ID`  | Yes | Task ID from export execution |

## Request Examples

### Using curl

```bash

# Download to current directory

curl GET 'https://demo.epmwarecloud.com/service/api/export/download_file/244596' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -o export_244596.txt

# Download with original filename

curl -OJ 'https://demo.epmwarecloud.com/service/api/export/download_file/244596' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```


### Using PowerShell

```powershell
$headers = @{
    Authorization = "Token 26d55747-0f89-48dc-8283-db62908fc7d9"
}

Invoke-WebRequest `
    -Uri "https://development.epmwarecloud.com/service/api/export/download_file/13731" `
    -Headers $headers `
    -OutFile "downloaded_file"
```

## Response Format

It downloads a file in the directory where the API is being executed


## Related Operations

- [Run Export](run-export.md) - Start export execution
- [Get Export Details](get-execution-details.md) - Get export history
- [Get Task Status](../task/get-status.md) - Monitor export progress
