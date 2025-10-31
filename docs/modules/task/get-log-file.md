# Get Log File

Retrieve detailed log files for completed or failed tasks.

## Overview

The Get Log File endpoint allows you to download comprehensive execution logs for any task. These logs contain detailed information about task execution, including:

- Step-by-step execution details
- Error messages and stack traces
- Performance metrics
- Data validation results
- System messages

## Endpoint Details

### Request

```http
GET /service/api/task/get_log_file/{task_id}
```

### Parameters

| Parameter | Type | Location | Required | Description |
|-----------|------|----------|----------|-------------|
| `task_id` | String | Path | Yes | Unique task identifier |

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `Authorization` | Yes | Token authentication: `Token <your-token>` |

## Response Format

### Success Response

**Status Code:** `200 OK`

**Content-Type:** `text/plain` or `application/octet-stream`

**Response Body:** Log file content (text format)

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

### Error Responses

**Status Code:** `404 Not Found`

```json
{
  "status": "E",
  "message": "Task not found or log file not available",
  "errorCode": "TASK_404"
}
```

**Status Code:** `401 Unauthorized`

```json
{
  "status": "E",
  "message": "Authentication required",
  "errorCode": "AUTH_001"
}
```

## Log File Structure

### Standard Log Sections

1. **Header Section**
   - Task identification
   - Execution metadata
   - User information

2. **Execution Section**
   - Timestamped events
   - Progress updates
   - Data processing details

3. **Error Section** (if applicable)
   - Error messages
   - Stack traces
   - Failed record details

4. **Summary Section**
   - Execution statistics
   - Performance metrics
   - Final status

## Usage Examples

### Basic Log Retrieval

=== "cURL"

    ```bash
    curl -X GET 'https://demo.epmwarecloud.com/service/api/task/get_log_file/244591' \
      -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
      -o task_244591.log
    ```

=== "Python"

    ```python
    import requests
    
    def download_task_log(task_id, token, filename=None):
        """Download task log file"""
        url = f"https://demo.epmwarecloud.com/service/api/task/get_log_file/{task_id}"
        headers = {"Authorization": f"Token {token}"}
        
        response = requests.get(url, headers=headers)
        
        if response.status_code == 200:
            filename = filename or f"task_{task_id}.log"
            with open(filename, 'w') as f:
                f.write(response.text)
            print(f"Log saved to {filename}")
            return response.text
        else:
            print(f"Error: {response.status_code}")
            return None
    
    # Usage
    log_content = download_task_log("244591", "your-token-here")
    ```

=== "PowerShell"

    ```powershell
    $headers = @{
        "Authorization" = "Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
    }
    
    $taskId = "244591"
    $url = "https://demo.epmwarecloud.com/service/api/task/get_log_file/$taskId"
    
    # Download log to file
    Invoke-RestMethod -Uri $url -Headers $headers -OutFile "task_$taskId.log"
    
    # Or get content directly
    $logContent = Invoke-RestMethod -Uri $url -Headers $headers
    Write-Host $logContent
    ```

=== "JavaScript"

    ```javascript
    async function downloadTaskLog(taskId, token) {
        const url = `https://demo.epmwarecloud.com/service/api/task/get_log_file/${taskId}`;
        
        try {
            const response = await fetch(url, {
                headers: {
                    'Authorization': `Token ${token}`
                }
            });
            
            if (response.ok) {
                const logContent = await response.text();
                
                // Save to file (Node.js)
                const fs = require('fs');
                fs.writeFileSync(`task_${taskId}.log`, logContent);
                
                return logContent;
            } else {
                console.error(`Error: ${response.status}`);
                return null;
            }
        } catch (error) {
            console.error('Network error:', error);
            return null;
        }
    }
    ```

## Advanced Usage

### Parse Log for Errors

```python
import re
from datetime import datetime

def analyze_task_log(log_content):
    """Extract and analyze errors from task log"""
    
    errors = []
    warnings = []
    metrics = {}
    
    lines = log_content.split('\n')
    
    for line in lines:
        # Extract errors
        if 'ERROR' in line:
            match = re.match(r'\[(.*?)\] ERROR: (.*)', line)
            if match:
                errors.append({
                    'timestamp': match.group(1),
                    'message': match.group(2)
                })
        
        # Extract warnings
        if 'WARNING' in line:
            match = re.match(r'\[(.*?)\] WARNING: (.*)', line)
            if match:
                warnings.append({
                    'timestamp': match.group(1),
                    'message': match.group(2)
                })
        
        # Extract metrics
        if 'Total records processed:' in line:
            match = re.search(r'Total records processed: (\d+)', line)
            if match:
                metrics['total_records'] = int(match.group(1))
        
        if 'Duration:' in line:
            match = re.search(r'Duration: ([\d:]+)', line)
            if match:
                metrics['duration'] = match.group(1)
    
    return {
        'errors': errors,
        'warnings': warnings,
        'metrics': metrics,
        'has_errors': len(errors) > 0
    }

# Usage
log_content = download_task_log("244591", token)
analysis = analyze_task_log(log_content)

if analysis['has_errors']:
    print(f"Task had {len(analysis['errors'])} errors")
    for error in analysis['errors']:
        print(f"  - {error['timestamp']}: {error['message']}")
```

### Stream Large Logs

```python
import requests

def stream_task_log(task_id, token, chunk_size=1024):
    """Stream large log files in chunks"""
    url = f"https://demo.epmwarecloud.com/service/api/task/get_log_file/{task_id}"
    headers = {"Authorization": f"Token {token}"}
    
    with requests.get(url, headers=headers, stream=True) as response:
        response.raise_for_status()
        
        for chunk in response.iter_content(chunk_size=chunk_size):
            if chunk:
                process_log_chunk(chunk.decode('utf-8'))

def process_log_chunk(chunk):
    """Process each chunk of the log"""
    # Real-time log processing
    for line in chunk.split('\n'):
        if 'ERROR' in line:
            send_alert(line)
        elif 'WARNING' in line:
            log_warning(line)
```

### Log File Archival

```python
import os
import gzip
from datetime import datetime

def archive_task_log(task_id, token):
    """Download and compress task log for archival"""
    
    # Download log
    log_content = download_task_log(task_id, token)
    
    if log_content:
        # Create archive filename with timestamp
        timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
        archive_name = f"logs/task_{task_id}_{timestamp}.log.gz"
        
        # Ensure directory exists
        os.makedirs('logs', exist_ok=True)
        
        # Compress and save
        with gzip.open(archive_name, 'wt', encoding='utf-8') as f:
            f.write(log_content)
        
        print(f"Log archived to {archive_name}")
        return archive_name
    
    return None
```

## Log Analysis Patterns

### Error Pattern Detection

```python
ERROR_PATTERNS = {
    'connection': r'Connection (failed|refused|timeout)',
    'validation': r'Validation (error|failed)',
    'permission': r'(Permission denied|Access denied|Unauthorized)',
    'data': r'(Data integrity|Constraint violation|Duplicate)',
    'resource': r'(Out of memory|Disk full|Resource exhausted)'
}

def categorize_errors(log_content):
    """Categorize errors by type"""
    error_categories = {key: [] for key in ERROR_PATTERNS}
    
    for line in log_content.split('\n'):
        if 'ERROR' in line:
            for category, pattern in ERROR_PATTERNS.items():
                if re.search(pattern, line, re.IGNORECASE):
                    error_categories[category].append(line)
                    break
    
    return error_categories
```

### Performance Metrics Extraction

```python
def extract_performance_metrics(log_content):
    """Extract performance metrics from log"""
    metrics = {
        'start_time': None,
        'end_time': None,
        'duration': None,
        'records_per_second': 0,
        'batches_processed': 0,
        'average_batch_time': 0
    }
    
    # Extract timing information
    start_match = re.search(r'Start Time: (.*)', log_content)
    end_match = re.search(r'End Time: (.*)', log_content)
    
    if start_match and end_match:
        metrics['start_time'] = start_match.group(1)
        metrics['end_time'] = end_match.group(1)
    
    # Extract processing rates
    batch_times = re.findall(r'Batch \d+/\d+ completed in (\d+\.?\d*) seconds', log_content)
    if batch_times:
        metrics['batches_processed'] = len(batch_times)
        metrics['average_batch_time'] = sum(float(t) for t in batch_times) / len(batch_times)
    
    return metrics
```

## Best Practices

### 1. Check Task Status First

Always verify task completion before retrieving logs:

```python
def get_log_when_ready(task_id, token):
    """Get log only after task completes"""
    status = get_task_status(task_id, token)
    
    if status['status'] in ['S', 'E', 'W', 'C']:
        return download_task_log(task_id, token)
    else:
        print(f"Task still running: {status['percentCompleted']}% complete")
        return None
```

### 2. Handle Large Logs

Implement pagination or streaming for large log files:

```python
def get_log_tail(log_content, lines=100):
    """Get last N lines of log"""
    log_lines = log_content.split('\n')
    return '\n'.join(log_lines[-lines:])
```

### 3. Implement Log Retention

```python
import os
from datetime import datetime, timedelta

def cleanup_old_logs(log_dir='logs', days_to_keep=30):
    """Remove logs older than specified days"""
    cutoff_date = datetime.now() - timedelta(days=days_to_keep)
    
    for filename in os.listdir(log_dir):
        filepath = os.path.join(log_dir, filename)
        file_modified = datetime.fromtimestamp(os.path.getmtime(filepath))
        
        if file_modified < cutoff_date:
            os.remove(filepath)
            print(f"Removed old log: {filename}")
```

## Troubleshooting

### Common Issues

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| 404 Not Found | Task doesn't exist | Verify task ID |
| Empty log file | Task not started | Wait for task to begin |
| Truncated log | Log rotation occurred | Check archive location |
| Encoding errors | Non-UTF8 characters | Use binary mode for download |

### Debug Checklist

1. ✅ Verify task exists using get_status endpoint
2. ✅ Ensure task has completed or failed
3. ✅ Check authentication token validity
4. ✅ Verify network connectivity
5. ✅ Confirm sufficient permissions

## Related Documentation

- [Get Task Status](../get-status/) - Check task status before log retrieval
- [Task Module Overview](../) - Understanding task lifecycle
- [Error Handling](../../../getting-started/error-handling/) - Handle API errors
- [Examples](../../../examples/task-management/) - Complete implementation examples

---

[← Back to Task Module](../) | [Get Task Status](../get-status/)
