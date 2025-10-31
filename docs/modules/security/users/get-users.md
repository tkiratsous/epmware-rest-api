# Get Users

The Get Users API allows you to retrieve information about all users or filter by user type (NATIVE or SAML) in EPMware.

## Overview

This endpoint provides a way to list all users in the system with their details, including username, name, email, status, and type. It's useful for user management, auditing, and reporting purposes.

## Endpoint Details

### URL Structure

```
GET /service/api/security/users
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/security/users
```

### Method

`GET`

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `userType` | Query | No | All | Filter by user type (NATIVE or SAML) |
| `pageSize` | Query | No | 10000 | Number of results per page |

## Request Examples

### Using curl

```bash
# Get all users
curl GET 'https://demo.epmwarecloud.com/service/api/security/users' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

# Get only NATIVE users
curl GET 'https://demo.epmwarecloud.com/service/api/security/users?userType=NATIVE' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

# Get only SAML users
curl GET 'https://demo.epmwarecloud.com/service/api/security/users?userType=SAML' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

# Get with custom page size
curl GET 'https://demo.epmwarecloud.com/service/api/security/users?pageSize=500' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

### Using Python

```python
import requests
from typing import List, Dict, Optional

def get_users(base_url: str, token: str, user_type: Optional[str] = None, 
              page_size: int = 10000) -> List[Dict]:
    """
    Retrieve users from EPMware
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        user_type: Filter by NATIVE or SAML (optional)
        page_size: Number of results per page
    
    Returns:
        list: List of user dictionaries
    """
    url = f"{base_url}/service/api/security/users"
    
    headers = {
        'Authorization': f'Token {token}'
    }
    
    params = {}
    if user_type:
        params['userType'] = user_type
    if page_size != 10000:
        params['pageSize'] = page_size
    
    response = requests.get(url, headers=headers, params=params)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to get users: {response.status_code} - {response.text}")

# Usage examples

# Get all users
all_users = get_users(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
)

print(f"Total users: {len(all_users)}")

# Get only NATIVE users
native_users = get_users(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    user_type="NATIVE"
)

print(f"Native users: {len(native_users)}")

# Get only SAML users
saml_users = get_users(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    user_type="SAML"
)

print(f"SAML users: {len(saml_users)}")

# Display user details
for user in all_users[:5]:  # First 5 users
    print(f"\nUsername: {user['userName']}")
    print(f"  Name: {user.get('firstName', '')} {user.get('lastName', '')}")
    print(f"  Email: {user.get('email', 'N/A')}")
    print(f"  Type: {user.get('userType', 'N/A')}")
    print(f"  Enabled: {user.get('enable', 'N/A')}")
```

### Using PowerShell

```powershell
function Get-EPMwareUsers {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [ValidateSet("NATIVE", "SAML", "ALL")]
        [string]$UserType = "ALL",
        
        [int]$PageSize = 10000
    )
    
    $url = "$BaseUrl/service/api/security/users"
    
    $headers = @{
        "Authorization" = "Token $Token"
    }
    
    # Build query parameters
    $params = @{}
    if ($UserType -ne "ALL") {
        $params.userType = $UserType
    }
    if ($PageSize -ne 10000) {
        $params.pageSize = $PageSize
    }
    
    try {
        if ($params.Count -gt 0) {
            $response = Invoke-RestMethod `
                -Uri $url `
                -Method Get `
                -Headers $headers `
                -Body $params
        }
        else {
            $response = Invoke-RestMethod `
                -Uri $url `
                -Method Get `
                -Headers $headers
        }
        
        Write-Host "âœ… Retrieved $($response.Count) users" -ForegroundColor Green
        
        # Display summary
        $enabledCount = ($response | Where-Object { $_.enable -eq 'Y' }).Count
        $disabledCount = ($response | Where-Object { $_.enable -eq 'N' }).Count
        
        Write-Host "`nUser Summary:" -ForegroundColor Cyan
        Write-Host "  Total Users: $($response.Count)"
        Write-Host "  Enabled: $enabledCount"
        Write-Host "  Disabled: $disabledCount"
        
        if ($UserType -eq "ALL") {
            $nativeCount = ($response | Where-Object { $_.userType -eq 'NATIVE' }).Count
            $samlCount = ($response | Where-Object { $_.userType -eq 'SAML' }).Count
            
            Write-Host "  Native Users: $nativeCount"
            Write-Host "  SAML Users: $samlCount"
        }
        
        return $response
    }
    catch {
        Write-Host "âŒ Failed to get users: $_" -ForegroundColor Red
        throw
    }
}

# Usage examples

# Get all users
$allUsers = Get-EPMwareUsers `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7"

# Get only NATIVE users
$nativeUsers = Get-EPMwareUsers `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -UserType "NATIVE"

# Display users in table format
$allUsers | Select-Object userName, firstName, lastName, email, userType, enable | Format-Table

# Export to CSV
$allUsers | Export-Csv -Path "epmware_users.csv" -NoTypeInformation
```

## Response Format

### Successful Response

```json
[
  {
    "userName": "john.smith",
    "firstName": "John",
    "lastName": "Smith",
    "email": "john.smith@example.com",
    "enable": "Y",
    "userType": "NATIVE",
    "description": "Finance department user"
  },
  {
    "userName": "jane.doe",
    "firstName": "Jane",
    "lastName": "Doe",
    "email": "jane.doe@example.com",
    "enable": "Y",
    "userType": "SAML",
    "description": "IT administrator"
  }
]
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `userName` | String | Unique username |
| `firstName` | String | User's first name |
| `lastName` | String | User's last name |
| `email` | String | Email address |
| `enable` | String | Enable status (Y/N) |
| `userType` | String | User type (NATIVE/SAML) |
| `description` | String | User description |

## User Analysis and Reporting

### Comprehensive User Analysis

```python
import pandas as pd
from datetime import datetime
import json

class UserAnalyzer:
    """Analyze EPMware user data"""
    
    def __init__(self, base_url: str, token: str):
        self.base_url = base_url
        self.token = token
    
    def get_all_users(self) -> pd.DataFrame:
        """Get all users and return as DataFrame"""
        
        users = get_users(self.base_url, self.token)
        df = pd.DataFrame(users)
        
        # Add calculated fields
        if not df.empty:
            df['full_name'] = df['firstName'].fillna('') + ' ' + df['lastName'].fillna('')
            df['is_enabled'] = df['enable'] == 'Y'
            df['email_domain'] = df['email'].str.split('@').str[-1]
        
        return df
    
    def generate_user_report(self):
        """Generate comprehensive user report"""
        
        df = self.get_all_users()
        
        if df.empty:
            print("No users found")
            return
        
        print("ðŸ“Š EPMware User Analysis Report")
        print("="*60)
        print(f"Report Date: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
        print("-"*60)
        
        # Overall statistics
        print("\nðŸ“ˆ Overall Statistics:")
        print(f"  Total Users: {len(df)}")
        print(f"  Active Users: {df['is_enabled'].sum()}")
        print(f"  Inactive Users: {(~df['is_enabled']).sum()}")
        
        # User type distribution
        print("\nðŸ‘¥ User Type Distribution:")
        type_counts = df['userType'].value_counts()
        for user_type, count in type_counts.items():
            percentage = (count / len(df)) * 100
            print(f"  {user_type}: {count} ({percentage:.1f}%)")
        
        # Email domain analysis
        print("\nðŸ“§ Top Email Domains:")
        domain_counts = df['email_domain'].value_counts().head(5)
        for domain, count in domain_counts.items():
            print(f"  {domain}: {count} users")
        
        # Status by user type
        print("\nðŸ” Status by User Type:")
        status_pivot = pd.crosstab(df['userType'], df['enable'], margins=True)
        print(status_pivot.to_string())
        
        # Users without email
        no_email = df[df['email'].isna() | (df['email'] == '')]
        if not no_email.empty:
            print(f"\nâš ï¸ Users without email: {len(no_email)}")
            for _, user in no_email.head(5).iterrows():
                print(f"  - {user['userName']}")
        
        # Duplicate email check
        duplicate_emails = df[df.duplicated('email', keep=False) & df['email'].notna()]
        if not duplicate_emails.empty:
            print(f"\nâš ï¸ Duplicate email addresses found:")
            for email in duplicate_emails['email'].unique()[:5]:
                users_with_email = duplicate_emails[duplicate_emails['email'] == email]['userName'].tolist()
                print(f"  {email}: {', '.join(users_with_email)}")
        
        return df
    
    def export_user_data(self, format='csv'):
        """Export user data in various formats"""
        
        df = self.get_all_users()
        
        timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
        
        if format == 'csv':
            filename = f"epmware_users_{timestamp}.csv"
            df.to_csv(filename, index=False)
        elif format == 'excel':
            filename = f"epmware_users_{timestamp}.xlsx"
            with pd.ExcelWriter(filename, engine='openpyxl') as writer:
                df.to_excel(writer, sheet_name='Users', index=False)
                
                # Add summary sheet
                summary_df = pd.DataFrame({
                    'Metric': ['Total Users', 'Active Users', 'Inactive Users', 
                              'Native Users', 'SAML Users'],
                    'Count': [
                        len(df),
                        df['is_enabled'].sum(),
                        (~df['is_enabled']).sum(),
                        (df['userType'] == 'NATIVE').sum(),
                        (df['userType'] == 'SAML').sum()
                    ]
                })
                summary_df.to_excel(writer, sheet_name='Summary', index=False)
        elif format == 'json':
            filename = f"epmware_users_{timestamp}.json"
            df.to_json(filename, orient='records', indent=2)
        
        print(f"ðŸ“„ User data exported to: {filename}")
        return filename
    
    def find_users(self, search_criteria: Dict) -> pd.DataFrame:
        """Find users matching specific criteria"""
        
        df = self.get_all_users()
        
        # Apply filters
        for field, value in search_criteria.items():
            if field in df.columns:
                if isinstance(value, str):
                    # Case-insensitive string matching
                    df = df[df[field].str.contains(value, case=False, na=False)]
                else:
                    df = df[df[field] == value]
        
        return df
    
    def get_inactive_users(self, include_disabled=True):
        """Get inactive users"""
        
        df = self.get_all_users()
        
        if include_disabled:
            inactive = df[df['enable'] == 'N']
        else:
            # Additional logic for last login if available
            inactive = df[df['enable'] == 'N']
        
        print(f"\nðŸ”’ Inactive Users Report")
        print("-"*40)
        print(f"Total Inactive: {len(inactive)}")
        
        if not inactive.empty:
            print("\nInactive Users:")
            for _, user in inactive.iterrows():
                print(f"  - {user['userName']} ({user.get('email', 'No email')})")
        
        return inactive

# Usage
analyzer = UserAnalyzer(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Generate report
df = analyzer.generate_user_report()

# Export data
analyzer.export_user_data(format='excel')

# Find specific users
contractors = analyzer.find_users({'description': 'contractor'})
print(f"\nFound {len(contractors)} contractor users")

# Get inactive users
inactive = analyzer.get_inactive_users()
```

### User Comparison

```python
def compare_user_lists(current_users: List[Dict], previous_users: List[Dict]):
    """Compare two user lists to identify changes"""
    
    current_set = {u['userName'] for u in current_users}
    previous_set = {u['userName'] for u in previous_users}
    
    # Find changes
    added_users = current_set - previous_set
    removed_users = previous_set - current_set
    existing_users = current_set & previous_set
    
    # Check for property changes
    changes = []
    
    current_dict = {u['userName']: u for u in current_users}
    previous_dict = {u['userName']: u for u in previous_users}
    
    for username in existing_users:
        current = current_dict[username]
        previous = previous_dict[username]
        
        user_changes = []
        for field in ['firstName', 'lastName', 'email', 'enable', 'description']:
            if current.get(field) != previous.get(field):
                user_changes.append({
                    'field': field,
                    'old': previous.get(field),
                    'new': current.get(field)
                })
        
        if user_changes:
            changes.append({
                'username': username,
                'changes': user_changes
            })
    
    # Generate report
    print("ðŸ‘¥ User Comparison Report")
    print("="*50)
    
    if added_users:
        print(f"\nâœ… New Users ({len(added_users)}):")
        for username in sorted(added_users):
            user = current_dict[username]
            print(f"  - {username} ({user.get('email', 'No email')})")
    
    if removed_users:
        print(f"\nâŒ Removed Users ({len(removed_users)}):")
        for username in sorted(removed_users):
            user = previous_dict[username]
            print(f"  - {username} ({user.get('email', 'No email')})")
    
    if changes:
        print(f"\nðŸ”„ Modified Users ({len(changes)}):")
        for change in changes[:10]:  # Show first 10
            print(f"  - {change['username']}:")
            for field_change in change['changes']:
                print(f"    {field_change['field']}: "
                      f"'{field_change['old']}' â†’ '{field_change['new']}'")
    
    return {
        'added': list(added_users),
        'removed': list(removed_users),
        'modified': changes
    }
```

## User Search and Filtering

### Advanced Search

```python
class UserSearcher:
    """Advanced user search functionality"""
    
    def __init__(self, users: List[Dict]):
        self.users = users
        self.df = pd.DataFrame(users) if users else pd.DataFrame()
    
    def search(self, query: str) -> List[Dict]:
        """Search users by multiple fields"""
        
        if self.df.empty:
            return []
        
        # Search across multiple fields
        mask = (
            self.df['userName'].str.contains(query, case=False, na=False) |
            self.df['firstName'].str.contains(query, case=False, na=False) |
            self.df['lastName'].str.contains(query, case=False, na=False) |
            self.df['email'].str.contains(query, case=False, na=False) |
            self.df['description'].str.contains(query, case=False, na=False)
        )
        
        results = self.df[mask]
        return results.to_dict('records')
    
    def filter_by_email_domain(self, domain: str) -> List[Dict]:
        """Filter users by email domain"""
        
        if self.df.empty:
            return []
        
        mask = self.df['email'].str.endswith(f'@{domain}', na=False)
        results = self.df[mask]
        return results.to_dict('records')
    
    def filter_by_status(self, enabled: bool = True) -> List[Dict]:
        """Filter users by enable status"""
        
        if self.df.empty:
            return []
        
        status = 'Y' if enabled else 'N'
        results = self.df[self.df['enable'] == status]
        return results.to_dict('records')
    
    def filter_by_type(self, user_type: str) -> List[Dict]:
        """Filter users by type"""
        
        if self.df.empty:
            return []
        
        results = self.df[self.df['userType'] == user_type]
        return results.to_dict('records')
    
    def get_users_without_groups(self, group_data: Dict) -> List[Dict]:
        """Find users not assigned to any groups"""
        
        users_with_groups = set()
        for group_users in group_data.values():
            users_with_groups.update(group_users)
        
        all_usernames = set(self.df['userName'])
        users_without_groups = all_usernames - users_with_groups
        
        results = self.df[self.df['userName'].isin(users_without_groups)]
        return results.to_dict('records')

# Usage
users = get_users(base_url, token)
searcher = UserSearcher(users)

# Search for users
results = searcher.search("john")
print(f"Found {len(results)} users matching 'john'")

# Filter by domain
company_users = searcher.filter_by_email_domain("company.com")
print(f"Found {len(company_users)} users with @company.com email")

# Get enabled users only
active_users = searcher.filter_by_status(enabled=True)
print(f"Found {len(active_users)} active users")
```

## Pagination Handler

```python
class UserPaginator:
    """Handle paginated user retrieval"""
    
    def __init__(self, base_url: str, token: str):
        self.base_url = base_url
        self.token = token
    
    def get_all_users_paginated(self, page_size: int = 100) -> List[Dict]:
        """Get all users with pagination"""
        
        all_users = []
        page = 1
        
        while True:
            # Note: This assumes the API supports page parameter
            # Adjust based on actual API implementation
            users = self._get_page(page, page_size)
            
            if not users:
                break
            
            all_users.extend(users)
            
            if len(users) < page_size:
                break
            
            page += 1
            print(f"Retrieved page {page}, total users so far: {len(all_users)}")
        
        return all_users
    
    def _get_page(self, page: int, page_size: int) -> List[Dict]:
        """Get a single page of users"""
        
        # Implementation depends on API pagination support
        # This is a placeholder
        return get_users(self.base_url, self.token, page_size=page_size)
    
    def process_users_in_batches(self, batch_size: int, processor_func):
        """Process users in batches to manage memory"""
        
        page = 1
        total_processed = 0
        
        while True:
            users = self._get_page(page, batch_size)
            
            if not users:
                break
            
            # Process batch
            processor_func(users)
            total_processed += len(users)
            
            print(f"Processed batch {page}, total: {total_processed}")
            
            if len(users) < batch_size:
                break
            
            page += 1
        
        return total_processed

# Usage
paginator = UserPaginator(base_url, token)

# Process users in batches
def process_batch(users):
    """Process a batch of users"""
    for user in users:
        # Do something with each user
        pass

total = paginator.process_users_in_batches(
    batch_size=100,
    processor_func=process_batch
)

print(f"Processed {total} users in total")
```

## Integration with Other Systems

### Export to LDAP Format

```python
def export_to_ldif(users: List[Dict], filename: str = 'users.ldif'):
    """Export users to LDIF format for LDAP import"""
    
    with open(filename, 'w') as file:
        for user in users:
            username = user['userName']
            
            # Write LDIF entry
            file.write(f"dn: uid={username},ou=users,dc=company,dc=com\n")
            file.write(f"objectClass: inetOrgPerson\n")
            file.write(f"objectClass: organizationalPerson\n")
            file.write(f"objectClass: person\n")
            file.write(f"objectClass: top\n")
            file.write(f"uid: {username}\n")
            file.write(f"cn: {user.get('firstName', '')} {user.get('lastName', '')}\n")
            file.write(f"givenName: {user.get('firstName', '')}\n")
            file.write(f"sn: {user.get('lastName', username)}\n")
            file.write(f"mail: {user.get('email', '')}\n")
            file.write(f"userPassword: {{SSHA}}placeholder\n")
            
            if user.get('description'):
                file.write(f"description: {user['description']}\n")
            
            file.write("\n")
    
    print(f"ðŸ“„ Exported {len(users)} users to {filename}")
```

### Sync with HR System

```python
def sync_with_hr_system(epmware_users: List[Dict], hr_users: List[Dict]):
    """Sync EPMware users with HR system data"""
    
    # Create lookup dictionaries
    epmware_dict = {u['email']: u for u in epmware_users if u.get('email')}
    hr_dict = {u['email']: u for u in hr_users}
    
    updates_needed = []
    new_users = []
    
    for email, hr_user in hr_dict.items():
        if email in epmware_dict:
            # Check if update needed
            epm_user = epmware_dict[email]
            
            if (hr_user.get('firstName') != epm_user.get('firstName') or
                hr_user.get('lastName') != epm_user.get('lastName') or
                hr_user.get('department') != epm_user.get('description')):
                
                updates_needed.append({
                    'username': epm_user['userName'],
                    'updates': {
                        'firstName': hr_user.get('firstName'),
                        'lastName': hr_user.get('lastName'),
                        'description': f"Department: {hr_user.get('department')}"
                    }
                })
        else:
            # New user from HR
            new_users.append({
                'userName': hr_user.get('employeeId'),
                'firstName': hr_user.get('firstName'),
                'lastName': hr_user.get('lastName'),
                'email': email,
                'description': f"Department: {hr_user.get('department')}"
            })
    
    print(f"ðŸ“Š HR Sync Summary:")
    print(f"  Updates needed: {len(updates_needed)}")
    print(f"  New users to create: {len(new_users)}")
    
    return updates_needed, new_users
```

## Monitoring and Alerts

```python
class UserMonitor:
    """Monitor user accounts and send alerts"""
    
    def __init__(self, base_url: str, token: str):
        self.base_url = base_url
        self.token = token
    
    def check_user_anomalies(self):
        """Check for user account anomalies"""
        
        users = get_users(self.base_url, self.token)
        anomalies = []
        
        for user in users:
            # Check for missing email
            if not user.get('email'):
                anomalies.append({
                    'type': 'missing_email',
                    'username': user['userName'],
                    'message': 'User has no email address'
                })
            
            # Check for generic usernames
            if user['userName'].lower().startswith(('test', 'temp', 'demo')):
                anomalies.append({
                    'type': 'test_account',
                    'username': user['userName'],
                    'message': 'Possible test account in production'
                })
            
            # Check for disabled admin accounts
            if 'admin' in user.get('description', '').lower() and user['enable'] == 'N':
                anomalies.append({
                    'type': 'disabled_admin',
                    'username': user['userName'],
                    'message': 'Admin account is disabled'
                })
        
        if anomalies:
            print("âš ï¸ User Account Anomalies Detected:")
            for anomaly in anomalies:
                print(f"  - {anomaly['username']}: {anomaly['message']}")
        
        return anomalies
    
    def generate_user_metrics(self):
        """Generate user metrics for dashboard"""
        
        users = get_users(self.base_url, self.token)
        df = pd.DataFrame(users)
        
        metrics = {
            'total_users': len(df),
            'active_users': (df['enable'] == 'Y').sum(),
            'inactive_users': (df['enable'] == 'N').sum(),
            'native_users': (df['userType'] == 'NATIVE').sum(),
            'saml_users': (df['userType'] == 'SAML').sum(),
            'users_without_email': df['email'].isna().sum(),
            'unique_email_domains': df['email'].str.split('@').str[-1].nunique(),
            'timestamp': datetime.now().isoformat()
        }
        
        return metrics

# Usage
monitor = UserMonitor(base_url, token)

# Check for anomalies
anomalies = monitor.check_user_anomalies()

# Get metrics
metrics = monitor.generate_user_metrics()
print("\nðŸ“Š User Metrics:")
for key, value in metrics.items():
    print(f"  {key}: {value}")
```

## Best Practices

### 1. Cache User Data

```python
import pickle
from datetime import datetime, timedelta

class UserCache:
    """Cache user data to reduce API calls"""
    
    def __init__(self, cache_file='user_cache.pkl', ttl_minutes=60):
        self.cache_file = cache_file
        self.ttl = timedelta(minutes=ttl_minutes)
    
    def get_users(self, base_url, token, force_refresh=False):
        """Get users with caching"""
        
        if not force_refresh:
            cached_data = self._load_cache()
            if cached_data:
                return cached_data
        
        # Fetch fresh data
        users = get_users(base_url, token)
        self._save_cache(users)
        
        return users
    
    def _load_cache(self):
        """Load cached data if valid"""
        
        try:
            with open(self.cache_file, 'rb') as file:
                cache = pickle.load(file)
                
                if datetime.now() - cache['timestamp'] < self.ttl:
                    print("ðŸ“¦ Using cached user data")
                    return cache['data']
        except (FileNotFoundError, KeyError):
            pass
        
        return None
    
    def _save_cache(self, data):
        """Save data to cache"""
        
        cache = {
            'timestamp': datetime.now(),
            'data': data
        }
        
        with open(self.cache_file, 'wb') as file:
            pickle.dump(cache, file)
        
        print("ðŸ’¾ User data cached")
```

### 2. Efficient Filtering

```python
def filter_users_efficiently(users: List[Dict], filters: Dict) -> List[Dict]:
    """Apply multiple filters efficiently"""
    
    # Convert to DataFrame for efficient filtering
    df = pd.DataFrame(users)
    
    # Apply all filters
    mask = pd.Series([True] * len(df))
    
    for field, value in filters.items():
        if field in df.columns:
            if isinstance(value, list):
                mask &= df[field].isin(value)
            elif isinstance(value, str) and '*' in value:
                # Wildcard matching
                pattern = value.replace('*', '.*')
                mask &= df[field].str.match(pattern, na=False)
            else:
                mask &= df[field] == value
    
    return df[mask].to_dict('records')

# Usage
filtered = filter_users_efficiently(users, {
    'userType': 'NATIVE',
    'enable': 'Y',
    'email': '*@company.com'
})
```

### 3. Parallel Processing

```python
from concurrent.futures import ThreadPoolExecutor

def process_users_parallel(users: List[Dict], processor_func, max_workers=10):
    """Process users in parallel"""
    
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        futures = [executor.submit(processor_func, user) for user in users]
        
        results = []
        for future in concurrent.futures.as_completed(futures):
            try:
                result = future.result()
                results.append(result)
            except Exception as e:
                print(f"Error processing user: {e}")
        
        return results
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Invalid token | Verify authentication |
| `403 Forbidden` | Insufficient permissions | Check user permissions |
| `500 Internal Server Error` | Server issue | Retry with backoff |

## Related Operations

- [Create User](create.md) - Create new users
- [Update User](update.md) - Modify user details
- [Enable/Disable User](enable-disable.md) - Manage user status
- [User Groups](../groups/) - Manage group assignments

## Next Steps

After retrieving users:
1. Analyze user data for insights
2. Export for reporting or backup
3. Sync with external systems
4. Monitor for anomalies