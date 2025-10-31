# Run ERP Import

The Run ERP Import API allows you to execute a pre-configured ERP Import interface task on demand. This endpoint triggers the import process for data already loaded in the ERP Import interface tables.

## Overview

This API executes an ERP Import that has been configured with "One Time Schedule" type. The ERP Import Service must be running for this API to work successfully.

!!! warning "Prerequisites"
    - ERP Import must be configured with "One Time Schedule" type
    - ERP Import Service must be running
    - Data should already be loaded in the interface table (via upload or manual load)

## Endpoint Details

### URL Structure

```
POST /service/api/erp/run
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/erp/run
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | String | Yes | - | ERP Import configuration name |
| `timeout_min` | Integer | No | 60 | Minutes to wait for execution completion |

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

### Using Python

```python
import requests
import json

def run_erp_import(base_url, token, import_name, timeout_min=60):
    """
    Execute ERP Import
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        import_name: ERP Import configuration name
        timeout_min: Timeout in minutes
    
    Returns:
        dict: Response containing task ID
    """
    url = f"{base_url}/service/api/erp/run"
    
    headers = {
        'Authorization': f'Token {token}',
        'Content-Type': 'application/json'
    }
    
    data = {
        'name': import_name,
        'timeout_min': timeout_min
    }
    
    response = requests.post(url, headers=headers, json=data)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to run import: {response.status_code} - {response.text}")

# Usage
result = run_erp_import(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    import_name="ASOALL_Account",
    timeout_min=60
)

print(f"Task ID: {result['taskId']}")
print(f"Status: {result['status']}")
```

### Using PowerShell

```powershell
function Start-EPMwareERPImport {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$ImportName,
        
        [int]$TimeoutMinutes = 60
    )
    
    $url = "$BaseUrl/service/api/erp/run"
    
    $headers = @{
        "Authorization" = "Token $Token"
        "Content-Type" = "application/json"
    }
    
    $body = @{
        name = $ImportName
        timeout_min = $TimeoutMinutes
    } | ConvertTo-Json
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Post `
            -Headers $headers `
            -Body $body
        
        Write-Host "âœ… ERP Import started successfully!" -ForegroundColor Green
        Write-Host "Task ID: $($response.taskId)"
        Write-Host "Status: $($response.status)"
        
        return $response
    }
    catch {
        Write-Host "âŒ Failed to start import: $_" -ForegroundColor Red
        throw
    }
}

# Usage
$result = Start-EPMwareERPImport `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -ImportName "ASOALL_Account" `
    -TimeoutMinutes 60
```

## Response Format

### Successful Response

```json
{
  "status": "S",
  "taskId": "244659"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `taskId` | String | Task ID for monitoring import progress |
| `status` | String | Initial submission status |

## Complete Import Workflow

### Step-by-Step Process

```python
import requests
import time
import json

class ERPImportManager:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.token = token
        self.headers = {
            'Authorization': f'Token {token}',
            'Content-Type': 'application/json'
        }
    
    def run_import(self, import_name, timeout_min=60):
        """Start ERP Import execution"""
        url = f"{self.base_url}/service/api/erp/run"
        data = {
            'name': import_name,
            'timeout_min': timeout_min
        }
        
        response = requests.post(url, headers=self.headers, json=data)
        
        if response.status_code == 200:
            return response.json()
        else:
            raise Exception(f"Failed to start import: {response.text}")
    
    def monitor_task(self, task_id, max_wait_seconds=3600):
        """Monitor task until completion"""
        start_time = time.time()
        
        while time.time() - start_time < max_wait_seconds:
            # Check status
            status_url = f"{self.base_url}/service/api/task/get_status/{task_id}"
            response = requests.get(status_url, headers={'Authorization': f'Token {self.token}'})
            status_data = response.json()
            
            print(f"Status: {status_data['status']} - Progress: {status_data['percentCompleted']}%")
            
            if status_data['status'] == 'S':
                print("âœ… Import completed successfully!")
                return True
            elif status_data['status'] == 'E':
                print(f"âŒ Import failed: {status_data['message']}")
                return False
            
            time.sleep(10)  # Check every 10 seconds
        
        raise TimeoutError("Import did not complete within timeout period")
    
    def get_import_log(self, task_id):
        """Retrieve import execution log"""
        url = f"{self.base_url}/service/api/task/get_log_file/{task_id}"
        response = requests.get(url, headers={'Authorization': f'Token {self.token}'})
        return response.text
    
    def execute_and_monitor(self, import_name, timeout_min=60):
        """Execute import and monitor to completion"""
        print(f"ðŸš€ Starting ERP Import: {import_name}")
        
        # Start import
        result = self.run_import(import_name, timeout_min)
        task_id = result['taskId']
        print(f"ðŸ“‹ Task ID: {task_id}")
        
        # Monitor progress
        success = self.monitor_task(task_id, timeout_min * 60)
        
        # Get log
        log = self.get_import_log(task_id)
        
        if success:
            print("\nðŸ“„ Import Log (Last 20 lines):")
            log_lines = log.strip().split('\n')
            for line in log_lines[-20:]:
                print(line)
        else:
            print("\nâŒ Error Log:")
            print(log)
        
        return success, task_id

# Usage example
manager = ERPImportManager(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
)

success, task_id = manager.execute_and_monitor(
    import_name="ASOALL_Account",
    timeout_min=30
)

if success:
    print(f"\nâœ… Import completed successfully. Task ID: {task_id}")
else:
    print(f"\nâŒ Import failed. Check task {task_id} for details.")
```

## Configuration Requirements

### ERP Import Setup

Before using this API, ensure your ERP Import is configured correctly:

1. **Schedule Type**: Set to "One Time Schedule"
2. **Interface Table**: Properly configured with required columns
3. **Mappings**: Column mappings defined
4. **Validations**: Any required validations configured
5. **Service Status**: ERP Import Service is running

### Checking Service Status

```python
def check_erp_service_status(base_url, token):
    """Check if ERP Import Service is running"""
    # Note: This is a conceptual example - actual implementation
    # depends on your EPMware monitoring endpoints
    
    headers = {'Authorization': f'Token {token}'}
    
    # You might need to check service status through a different endpoint
    # or administrative interface
    
    print("âš ï¸ Ensure ERP Import Service is running before executing imports")
    return True
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Invalid token | Verify authentication token |
| `404 Not Found` | Import configuration not found | Check configuration name |
| `400 Bad Request` | Invalid parameters | Verify request format |
| `503 Service Unavailable` | ERP Service not running | Start ERP Import Service |
| `409 Conflict` | Import already running | Wait for current import to complete |

### Error Response Examples

```json
// Configuration not found
{
  "status": "E",
  "message": "ERP Import configuration 'INVALID_NAME' not found"
}

// Service not running
{
  "status": "E",
  "message": "ERP Import Service is not running"
}

// Import already in progress
{
  "status": "E",
  "message": "Import 'ASOALL_Account' is already in progress"
}
```

## Best Practices

### 1. Check Prerequisites

```python
def validate_before_import(import_name):
    """Validate prerequisites before running import"""
    checks = {
        "Configuration exists": check_configuration_exists(import_name),
        "Service running": check_service_status(),
        "No active imports": check_no_active_imports(import_name),
        "Data loaded": check_interface_table_has_data(import_name)
    }
    
    all_passed = all(checks.values())
    
    for check, passed in checks.items():
        status = "âœ…" if passed else "âŒ"
        print(f"{status} {check}")
    
    return all_passed
```

### 2. Implement Timeout Strategy

```python
def calculate_timeout(record_count):
    """Calculate appropriate timeout based on data volume"""
    # Base timeout of 10 minutes
    base_timeout = 10
    
    # Add 1 minute per 1000 records
    additional_time = (record_count // 1000)
    
    # Cap at 120 minutes
    timeout = min(base_timeout + additional_time, 120)
    
    return timeout
```

### 3. Handle Concurrent Imports

```python
import threading
import queue

def run_multiple_imports(imports_list, max_concurrent=3):
    """Run multiple imports with concurrency control"""
    
    task_queue = queue.Queue()
    results = {}
    
    def worker():
        while True:
            import_name = task_queue.get()
            if import_name is None:
                break
            
            try:
                result = run_erp_import(import_name)
                results[import_name] = result
                print(f"âœ… Started: {import_name} - Task: {result['taskId']}")
            except Exception as e:
                results[import_name] = {'status': 'E', 'error': str(e)}
                print(f"âŒ Failed: {import_name} - {e}")
            
            task_queue.task_done()
    
    # Start worker threads
    threads = []
    for _ in range(max_concurrent):
        t = threading.Thread(target=worker)
        t.start()
        threads.append(t)
    
    # Queue all imports
    for import_name in imports_list:
        task_queue.put(import_name)
    
    # Wait for completion
    task_queue.join()
    
    # Stop workers
    for _ in range(max_concurrent):
        task_queue.put(None)
    for t in threads:
        t.join()
    
    return results
```

## Monitoring and Logging

### Import Execution Log Example

```
25-NOV-2019 21:10:20 --> Task 244654 [ERP Import REST API] started
25-NOV-2019 21:10:20 --> Stage # 1 ASOALL_account - file id : 138
25-NOV-2019 21:10:20 --> Load File and Execute ERP Import for Interface : ASOALL_account
25-NOV-2019 21:10:20 --> Process file : ASOALL_Account.csv
25-NOV-2019 21:10:20 --> Column Separator : ,
25-NOV-2019 21:10:20 --> Column Enclosed By : "
25-NOV-2019 21:10:20 --> Status : S - Message : 
25-NOV-2019 21:10:20 --> Lines : 4
25-NOV-2019 21:10:20 --> Columns Count : 15
25-NOV-2019 21:10:21 --> Transfer Complete.
25-NOV-2019 21:10:21 --> Task status : Success
25-NOV-2019 21:10:21 --> Task 244654 Completed Successfully
```

## Related Operations

- [Upload File](upload-file.md) - Upload files for ERP Import
- [Get Task Status](../task/get-status.md) - Monitor import progress
- [Get Log File](../task/get-log-file.md) - Retrieve detailed logs

## Next Steps

After successfully running an import:
1. Monitor the task using the returned Task ID
2. Retrieve and review the execution log
3. Validate imported data in target systems
4. Proceed with dependent operations