# Get Task Status

The Get Task Status API allows you to check the current status and progress of any task in EPMware. This is essential for monitoring long-running operations initiated through other API calls.

## Overview

When you submit operations through the REST API (such as ERP imports, deployments, or exports), EPMware returns a Task ID. Use this endpoint to poll the task status until completion.

## Endpoint Details

### URL Structure

```
GET /service/api/task/get_status/{TASK_ID}
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/task/get_status/244591
```

### Method

`GET`

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `TASK_ID` | Path | Yes | The task ID returned from a previous API call |

## Request Example

### Using curl

```bash
curl GET 'https://demo.epmwarecloud.com/service/api/task/get_status/244591' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

### Using Python

```python
import requests

task_id = "244591"
url = f"https://demo.epmwarecloud.com/service/api/task/get_status/{task_id}"
headers = {
    'Authorization': 'Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
}

response = requests.get(url, headers=headers)
task_status = response.json()

print(f"Status: {task_status['status']}")
print(f"Progress: {task_status['percentCompleted']}%")
print(f"Message: {task_status['message']}")
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

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `taskId` | String | The task ID being queried |
| `status` | String | Current status code (see Status Codes below) |
| `percentCompleted` | String | Percentage of task completion (0-100) |
| `message` | String | Status message or error description |

## Status Codes

The `status` field returns one of the following codes:

| Status | Meaning | Description | Next Action |
|--------|---------|-------------|-------------|
| `N` | New | Task is queued but not started | Continue polling |
| `S` | Success | Task completed successfully | Retrieve results |
| `E` | Error | Task failed with errors | Check message and logs |

!!! info "Polling Recommendation"
    For status code `N`, implement polling with a reasonable interval (e.g., 5-10 seconds) to avoid overwhelming the server.

## Implementation Examples

### Polling Until Completion

```python
import requests
import time

def wait_for_task_completion(task_id, base_url, token, timeout=600):
    """
    Poll task status until completion or timeout
    
    Args:
        task_id: The task ID to monitor
        base_url: EPMware base URL
        token: Authentication token
        timeout: Maximum wait time in seconds
    
    Returns:
        Final task status dict
    """
    url = f"{base_url}/service/api/task/get_status/{task_id}"
    headers = {'Authorization': f'Token {token}'}
    
    start_time = time.time()
    
    while time.time() - start_time < timeout:
        response = requests.get(url, headers=headers)
        
        if response.status_code != 200:
            raise Exception(f"API error: {response.status_code}")
        
        status = response.json()
        
        print(f"Task {task_id}: {status['percentCompleted']}% - {status['status']}")
        
        if status['status'] in ['S', 'E']:
            return status
        
        time.sleep(5)  # Wait 5 seconds before next poll
    
    raise TimeoutError(f"Task {task_id} did not complete within {timeout} seconds")

# Usage
try:
    result = wait_for_task_completion(
        task_id="244591",
        base_url="https://demo.epmwarecloud.com",
        token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
    )
    
    if result['status'] == 'S':
        print(f"âœ… Task completed: {result['message']}")
    else:
        print(f"âŒ Task failed: {result['message']}")
        
except TimeoutError as e:
    print(f"â±ï¸ {e}")
```

### PowerShell Implementation

```powershell
function Get-EPMwareTaskStatus {
    param(
        [Parameter(Mandatory=$true)]
        [string]$TaskId,
        
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [int]$MaxWaitMinutes = 10
    )
    
    $headers = @{
        "Authorization" = "Token $Token"
    }
    
    $url = "$BaseUrl/service/api/task/get_status/$TaskId"
    $endTime = (Get-Date).AddMinutes($MaxWaitMinutes)
    
    Write-Host "Monitoring task $TaskId..." -ForegroundColor Cyan
    
    while ((Get-Date) -lt $endTime) {
        try {
            $response = Invoke-RestMethod -Uri $url -Method Get -Headers $headers
            
            Write-Host "Status: $($response.status) | Progress: $($response.percentCompleted)%"
            
            if ($response.status -eq 'S') {
                Write-Host "âœ… Task completed successfully!" -ForegroundColor Green
                Write-Host "Message: $($response.message)"
                return $response
            }
            elseif ($response.status -eq 'E') {
                Write-Host "âŒ Task failed!" -ForegroundColor Red
                Write-Host "Error: $($response.message)"
                return $response
            }
            
            Start-Sleep -Seconds 5
        }
        catch {
            Write-Host "Error checking status: $_" -ForegroundColor Red
            throw
        }
    }
    
    Write-Warning "Task did not complete within $MaxWaitMinutes minutes"
}

# Usage
$result = Get-EPMwareTaskStatus `
    -TaskId "244591" `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -MaxWaitMinutes 10
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Invalid or missing token | Verify authentication token |
| `404 Not Found` | Invalid task ID | Check task ID from original request |
| `500 Internal Server Error` | Server issue | Contact support or retry |

### Error Response Example

```json
{
  "status": "E",
  "message": "Task ID not found",
  "taskId": "999999"
}
```

## Best Practices

### 1. Implement Exponential Backoff

Instead of fixed polling intervals, increase wait time between requests:

```python
wait_times = [5, 10, 15, 30, 60]  # seconds
for wait_time in wait_times:
    status = check_task_status(task_id)
    if status['status'] in ['S', 'E']:
        break
    time.sleep(wait_time)
```

### 2. Set Reasonable Timeouts

Don't poll indefinitely. Set maximum wait times based on expected task duration:

- ERP Imports: 5-30 minutes
- Deployments: 10-60 minutes
- Exports: 5-45 minutes

### 3. Handle Partial Completion

Check `percentCompleted` to provide progress feedback:

```python
if int(status['percentCompleted']) > 0:
    print(f"Processing: {status['percentCompleted']}% complete")
```

### 4. Log Status Transitions

Track status changes for debugging:

```python
previous_status = None
while True:
    current_status = check_task_status(task_id)
    if current_status['status'] != previous_status:
        log.info(f"Task {task_id} status changed: {previous_status} -> {current_status['status']}")
        previous_status = current_status['status']
```

## Related Operations

After checking task status:

- **If Status = 'S' (Success):**
  - [Get Log File](get-log-file.md) for detailed execution log
  - Proceed with dependent operations
  
- **If Status = 'E' (Error):**
  - [Get Log File](get-log-file.md) for error details
  - Implement error recovery logic

## Next Steps

- [Get Log File](get-log-file.md) - Retrieve detailed task logs
- [ERP Module](../erp/) - Submit ERP import tasks
- [Deployment Module](../deployment/) - Execute deployments
- [Export Module](../export/) - Run export operations