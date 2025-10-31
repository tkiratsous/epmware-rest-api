# Task Management Examples

Examples for monitoring and managing EPMware tasks.

## Task Monitor Class

```python
import requests
import time
from enum import Enum

class TaskStatus(Enum):
    PENDING = 'P'
    RUNNING = 'R'
    SUCCESS = 'S'
    ERROR = 'E'
    WARNING = 'W'
    CANCELLED = 'C'

class TaskMonitor:
    """Monitor EPMware task execution"""
    
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.headers = {'Authorization': f'Token {token}'}
    
    def get_status(self, task_id):
        """Get current task status"""
        url = f"{self.base_url}/service/api/task/get_status/{task_id}"
        response = requests.get(url, headers=self.headers)
        return response.json()
    
    def wait_for_completion(self, task_id, timeout=3600, interval=5):
        """Wait for task to complete"""
        start_time = time.time()
        
        while time.time() - start_time < timeout:
            status = self.get_status(task_id)
            
            if status['status'] in ['S', 'E', 'W', 'C']:
                return status
            
            print(f"Task {task_id}: {status['percentCompleted']}% complete")
            time.sleep(interval)
        
        raise TimeoutError(f"Task {task_id} exceeded timeout")
    
    def get_log(self, task_id):
        """Download task log"""
        url = f"{self.base_url}/service/api/task/get_log_file/{task_id}"
        response = requests.get(url, headers=self.headers)
        return response.text

# Usage
monitor = TaskMonitor('https://demo.epmwarecloud.com', 'your-token')
result = monitor.wait_for_completion('12345')

if result['status'] == 'E':
    log = monitor.get_log('12345')
    print(f"Task failed. Log:\n{log}")
```

---

[← Back to Examples](../) | [ERP Integration →](../erp-integration/)
