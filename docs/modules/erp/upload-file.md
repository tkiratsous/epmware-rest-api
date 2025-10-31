# Upload File for ERP Import

The Upload File API allows you to upload metadata files to EPMware for ERP Import processing. This endpoint handles file upload and initiates the data loading process into the ERP Import interface table.

## Overview

This API uploads a local metadata file for a specified ERP Import interface configuration. Upon successful upload, it returns a Task ID that can be used to monitor the import progress.

## Endpoint Details

### URL Structure

```
POST /service/api/erp/upload_file
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/erp/upload_file
```

### Method

`POST`

### Content Type

`multipart/form-data`

## Parameters

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `file` | File | Yes | The metadata file to upload | `ASOALL_Account.csv` |
| `name` | String | Yes | ERP Import configuration name | `ASOALL_Account` |
| `delimiter_char` | String | Yes | Column delimiter character | `,` (comma) |
| `enclosing_char` | String | No | Character enclosing field values | `"` (double quote) |

!!! info "File Requirements"
    - File must be in a supported format (CSV, TXT)
    - File size limitations may apply based on your environment
    - Ensure the file structure matches the configured ERP Import interface

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

### Using Python

```python
import requests

def upload_erp_file(base_url, token, file_path, config_name, delimiter=',', enclosing_char='"'):
    """
    Upload a file for ERP Import
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        file_path: Path to the file to upload
        config_name: ERP Import configuration name
        delimiter: Column delimiter character
        enclosing_char: Field enclosing character
    
    Returns:
        dict: Response containing task ID
    """
    url = f"{base_url}/service/api/erp/upload_file"
    
    headers = {
        'Authorization': f'Token {token}'
    }
    
    with open(file_path, 'rb') as file:
        files = {
            'file': file
        }
        data = {
            'name': config_name,
            'delimiter_char': delimiter,
            'enclosing_char': enclosing_char
        }
        
        response = requests.post(url, headers=headers, files=files, data=data)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Upload failed: {response.status_code} - {response.text}")

# Usage
result = upload_erp_file(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    file_path="c:/test/ASOALL_Account.csv",
    config_name="ASOALL_Account",
    delimiter=",",
    enclosing_char='"'
)

print(f"Task ID: {result['taskId']}")
print(f"Status: {result['status']}")
print(f"Message: {result['message']}")
```

### Using PowerShell

```powershell
function Upload-EPMwareERPFile {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$FilePath,
        
        [Parameter(Mandatory=$true)]
        [string]$ConfigName,
        
        [string]$Delimiter = ",",
        
        [string]$EnclosingChar = '"'
    )
    
    $url = "$BaseUrl/service/api/erp/upload_file"
    
    # Prepare the multipart form
    $boundary = [System.Guid]::NewGuid().ToString()
    $LF = "`r`n"
    
    # Read file content
    $fileBytes = [System.IO.File]::ReadAllBytes($FilePath)
    $fileName = [System.IO.Path]::GetFileName($FilePath)
    
    # Build the multipart body
    $bodyLines = @(
        "--$boundary",
        "Content-Disposition: form-data; name=`"file`"; filename=`"$fileName`"",
        "Content-Type: application/octet-stream",
        "",
        [System.Text.Encoding]::Default.GetString($fileBytes),
        "--$boundary",
        "Content-Disposition: form-data; name=`"name`"",
        "",
        $ConfigName,
        "--$boundary",
        "Content-Disposition: form-data; name=`"delimiter_char`"",
        "",
        $Delimiter,
        "--$boundary",
        "Content-Disposition: form-data; name=`"enclosing_char`"",
        "",
        $EnclosingChar,
        "--$boundary--"
    )
    
    $body = $bodyLines -join $LF
    
    $headers = @{
        "Authorization" = "Token $Token"
        "Content-Type" = "multipart/form-data; boundary=$boundary"
    }
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Post `
            -Headers $headers `
            -Body $body
        
        Write-Host "âœ… File uploaded successfully!" -ForegroundColor Green
        Write-Host "Task ID: $($response.taskId)"
        Write-Host "Status: $($response.status)"
        Write-Host "Message: $($response.message)"
        
        return $response
    }
    catch {
        Write-Host "âŒ Upload failed: $_" -ForegroundColor Red
        throw
    }
}

# Usage
$result = Upload-EPMwareERPFile `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -FilePath "C:\test\ASOALL_Account.csv" `
    -ConfigName "ASOALL_Account" `
    -Delimiter "," `
    -EnclosingChar '"'
```

## Response Format

### Successful Response

```json
{
  "message": "Task Submitted Successfully.",
  "status": "S",
  "taskId": "244659"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `taskId` | String | Task ID for monitoring the import progress |
| `status` | String | Initial status (`S` for submitted successfully) |
| `message` | String | Descriptive message about the submission |

## Complete Workflow Example

Here's a complete example that uploads a file and monitors the import progress:

```python
import requests
import time

class ERPImporter:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.token = token
        self.headers = {'Authorization': f'Token {token}'}
    
    def upload_file(self, file_path, config_name, delimiter=',', enclosing_char='"'):
        """Upload file for ERP Import"""
        url = f"{self.base_url}/service/api/erp/upload_file"
        
        with open(file_path, 'rb') as file:
            files = {'file': file}
            data = {
                'name': config_name,
                'delimiter_char': delimiter,
                'enclosing_char': enclosing_char
            }
            
            response = requests.post(url, headers=self.headers, 
                                    files=files, data=data)
            
            if response.status_code == 200:
                return response.json()
            else:
                raise Exception(f"Upload failed: {response.text}")
    
    def check_status(self, task_id):
        """Check task status"""
        url = f"{self.base_url}/service/api/task/get_status/{task_id}"
        response = requests.get(url, headers=self.headers)
        return response.json()
    
    def get_log(self, task_id):
        """Get task log"""
        url = f"{self.base_url}/service/api/task/get_log_file/{task_id}"
        response = requests.get(url, headers=self.headers)
        return response.text
    
    def import_and_monitor(self, file_path, config_name, 
                          delimiter=',', enclosing_char='"', 
                          timeout_seconds=600):
        """Upload file and monitor import completion"""
        
        print(f"ðŸ“¤ Uploading file: {file_path}")
        upload_result = self.upload_file(file_path, config_name, 
                                        delimiter, enclosing_char)
        task_id = upload_result['taskId']
        print(f"âœ… File uploaded. Task ID: {task_id}")
        
        # Monitor progress
        start_time = time.time()
        while time.time() - start_time < timeout_seconds:
            status = self.check_status(task_id)
            
            print(f"â³ Progress: {status['percentCompleted']}% - Status: {status['status']}")
            
            if status['status'] == 'S':
                print(f"âœ… Import completed successfully!")
                log = self.get_log(task_id)
                print("ðŸ“‹ Import Log:")
                print(log)
                return True
            elif status['status'] == 'E':
                print(f"âŒ Import failed: {status['message']}")
                log = self.get_log(task_id)
                print("ðŸ“‹ Error Log:")
                print(log)
                return False
            
            time.sleep(5)
        
        raise TimeoutError(f"Import did not complete within {timeout_seconds} seconds")

# Usage
importer = ERPImporter(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
)

success = importer.import_and_monitor(
    file_path="c:/test/ASOALL_Account.csv",
    config_name="ASOALL_Account",
    delimiter=",",
    enclosing_char='"'
)
```

## File Format Guidelines

### CSV Files

```csv
"Account","Description","Parent","AccountType","TimeBalance"
"1000","Cash","Assets","Asset","Balance"
"1100","Accounts Receivable","Assets","Asset","Balance"
"2000","Accounts Payable","Liabilities","Liability","Balance"
```

### Delimiter Options

| Delimiter | Character | Example |
|-----------|-----------|---------|
| Comma | `,` | `Account,Description,Parent` |
| Semicolon | `;` | `Account;Description;Parent` |
| Tab | `\t` | `Account	Description	Parent` |
| Pipe | `|` | `Account|Description|Parent` |

### Enclosing Character Options

| Character | Usage | Example |
|-----------|-------|---------|
| Double Quote | `"` | `"Account Name"` |
| Single Quote | `'` | `'Account Name'` |
| None | (empty) | `Account Name` |

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
  "message": "ERP Import configuration 'INVALID_CONFIG' not found"
}
```

## Best Practices

### 1. Validate File Before Upload

```python
import csv

def validate_csv_file(file_path, expected_columns):
    """Validate CSV file before upload"""
    try:
        with open(file_path, 'r') as file:
            reader = csv.DictReader(file)
            headers = reader.fieldnames
            
            # Check if all expected columns exist
            missing = set(expected_columns) - set(headers)
            if missing:
                raise ValueError(f"Missing columns: {missing}")
            
            # Validate at least one row exists
            first_row = next(reader, None)
            if not first_row:
                raise ValueError("File has no data rows")
            
            print("âœ… File validation passed")
            return True
            
    except Exception as e:
        print(f"âŒ File validation failed: {e}")
        return False

# Usage
expected_cols = ["Account", "Description", "Parent", "AccountType"]
if validate_csv_file("ASOALL_Account.csv", expected_cols):
    # Proceed with upload
    pass
```

### 2. Implement Retry Logic

```python
import time

def upload_with_retry(file_path, config_name, max_retries=3):
    """Upload with automatic retry on failure"""
    for attempt in range(max_retries):
        try:
            result = upload_erp_file(file_path, config_name)
            return result
        except Exception as e:
            if attempt < max_retries - 1:
                wait_time = 2 ** attempt  # Exponential backoff
                print(f"Retry {attempt + 1} after {wait_time} seconds...")
                time.sleep(wait_time)
            else:
                raise e
```

### 3. Log Upload Activities

```python
import logging
from datetime import datetime

logging.basicConfig(
    filename='erp_uploads.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

def log_upload(file_path, config_name, task_id, status):
    """Log upload activity for audit trail"""
    logging.info(f"File: {file_path}, Config: {config_name}, " 
                f"Task: {task_id}, Status: {status}")
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