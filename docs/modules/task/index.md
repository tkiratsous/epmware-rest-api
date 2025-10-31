# Task Module

Monitor and manage EPMware tasks through the REST API.

## Overview

The Task module provides endpoints for monitoring the status and progress of operations initiated through other API modules. When you trigger asynchronous operations like imports, exports, or deployments, they create tasks that can be tracked using this module.

## Key Features

- **Real-time Status Monitoring**: Track task progress and completion
- **Log File Access**: Retrieve detailed execution logs
- **Error Diagnostics**: Access error messages and stack traces
- **Performance Metrics**: Monitor execution times and resource usage

## Available Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| [`/service/api/task/get_status/{task_id}`](get-status/) | GET | Get current task status |
| [`/service/api/task/get_log_file/{task_id}`](get-log-file/) | GET | Download task log file |

## Task Lifecycle

```mermaid
stateDig TD
    A[Task Created] --> B{Status Check}
    B -->|Pending| C[PENDING]
    B -->|Running| D[RUNNING]
    B -->|Complete| E[SUCCESS]
    B -->|Failed| F[ERROR]
    
    C --> D
    D --> E
    D --> F
    
    E --> G[Retrieve Results]
    F --> H[Check Logs]
```

## Task Status Codes

| Status Code | Meaning | Description |
|-------------|---------|-------------|
| `P` | Pending | Task is queued for execution |
| `R` | Running | Task is currently executing |
| `S` | Success | Task completed successfully |
| `E` | Error | Task failed with errors |
| `W` | Warning | Task completed with warnings |
| `C` | Cancelled | Task was cancelled by user |

## Common Use Cases

### 1. Monitor Import Progress

```python
import requests
import time

def monitor_task(task_id, token):
    """Monitor task until completion"""
    url = f"https://demo.epmwarecloud.com/service/api/task/get_status/{task_id}"
    headers = {"Authorization": f"Token {token}"}
    
    while True:
        response = requests.get(url, headers=headers)
        data = response.json()
        
        print(f"Status: {data['status']} - {data['percentCompleted']}%")
        
        if data['status'] in ['S', 'E', 'C']:
            return data
            
        time.sleep(5)  # Poll every 5 seconds
```

### 2. Retrieve Error Details

```python
def get_task_log(task_id, token):
    """Download task log for error analysis"""
    url = f"https://demo.epmwarecloud.com/service/api/task/get_log_file/{task_id}"
    headers = {"Authorization": f"Token {token}"}
    
    response = requests.get(url, headers=headers)
    
    if response.status_code == 200:
        with open(f"task_{task_id}.log", "w") as f:
            f.write(response.text)
        return response.text
    else:
        return None
```

### 3. Batch Task Monitoring

```python
def monitor_multiple_tasks(task_ids, token):
    """Monitor multiple tasks simultaneously"""
    results = {}
    
    for task_id in task_ids:
        url = f"https://demo.epmwarecloud.com/service/api/task/get_status/{task_id}"
        headers = {"Authorization": f"Token {token}"}
        
        response = requests.get(url, headers=headers)
        results[task_id] = response.json()
    
    return results
```

## Response Examples

### Successful Task Status

```json
{
  "taskId": "244591",
  "status": "S",
  "percentCompleted": "100",
  "message": "Import completed successfully",
  "startTime": "2025-01-15T10:00:00Z",
  "endTime": "2025-01-15T10:05:32Z",
  "duration": "00:05:32"
}
```

### Running Task Status

```json
{
  "taskId": "244592",
  "status": "R",
  "percentCompleted": "45",
  "message": "Processing records...",
  "startTime": "2025-01-15T10:10:00Z",
  "recordsProcessed": 4500,
  "totalRecords": 10000
}
```

### Failed Task Status

```json
{
  "taskId": "244593",
  "status": "E",
  "percentCompleted": "0",
  "message": "Import failed: Invalid data format",
  "errorCode": "IMP_001",
  "startTime": "2025-01-15T10:15:00Z",
  "endTime": "2025-01-15T10:15:05Z"
}
```

## Best Practices

### 1. Polling Intervals

Choose appropriate polling intervals based on expected task duration:

| Task Type | Expected Duration | Recommended Interval |
|-----------|------------------|---------------------|
| Quick operations | < 1 minute | 2-3 seconds |
| Standard imports | 1-10 minutes | 5-10 seconds |
| Large deployments | > 10 minutes | 30-60 seconds |

### 2. Error Handling

Always check both status code and response content:

```python
def check_task_safely(task_id, token):
    try:
        response = requests.get(
            f"{base_url}/task/get_status/{task_id}",
            headers={"Authorization": f"Token {token}"}
        )
        
        if response.status_code == 200:
            data = response.json()
            if data['status'] == 'E':
                # Get detailed log for error analysis
                log = get_task_log(task_id, token)
                analyze_error(log)
            return data
        else:
            handle_http_error(response.status_code)
            
    except requests.exceptions.RequestException as e:
        handle_network_error(e)
```

### 3. Resource Management

Implement timeouts to prevent indefinite waiting:

```python
import time

def monitor_with_timeout(task_id, token, timeout=3600):
    """Monitor task with timeout (default 1 hour)"""
    start_time = time.time()
    
    while time.time() - start_time < timeout:
        status = get_task_status(task_id, token)
        
        if status['status'] in ['S', 'E', 'C']:
            return status
            
        time.sleep(10)
    
    raise TimeoutError(f"Task {task_id} exceeded timeout of {timeout} seconds")
```

## Integration Patterns

### Event-Driven Architecture

```python
class TaskEventHandler:
    def __init__(self):
        self.handlers = {
            'S': self.on_success,
            'E': self.on_error,
            'W': self.on_warning
        }
    
    def process_task(self, task_id):
        status = get_task_status(task_id)
        handler = self.handlers.get(status['status'])
        if handler:
            handler(task_id, status)
    
    def on_success(self, task_id, status):
        # Trigger downstream processes
        pass
    
    def on_error(self, task_id, status):
        # Send alerts and notifications
        pass
    
    def on_warning(self, task_id, status):
        # Log warnings for review
        pass
```

### Dashboard Integration

```javascript
// Real-time task monitoring dashboard
function updateTaskDashboard() {
    const taskIds = getActiveTaskIds();
    
    taskIds.forEach(taskId => {
        fetch(`${apiUrl}/task/get_status/${taskId}`, {
            headers: { 'Authorization': `Token ${token}` }
        })
        .then(response => response.json())
        .then(data => {
            updateTaskWidget(taskId, data);
            updateProgressBar(taskId, data.percentCompleted);
            
            if (data.status === 'E') {
                showErrorAlert(taskId, data.message);
            }
        });
    });
}

// Update every 5 seconds
setInterval(updateTaskDashboard, 5000);
```

## Troubleshooting

### Common Issues

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Task not found | Invalid task ID | Verify task ID from creation response |
| Status stuck at 'P' | Queue congestion | Check system resources |
| No log file available | Task still running | Wait for task completion |
| Incomplete log data | Log rotation | Check archive logs |

### Debug Checklist

1. ✅ Verify task ID is correct
2. ✅ Check authentication token is valid
3. ✅ Ensure proper network connectivity
4. ✅ Confirm task module permissions
5. ✅ Review system resource availability

## Performance Considerations

- **Caching**: Cache completed task statuses to reduce API calls
- **Batching**: Check multiple task statuses in single requests where possible
- **Compression**: Enable response compression for log file downloads
- **Connection Pooling**: Reuse HTTP connections for multiple requests

## Related Documentation

- [Get Task Status](get-status/) - Detailed endpoint documentation
- [Get Log File](get-log-file/) - Log retrieval guide
- [Error Handling](../../getting-started/error-handling/) - Error handling best practices
- [Examples](../../examples/task-management/) - Complete code examples

---

[← Back to Modules](../) | [Get Task Status →](get-status/)
