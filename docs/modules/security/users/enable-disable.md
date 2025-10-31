# Enable/Disable SSO User

The Enable and Disable APIs allow you to manage SSO user account status in EPMware, controlling user access without deleting accounts.

## Overview

These endpoints provide a way to temporarily or permanently control user access to EPMware. Disabled users cannot log in but their data and configurations are preserved.

!!! info "Account Status"
    - **Enabled (Y)**: User can log in and access EPMware
    - **Disabled (N)**: User cannot log in, but account data is preserved

## Enable User

### Endpoint Details

#### URL Structure

```
PATCH /service/api/security/users/sso/enable/{USERNAME}
```

#### Full URL Example

```
https://demo.epmwarecloud.com/service/api/security/users/sso/enable/SSO_USER1
```

#### Method

`PATCH`

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `USERNAME` | Path | Yes | Username to enable |

### Request Examples

#### Using curl

```bash
curl --request PATCH 'https://demo.epmwarecloud.com/service/api/security/users/sso/enable/SSO_USER1' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

#### Using Python

```python
import requests

def enable_user(base_url, token, username):
    """
    Enable an SSO user account
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        username: Username to enable
    
    Returns:
        dict: Response message
    """
    url = f"{base_url}/service/api/security/users/sso/enable/{username}"
    
    headers = {
        'Authorization': f'Token {token}'
    }
    
    response = requests.patch(url, headers=headers)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to enable user: {response.status_code} - {response.text}")

# Usage
result = enable_user(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    username="SSO_USER1"
)

print(result['value'])  # "User status enabled successfully"
```

#### Using PowerShell

```powershell
function Enable-EPMwareUser {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$Username
    )
    
    $url = "$BaseUrl/service/api/security/users/sso/enable/$Username"
    
    $headers = @{
        "Authorization" = "Token $Token"
    }
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Patch `
            -Headers $headers
        
        Write-Host "âœ… User enabled successfully: $Username" -ForegroundColor Green
        Write-Host $response.value
        
        return $response
    }
    catch {
        Write-Host "âŒ Failed to enable user: $_" -ForegroundColor Red
        throw
    }
}

# Usage
Enable-EPMwareUser `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -Username "SSO_USER1"
```

### Response Format

#### Successful Response

```json
{
  "value": "User status enabled successfully"
}
```

## Disable User

### Endpoint Details

#### URL Structure

```
PATCH /service/api/security/users/sso/disable/{USERNAME}
```

#### Full URL Example

```
https://demo.epmwarecloud.com/service/api/security/users/sso/disable/SSO_USER1
```

#### Method

`PATCH`

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `USERNAME` | Path | Yes | Username to disable |

### Request Examples

#### Using curl

```bash
curl --request PATCH 'https://demo.epmwarecloud.com/service/api/security/users/sso/disable/SSO_USER1' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

#### Using Python

```python
import requests

def disable_user(base_url, token, username):
    """
    Disable an SSO user account
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        username: Username to disable
    
    Returns:
        dict: Response message
    """
    url = f"{base_url}/service/api/security/users/sso/disable/{username}"
    
    headers = {
        'Authorization': f'Token {token}'
    }
    
    response = requests.patch(url, headers=headers)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to disable user: {response.status_code} - {response.text}")

# Usage
result = disable_user(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    username="SSO_USER1"
)

print(result['value'])  # "User status disabled successfully"
```

#### Using PowerShell

```powershell
function Disable-EPMwareUser {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$Username
    )
    
    $url = "$BaseUrl/service/api/security/users/sso/disable/$Username"
    
    $headers = @{
        "Authorization" = "Token $Token"
    }
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Patch `
            -Headers $headers
        
        Write-Host "âœ… User disabled successfully: $Username" -ForegroundColor Green
        Write-Host $response.value
        
        return $response
    }
    catch {
        Write-Host "âŒ Failed to disable user: $_" -ForegroundColor Red
        throw
    }
}

# Usage
Disable-EPMwareUser `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -Username "SSO_USER1"
```

### Response Format

#### Successful Response

```json
{
  "value": "User status disabled successfully"
}
```

## Response Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `200` | Status changed successfully | Operation complete |
| `401` | Unauthorized - Invalid token | Verify authentication |
| `404` | User not found | Check username exists |
| `409` | Status already set | No action needed |

## Bulk Operations

### Enable/Disable Multiple Users

```python
from typing import List, Dict
import time
import concurrent.futures

class UserStatusManager:
    """Manage user account status in bulk"""
    
    def __init__(self, base_url: str, token: str):
        self.base_url = base_url
        self.token = token
        self.headers = {'Authorization': f'Token {token}'}
    
    def enable_user(self, username: str) -> Dict:
        """Enable single user"""
        url = f"{self.base_url}/service/api/security/users/sso/enable/{username}"
        response = requests.patch(url, headers=self.headers)
        
        return {
            'username': username,
            'action': 'enable',
            'success': response.status_code == 200,
            'message': response.json().get('value') if response.status_code == 200 else response.text
        }
    
    def disable_user(self, username: str) -> Dict:
        """Disable single user"""
        url = f"{self.base_url}/service/api/security/users/sso/disable/{username}"
        response = requests.patch(url, headers=self.headers)
        
        return {
            'username': username,
            'action': 'disable',
            'success': response.status_code == 200,
            'message': response.json().get('value') if response.status_code == 200 else response.text
        }
    
    def bulk_enable(self, usernames: List[str], concurrent: bool = False) -> List[Dict]:
        """Enable multiple users"""
        
        if concurrent:
            return self._concurrent_operation(usernames, self.enable_user)
        else:
            return self._sequential_operation(usernames, self.enable_user)
    
    def bulk_disable(self, usernames: List[str], concurrent: bool = False) -> List[Dict]:
        """Disable multiple users"""
        
        if concurrent:
            return self._concurrent_operation(usernames, self.disable_user)
        else:
            return self._sequential_operation(usernames, self.disable_user)
    
    def _sequential_operation(self, usernames: List[str], operation) -> List[Dict]:
        """Process users sequentially"""
        results = []
        
        for username in usernames:
            print(f"Processing: {username}")
            try:
                result = operation(username)
                results.append(result)
                
                if result['success']:
                    print(f"  âœ… {result['action'].capitalize()}d: {username}")
                else:
                    print(f"  âŒ Failed: {username}")
                
                # Rate limiting
                time.sleep(0.5)
                
            except Exception as e:
                results.append({
                    'username': username,
                    'action': operation.__name__,
                    'success': False,
                    'message': str(e)
                })
        
        return results
    
    def _concurrent_operation(self, usernames: List[str], operation, max_workers: int = 5) -> List[Dict]:
        """Process users concurrently"""
        results = []
        
        with concurrent.futures.ThreadPoolExecutor(max_workers=max_workers) as executor:
            future_to_username = {
                executor.submit(operation, username): username 
                for username in usernames
            }
            
            for future in concurrent.futures.as_completed(future_to_username):
                username = future_to_username[future]
                try:
                    result = future.result()
                    results.append(result)
                except Exception as e:
                    results.append({
                        'username': username,
                        'action': operation.__name__,
                        'success': False,
                        'message': str(e)
                    })
        
        return results
    
    def toggle_user_status(self, username: str) -> Dict:
        """Toggle user status (enable if disabled, disable if enabled)"""
        
        # First, get current status
        user_info = self.get_user_info(username)
        
        if user_info.get('enable') == 'Y':
            return self.disable_user(username)
        else:
            return self.enable_user(username)
    
    def print_summary(self, results: List[Dict]):
        """Print operation summary"""
        
        total = len(results)
        success_count = sum(1 for r in results if r['success'])
        failed_count = total - success_count
        
        print("\n" + "="*50)
        print("ðŸ“Š Operation Summary:")
        print(f"  Total: {total}")
        print(f"  âœ… Successful: {success_count}")
        print(f"  âŒ Failed: {failed_count}")
        
        if failed_count > 0:
            print("\nFailed operations:")
            for result in results:
                if not result['success']:
                    print(f"  - {result['username']}: {result['message']}")
    
    def save_results(self, results: List[Dict], filename: str = 'user_status_changes.csv'):
        """Save results to CSV"""
        import csv
        
        with open(filename, 'w', newline='') as file:
            fieldnames = ['username', 'action', 'success', 'message', 'timestamp']
            writer = csv.DictWriter(file, fieldnames=fieldnames)
            writer.writeheader()
            
            for result in results:
                result['timestamp'] = datetime.now().isoformat()
                writer.writerow(result)
        
        print(f"\nðŸ“„ Results saved to: {filename}")

# Usage
manager = UserStatusManager(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Enable multiple users
users_to_enable = ["USER1", "USER2", "USER3"]
results = manager.bulk_enable(users_to_enable)
manager.print_summary(results)

# Disable multiple users
users_to_disable = ["USER4", "USER5"]
results = manager.bulk_disable(users_to_disable, concurrent=True)
manager.print_summary(results)

# Save results
manager.save_results(results)
```

## Status Management Patterns

### Scheduled Status Changes

```python
import schedule
import time
from datetime import datetime

class ScheduledUserManager:
    """Schedule user status changes"""
    
    def __init__(self, base_url, token):
        self.manager = UserStatusManager(base_url, token)
        self.scheduled_changes = []
    
    def schedule_disable(self, username, when):
        """Schedule user to be disabled"""
        
        def job():
            print(f"Executing scheduled disable for {username}")
            result = self.manager.disable_user(username)
            self.log_scheduled_change(username, 'disable', result)
        
        if isinstance(when, str):
            # Time-based schedule (e.g., "14:30")
            schedule.every().day.at(when).do(job)
        else:
            # Date-based schedule
            schedule.every().day.at(when.strftime("%H:%M")).do(job).tag(username)
        
        print(f"Scheduled {username} to be disabled at {when}")
    
    def schedule_enable(self, username, when):
        """Schedule user to be enabled"""
        
        def job():
            print(f"Executing scheduled enable for {username}")
            result = self.manager.enable_user(username)
            self.log_scheduled_change(username, 'enable', result)
        
        if isinstance(when, str):
            schedule.every().day.at(when).do(job)
        else:
            schedule.every().day.at(when.strftime("%H:%M")).do(job).tag(username)
        
        print(f"Scheduled {username} to be enabled at {when}")
    
    def schedule_temporary_disable(self, username, duration_hours):
        """Temporarily disable user for specified hours"""
        
        # Disable immediately
        self.manager.disable_user(username)
        print(f"Disabled {username} for {duration_hours} hours")
        
        # Schedule re-enable
        enable_time = datetime.now() + timedelta(hours=duration_hours)
        self.schedule_enable(username, enable_time)
    
    def log_scheduled_change(self, username, action, result):
        """Log scheduled status change"""
        
        log_entry = {
            'timestamp': datetime.now().isoformat(),
            'username': username,
            'action': action,
            'success': result['success'],
            'message': result.get('message')
        }
        
        with open('scheduled_changes.log', 'a') as file:
            file.write(json.dumps(log_entry) + '\n')
    
    def run_scheduler(self):
        """Run the scheduler"""
        print("Scheduler started. Press Ctrl+C to stop.")
        
        while True:
            schedule.run_pending()
            time.sleep(60)

# Usage
scheduler = ScheduledUserManager(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Schedule users to be disabled at end of day
scheduler.schedule_disable("CONTRACTOR1", "18:00")
scheduler.schedule_disable("CONTRACTOR2", "18:00")

# Schedule user to be re-enabled Monday morning
scheduler.schedule_enable("MAINTENANCE_USER", "08:00")

# Temporary disable (e.g., for maintenance)
scheduler.schedule_temporary_disable("TEST_USER", duration_hours=4)

# Run scheduler
# scheduler.run_scheduler()
```

### Conditional Status Management

```python
def manage_user_status_by_criteria(base_url, token):
    """Enable/disable users based on criteria"""
    
    manager = UserStatusManager(base_url, token)
    
    # Get all users
    users = get_all_users(base_url, token)
    
    changes = []
    
    for user in users:
        username = user['userName']
        email = user.get('email', '')
        description = user.get('description', '')
        
        # Disable users with old email domains
        if email.endswith('@olddomain.com'):
            result = manager.disable_user(username)
            changes.append({
                'username': username,
                'action': 'disabled',
                'reason': 'Old email domain'
            })
        
        # Disable inactive contractors
        elif 'contractor' in description.lower() and user.get('lastLogin'):
            last_login = datetime.fromisoformat(user['lastLogin'])
            if (datetime.now() - last_login).days > 90:
                result = manager.disable_user(username)
                changes.append({
                    'username': username,
                    'action': 'disabled',
                    'reason': 'Inactive contractor (>90 days)'
                })
        
        # Enable users who completed training
        elif 'training_completed' in description.lower() and user['enable'] == 'N':
            result = manager.enable_user(username)
            changes.append({
                'username': username,
                'action': 'enabled',
                'reason': 'Training completed'
            })
    
    # Report changes
    print(f"\nðŸ“Š Status Changes Made: {len(changes)}")
    for change in changes:
        print(f"  - {change['username']}: {change['action']} ({change['reason']})")
    
    return changes
```

### Audit Trail

```python
class UserStatusAuditor:
    """Track and audit user status changes"""
    
    def __init__(self, log_file='user_status_audit.log'):
        self.log_file = log_file
    
    def log_status_change(self, username, action, performed_by, reason=None):
        """Log status change for audit"""
        
        entry = {
            'timestamp': datetime.now().isoformat(),
            'username': username,
            'action': action,
            'performed_by': performed_by,
            'reason': reason,
            'ip_address': self.get_client_ip()
        }
        
        with open(self.log_file, 'a') as file:
            file.write(json.dumps(entry) + '\n')
    
    def get_client_ip(self):
        """Get client IP address"""
        import socket
        return socket.gethostbyname(socket.gethostname())
    
    def generate_audit_report(self, days=30):
        """Generate audit report of status changes"""
        
        from datetime import datetime, timedelta
        import json
        
        cutoff = datetime.now() - timedelta(days=days)
        changes = []
        
        with open(self.log_file, 'r') as file:
            for line in file:
                entry = json.loads(line)
                entry_date = datetime.fromisoformat(entry['timestamp'])
                
                if entry_date > cutoff:
                    changes.append(entry)
        
        # Generate statistics
        print(f"\nðŸ“Š User Status Change Audit Report (Last {days} days)")
        print("="*60)
        print(f"Total Changes: {len(changes)}")
        
        # Count by action
        enable_count = sum(1 for c in changes if c['action'] == 'enable')
        disable_count = sum(1 for c in changes if c['action'] == 'disable')
        
        print(f"Enabled: {enable_count}")
        print(f"Disabled: {disable_count}")
        
        # Most changed users
        from collections import Counter
        user_counts = Counter(c['username'] for c in changes)
        
        print("\nMost Changed Users:")
        for username, count in user_counts.most_common(5):
            print(f"  - {username}: {count} changes")
        
        # Recent changes
        print("\nRecent Changes (Last 5):")
        for change in sorted(changes, key=lambda x: x['timestamp'], reverse=True)[:5]:
            print(f"  - {change['timestamp']}: {change['username']} {change['action']}")
            if change.get('reason'):
                print(f"    Reason: {change['reason']}")
        
        return changes

# Usage
auditor = UserStatusAuditor()

# Log a status change
auditor.log_status_change(
    username="USER1",
    action="disable",
    performed_by="admin",
    reason="Account suspension - policy violation"
)

# Generate report
auditor.generate_audit_report(days=30)
```

## Integration Examples

### Active Directory Integration

```python
def sync_status_with_ad(ad_users, epmware_manager):
    """Sync user status with Active Directory"""
    
    changes = []
    
    for ad_user in ad_users:
        username = ad_user['sAMAccountName']
        ad_enabled = ad_user['userAccountControl'] & 2 == 0  # Check if AD account is enabled
        
        try:
            if ad_enabled:
                result = epmware_manager.enable_user(username)
                changes.append({'username': username, 'action': 'enabled', 'source': 'AD'})
            else:
                result = epmware_manager.disable_user(username)
                changes.append({'username': username, 'action': 'disabled', 'source': 'AD'})
                
        except Exception as e:
            print(f"Failed to sync {username}: {e}")
    
    return changes
```

### Notification System

```python
def notify_status_change(username, action, email_service):
    """Send notification when user status changes"""
    
    subject = f"EPMware Account {action.capitalize()}d"
    
    if action == 'disable':
        body = f"""
        Your EPMware account '{username}' has been disabled.
        
        If you believe this is an error, please contact your administrator.
        """
    else:
        body = f"""
        Your EPMware account '{username}' has been enabled.
        
        You can now log in to EPMware.
        """
    
    # Send email (implementation depends on email service)
    email_service.send(
        to=f"{username}@company.com",
        subject=subject,
        body=body
    )
```

## Best Practices

### 1. Verify Before Change

```python
def safe_disable_user(manager, username):
    """Safely disable user with confirmation"""
    
    # Get current user info
    user = get_user_info(username)
    
    if not user:
        print(f"User {username} not found")
        return False
    
    if user.get('enable') == 'N':
        print(f"User {username} is already disabled")
        return True
    
    # Check for active sessions or critical roles
    if user.get('hasActiveSessions'):
        print(f"Warning: User {username} has active sessions")
    
    if 'Admin' in user.get('groups', []):
        print(f"Warning: Disabling admin user {username}")
        response = input("Are you sure? (yes/no): ")
        if response.lower() != 'yes':
            return False
    
    # Disable user
    result = manager.disable_user(username)
    return result['success']
```

### 2. Batch Processing

```python
def process_status_changes_from_csv(csv_file, manager):
    """Process status changes from CSV file"""
    
    import csv
    
    with open(csv_file, 'r') as file:
        reader = csv.DictReader(file)
        
        for row in reader:
            username = row['username']
            action = row['action'].lower()
            
            try:
                if action == 'enable':
                    manager.enable_user(username)
                elif action == 'disable':
                    manager.disable_user(username)
                else:
                    print(f"Unknown action: {action}")
                    
            except Exception as e:
                print(f"Failed to {action} {username}: {e}")
```

### 3. Emergency Disable All

```python
def emergency_disable_all(manager, exclude_users=None):
    """Emergency disable all users except specified"""
    
    exclude_users = exclude_users or ['admin', 'service_account']
    
    print("âš ï¸ EMERGENCY DISABLE - Disabling all user accounts")
    
    users = get_all_users()
    disabled_count = 0
    
    for user in users:
        username = user['userName']
        
        if username in exclude_users:
            print(f"Skipping protected user: {username}")
            continue
        
        try:
            manager.disable_user(username)
            disabled_count += 1
        except Exception as e:
            print(f"Failed to disable {username}: {e}")
    
    print(f"âœ… Disabled {disabled_count} user accounts")
    print(f"Protected users: {', '.join(exclude_users)}")
    
    return disabled_count
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `404 Not Found` | User doesn't exist | Verify username |
| `401 Unauthorized` | Invalid token | Check authentication |
| `409 Conflict` | Status already set | No action needed |
| `500 Internal Server Error` | Server issue | Retry or contact support |

### Error Recovery

```python
def enable_with_retry(manager, username, max_retries=3):
    """Enable user with retry on failure"""
    
    for attempt in range(max_retries):
        try:
            result = manager.enable_user(username)
            if result['success']:
                return result
        except Exception as e:
            if attempt < max_retries - 1:
                print(f"Attempt {attempt + 1} failed, retrying...")
                time.sleep(2 ** attempt)  # Exponential backoff
            else:
                raise e
```

## Related Operations

- [Create User](create.md) - Create new users
- [Update User](update.md) - Modify user details
- [Get Users](get-users.md) - List all users
- [Assign Groups](../groups/assign-groups.md) - Manage user groups

## Next Steps

After changing user status:
1. Verify the change was applied
2. Send notification to affected user
3. Update audit logs
4. Review dependent systems