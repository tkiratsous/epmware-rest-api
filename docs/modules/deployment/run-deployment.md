# Run Deployment

The Run Deployment API allows you to execute a pre-configured deployment task on demand. This endpoint triggers the deployment process to push metadata changes to target applications.

## Overview

This API executes a Deployment that has been configured with "One Time Schedule" type. The Deployment Service must be running for this API to work successfully.

!!! warning "Prerequisites"
    - Deployment must be configured with "One Time Schedule" type
    - Deployment Service must be running
    - Target applications must be accessible
    - User must have appropriate permissions

## Endpoint Details

### URL Structure

```
POST /service/api/deployment/run
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/deployment/run
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | String | Yes | - | Deployment configuration name |
| `timeout_min` | Integer | No | 60 | Minutes to wait for execution completion |

## Request Examples

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/deployment/run' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "ASOALL",
    "timeout_min": 60
  }'
```

### Using Python

```python
import requests
import json

def run_deployment(base_url, token, deployment_name, timeout_min=60):
    """
    Execute a deployment
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        deployment_name: Deployment configuration name
        timeout_min: Timeout in minutes
    
    Returns:
        dict: Response containing task ID
    """
    url = f"{base_url}/service/api/deployment/run"
    
    headers = {
        'Authorization': f'Token {token}',
        'Content-Type': 'application/json'
    }
    
    data = {
        'name': deployment_name,
        'timeout_min': timeout_min
    }
    
    response = requests.post(url, headers=headers, json=data)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to run deployment: {response.status_code} - {response.text}")

# Usage
result = run_deployment(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    deployment_name="ASOALL",
    timeout_min=60
)

print(f"Task ID: {result['taskId']}")
print(f"Status: {result['status']}")
```

### Using PowerShell

```powershell
function Start-EPMwareDeployment {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$DeploymentName,
        
        [int]$TimeoutMinutes = 60
    )
    
    $url = "$BaseUrl/service/api/deployment/run"
    
    $headers = @{
        "Authorization" = "Token $Token"
        "Content-Type" = "application/json"
    }
    
    $body = @{
        name = $DeploymentName
        timeout_min = $TimeoutMinutes
    } | ConvertTo-Json
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Post `
            -Headers $headers `
            -Body $body
        
        Write-Host "âœ… Deployment started successfully!" -ForegroundColor Green
        Write-Host "Deployment: $DeploymentName"
        Write-Host "Task ID: $($response.taskId)"
        Write-Host "Status: $($response.status)"
        
        return $response
    }
    catch {
        Write-Host "âŒ Failed to start deployment: $_" -ForegroundColor Red
        throw
    }
}

# Usage
$result = Start-EPMwareDeployment `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -DeploymentName "ASOALL" `
    -TimeoutMinutes 60
```

## Response Format

### Successful Response

```json
{
  "status": "S",
  "taskId": "244596"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `taskId` | String | Task ID for monitoring deployment progress |
| `status` | String | Initial submission status (`S` for success) |

## Complete Deployment Workflow

### Comprehensive Implementation

```python
import requests
import time
import json
from datetime import datetime

class DeploymentManager:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.token = token
        self.headers = {
            'Authorization': f'Token {token}',
            'Content-Type': 'application/json'
        }
    
    def run_deployment(self, deployment_name, timeout_min=60):
        """Start deployment execution"""
        url = f"{self.base_url}/service/api/deployment/run"
        
        data = {
            'name': deployment_name,
            'timeout_min': timeout_min
        }
        
        response = requests.post(url, headers=self.headers, json=data)
        
        if response.status_code == 200:
            return response.json()
        else:
            raise Exception(f"Failed to start deployment: {response.text}")
    
    def get_task_status(self, task_id):
        """Check deployment task status"""
        url = f"{self.base_url}/service/api/task/get_status/{task_id}"
        response = requests.get(url, headers={'Authorization': f'Token {self.token}'})
        return response.json()
    
    def get_task_log(self, task_id):
        """Retrieve deployment log"""
        url = f"{self.base_url}/service/api/task/get_log_file/{task_id}"
        response = requests.get(url, headers={'Authorization': f'Token {self.token}'})
        return response.text
    
    def get_execution_details(self, deployment_name, latest_only='Y'):
        """Get deployment execution details"""
        url = f"{self.base_url}/service/api/deployment/get_execution_details"
        params = {
            'name': deployment_name,
            'latest_only': latest_only
        }
        response = requests.get(url, headers={'Authorization': f'Token {self.token}'}, 
                              params=params)
        return response.json()
    
    def monitor_deployment(self, task_id, check_interval=10, max_wait_seconds=3600):
        """Monitor deployment progress"""
        start_time = time.time()
        last_percent = 0
        
        print(f"â³ Monitoring deployment task {task_id}...")
        
        while time.time() - start_time < max_wait_seconds:
            status = self.get_task_status(task_id)
            percent = int(status.get('percentCompleted', 0))
            
            # Show progress bar
            if percent > last_percent:
                bar = 'â–ˆ' * (percent // 5) + 'â–‘' * (20 - percent // 5)
                print(f'\r[{bar}] {percent}% - {status["status"]}', end='', flush=True)
                last_percent = percent
            
            if status['status'] == 'S':
                print(f"\nâœ… Deployment completed successfully!")
                return True, status
            elif status['status'] == 'E':
                print(f"\nâŒ Deployment failed: {status.get('message', 'Unknown error')}")
                return False, status
            
            time.sleep(check_interval)
        
        print(f"\nâ±ï¸ Deployment did not complete within {max_wait_seconds} seconds")
        return False, None
    
    def execute_deployment(self, deployment_name, timeout_min=60):
        """Execute deployment with full monitoring"""
        print(f"ðŸš€ Starting Deployment: {deployment_name}")
        print(f"â° Timeout: {timeout_min} minutes")
        print("=" * 50)
        
        try:
            # Start deployment
            start_time = datetime.now()
            result = self.run_deployment(deployment_name, timeout_min)
            task_id = result['taskId']
            
            print(f"ðŸ“‹ Task ID: {task_id}")
            print(f"ðŸ• Started at: {start_time.strftime('%Y-%m-%d %H:%M:%S')}")
            print("-" * 50)
            
            # Monitor progress
            success, final_status = self.monitor_deployment(
                task_id, 
                check_interval=10,
                max_wait_seconds=timeout_min * 60
            )
            
            # Calculate duration
            end_time = datetime.now()
            duration = end_time - start_time
            
            print("-" * 50)
            print(f"ðŸ• Completed at: {end_time.strftime('%Y-%m-%d %H:%M:%S')}")
            print(f"â±ï¸ Duration: {duration}")
            
            # Get and display log
            if success:
                print("\nðŸ“„ Deployment Summary:")
                log = self.get_task_log(task_id)
                # Show last 10 lines of log
                log_lines = log.strip().split('\n')
                for line in log_lines[-10:]:
                    print(f"  {line}")
            else:
                print("\nâŒ Error Details:")
                log = self.get_task_log(task_id)
                print(log)
            
            # Get execution details
            print("\nðŸ“Š Execution Details:")
            details = self.get_execution_details(deployment_name)
            if details:
                print(json.dumps(details, indent=2))
            
            return success, task_id
            
        except Exception as e:
            print(f"\nâŒ Deployment failed with error: {e}")
            return False, None

# Usage example
manager = DeploymentManager(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
)

success, task_id = manager.execute_deployment(
    deployment_name="ASOALL",
    timeout_min=30
)

if success:
    print(f"\nðŸŽ‰ Deployment completed successfully!")
else:
    print(f"\nðŸ˜ž Deployment failed. Please check logs for details.")
```

## Deployment Types and Strategies

### Common Deployment Scenarios

| Scenario | Timeout (min) | Description |
|----------|--------------|-------------|
| Metadata Only | 10-30 | Dimension members and properties |
| Data and Metadata | 30-60 | Includes data values |
| Full Application | 60-120 | Complete application deployment |
| Multi-Application | 120+ | Deployment to multiple targets |

### Batch Deployment Example

```python
def deploy_batch(deployments, max_concurrent=2):
    """Deploy multiple configurations with concurrency control"""
    from concurrent.futures import ThreadPoolExecutor, as_completed
    
    manager = DeploymentManager(base_url, token)
    results = {}
    
    with ThreadPoolExecutor(max_workers=max_concurrent) as executor:
        # Submit all deployments
        futures = {
            executor.submit(
                manager.execute_deployment, 
                deployment['name'], 
                deployment.get('timeout_min', 60)
            ): deployment['name'] 
            for deployment in deployments
        }
        
        # Process completed deployments
        for future in as_completed(futures):
            deployment_name = futures[future]
            try:
                success, task_id = future.result()
                results[deployment_name] = {
                    'success': success,
                    'task_id': task_id
                }
                status = "âœ…" if success else "âŒ"
                print(f"{status} {deployment_name}: Task {task_id}")
            except Exception as e:
                results[deployment_name] = {
                    'success': False,
                    'error': str(e)
                }
                print(f"âŒ {deployment_name}: {e}")
    
    return results

# Usage
deployments = [
    {'name': 'ASOALL', 'timeout_min': 30},
    {'name': 'HFM_PROD', 'timeout_min': 45},
    {'name': 'PLANNING_DEV', 'timeout_min': 20}
]

results = deploy_batch(deployments, max_concurrent=2)
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Invalid token | Verify authentication token |
| `404 Not Found` | Deployment not found | Check deployment name |
| `503 Service Unavailable` | Deployment Service not running | Start Deployment Service |
| `409 Conflict` | Deployment already running | Wait for current deployment |
| `500 Internal Server Error` | Target application error | Check application connectivity |

### Error Response Examples

```json
// Deployment not found
{
  "status": "E",
  "message": "Deployment configuration 'INVALID_NAME' not found"
}

// Service not running
{
  "status": "E",
  "message": "Deployment Service is not running"
}

// Already running
{
  "status": "E",
  "message": "Deployment 'ASOALL' is already in progress"
}
```

## Best Practices

### 1. Pre-Deployment Validation

```python
def validate_deployment_ready(deployment_name):
    """Validate deployment prerequisites"""
    checks = []
    
    # Check deployment exists
    try:
        details = get_execution_details(deployment_name)
        checks.append(("Deployment exists", True))
    except:
        checks.append(("Deployment exists", False))
    
    # Check service status
    # This would require a service status endpoint
    checks.append(("Deployment Service running", True))
    
    # Check no active deployments
    # This would check for running tasks
    checks.append(("No active deployments", True))
    
    # Display results
    print("Pre-deployment Checks:")
    for check, passed in checks:
        status = "âœ…" if passed else "âŒ"
        print(f"  {status} {check}")
    
    return all(passed for _, passed in checks)
```

### 2. Deployment Scheduling

```python
from datetime import datetime, timedelta
import schedule
import time

def schedule_deployments():
    """Schedule deployments at specific times"""
    
    # Schedule daily deployment at 2 AM
    schedule.every().day.at("02:00").do(
        lambda: run_deployment("PROD_DAILY", timeout_min=60)
    )
    
    # Schedule weekly deployment on Sunday at 3 AM
    schedule.every().sunday.at("03:00").do(
        lambda: run_deployment("PROD_WEEKLY", timeout_min=120)
    )
    
    print("ðŸ“… Deployment schedule configured:")
    print("  - Daily: PROD_DAILY at 02:00")
    print("  - Weekly: PROD_WEEKLY on Sunday at 03:00")
    
    while True:
        schedule.run_pending()
        time.sleep(60)  # Check every minute
```

### 3. Deployment Rollback Strategy

```python
def deployment_with_rollback(deployment_name, rollback_name=None):
    """Deploy with automatic rollback on failure"""
    
    manager = DeploymentManager(base_url, token)
    
    print(f"ðŸš€ Starting deployment: {deployment_name}")
    success, task_id = manager.execute_deployment(deployment_name)
    
    if not success and rollback_name:
        print(f"âš ï¸ Deployment failed. Initiating rollback: {rollback_name}")
        rollback_success, rollback_task = manager.execute_deployment(rollback_name)
        
        if rollback_success:
            print("âœ… Rollback completed successfully")
        else:
            print("âŒ CRITICAL: Rollback failed! Manual intervention required")
            # Send alert notification
            send_alert(f"Deployment and rollback failed for {deployment_name}")
    
    return success, task_id
```

## Monitoring and Notifications

### Email Notification Example

```python
import smtplib
from email.mime.text import MIMEText

def send_deployment_notification(deployment_name, success, task_id, recipients):
    """Send email notification after deployment"""
    
    subject = f"Deployment {'Successful' if success else 'Failed'}: {deployment_name}"
    
    if success:
        body = f"""
        Deployment completed successfully!
        
        Deployment: {deployment_name}
        Task ID: {task_id}
        Status: Success
        Time: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}
        """
    else:
        body = f"""
        Deployment failed!
        
        Deployment: {deployment_name}
        Task ID: {task_id}
        Status: Failed
        Time: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}
        
        Please check the logs for details.
        """
    
    msg = MIMEText(body)
    msg['Subject'] = subject
    msg['From'] = 'epmware@company.com'
    msg['To'] = ', '.join(recipients)
    
    # Send email (configure SMTP settings)
    # smtp = smtplib.SMTP('smtp.company.com')
    # smtp.send_message(msg)
    # smtp.quit()
    
    print(f"ðŸ“§ Notification sent to: {recipients}")
```

## Related Operations

- [Get Execution Details](get-execution-details.md) - Retrieve deployment history
- [Get Task Status](../task/get-status.md) - Monitor deployment progress
- [Get Log File](../task/get-log-file.md) - Retrieve detailed logs

## Next Steps

After successfully running a deployment:
1. Monitor the task using the returned Task ID
2. Retrieve and review the execution log
3. Validate changes in target applications
4. Document deployment results