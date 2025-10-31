# Create SSO User

The Create SSO User API allows you to programmatically create new Single Sign-On (SSO) users in EPMware. This endpoint is essential for automating user provisioning and integration with identity management systems.

## Overview

This API creates new SSO users with specified attributes. Created users can then be assigned to security groups and granted appropriate permissions within EPMware.

!!! info "SSO vs Native Users"
    This API creates SSO users only. Native users must be created through the EPMware UI.

## Endpoint Details

### URL Structure

```
POST /service/api/security/users/sso/create
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/security/users/sso/create
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `userName` | String | Yes | Unique username | `SSO_USER1` |
| `firstName` | String | Yes | User's first name | `John` |
| `lastName` | String | No | User's last name | `Smith` |
| `email` | String | Yes | Email address | `john.smith@example.com` |
| `description` | String | No | User description | `Finance department user` |

## Request Examples

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/security/users/sso/create' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "SSO_USER1",
    "firstName": "SSO",
    "lastName": "USER1",
    "email": "SSO_USER1@example.com",
    "description": "New user creation"
  }'
```

### Using Python

```python
import requests
import json

def create_sso_user(base_url, token, user_data):
    """
    Create a new SSO user
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        user_data: Dictionary containing user information
    
    Returns:
        dict: Created user details
    """
    url = f"{base_url}/service/api/security/users/sso/create"
    
    headers = {
        'Authorization': f'Token {token}',
        'Content-Type': 'application/json'
    }
    
    response = requests.post(url, headers=headers, json=user_data)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to create user: {response.status_code} - {response.text}")

# Usage
user_data = {
    "userName": "SSO_USER1",
    "firstName": "John",
    "lastName": "Smith",
    "email": "john.smith@example.com",
    "description": "Finance department user"
}

result = create_sso_user(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    user_data=user_data
)

print(f"User created: {result['userName']}")
print(f"User ID: {result['id']}")
print(f"Status: {'Enabled' if result['enable'] == 'Y' else 'Disabled'}")
```

### Using PowerShell

```powershell
function New-EPMwareSSO User {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$UserName,
        
        [Parameter(Mandatory=$true)]
        [string]$FirstName,
        
        [string]$LastName,
        
        [Parameter(Mandatory=$true)]
        [string]$Email,
        
        [string]$Description
    )
    
    $url = "$BaseUrl/service/api/security/users/sso/create"
    
    $headers = @{
        "Authorization" = "Token $Token"
        "Content-Type" = "application/json"
    }
    
    $body = @{
        userName = $UserName
        firstName = $FirstName
        email = $Email
    }
    
    # Add optional parameters if provided
    if ($LastName) { $body.lastName = $LastName }
    if ($Description) { $body.description = $Description }
    
    $jsonBody = $body | ConvertTo-Json
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Post `
            -Headers $headers `
            -Body $jsonBody
        
        Write-Host "âœ… User created successfully!" -ForegroundColor Green
        Write-Host "Username: $($response.userName)"
        Write-Host "User ID: $($response.id)"
        Write-Host "Email: $($response.email)"
        Write-Host "Status: $(if($response.enable -eq 'Y'){'Enabled'}else{'Disabled'})"
        
        return $response
    }
    catch {
        Write-Host "âŒ Failed to create user: $_" -ForegroundColor Red
        throw
    }
}

# Usage
$newUser = New-EPMwareSSO User `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -UserName "SSO_USER1" `
    -FirstName "John" `
    -LastName "Smith" `
    -Email "john.smith@example.com" `
    -Description "Finance department user"
```

## Response Format

### Successful Response

```json
{
  "id": "586",
  "createDate": "2025-06-06T13:13:54Z",
  "updateDate": "2025-06-06T13:13:54Z",
  "createdBy": "330",
  "updatedBy": "330",
  "userName": "SSO_USER1",
  "firstName": "SSO",
  "lastName": "USER1",
  "email": "SSO_USER1@example.com",
  "description": "New user creation",
  "enable": "Y",
  "samlEnabled": "false"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | String | Unique user ID |
| `createDate` | DateTime | User creation timestamp |
| `updateDate` | DateTime | Last update timestamp |
| `createdBy` | String | ID of user who created this user |
| `updatedBy` | String | ID of user who last updated |
| `userName` | String | Username |
| `firstName` | String | First name |
| `lastName` | String | Last name |
| `email` | String | Email address |
| `description` | String | User description |
| `enable` | String | Enable status (Y/N) |
| `samlEnabled` | String | SAML status |

## Response Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `200` | User successfully created | Continue with user setup |
| `401` | Unauthorized - Invalid token | Verify authentication |
| `400` | Bad Request - Invalid data | Check required fields |
| `409` | Conflict - User already exists | Use different username |

## Complete User Provisioning Workflow

```python
import requests
import json
from typing import List, Dict

class UserProvisioning:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.token = token
        self.headers = {
            'Authorization': f'Token {token}',
            'Content-Type': 'application/json'
        }
    
    def create_user(self, user_data: Dict) -> Dict:
        """Create a new SSO user"""
        url = f"{self.base_url}/service/api/security/users/sso/create"
        response = requests.post(url, headers=self.headers, json=user_data)
        
        if response.status_code == 200:
            return response.json()
        else:
            raise Exception(f"Failed to create user: {response.text}")
    
    def assign_groups(self, username: str, groups: List[str]) -> bool:
        """Assign user to security groups"""
        url = f"{self.base_url}/service/api/security/users/sso/assign_groups"
        data = {
            "userName": username,
            "groupNames": groups
        }
        
        response = requests.post(url, headers=self.headers, json=data)
        return response.status_code == 200
    
    def enable_user(self, username: str) -> bool:
        """Enable a user account"""
        url = f"{self.base_url}/service/api/security/users/sso/enable/{username}"
        response = requests.patch(url, headers=self.headers)
        return response.status_code == 200
    
    def provision_user(self, user_info: Dict, groups: List[str] = None):
        """Complete user provisioning process"""
        print(f"ðŸš€ Provisioning user: {user_info['userName']}")
        
        try:
            # Step 1: Create user
            print("1ï¸âƒ£ Creating user...")
            user = self.create_user(user_info)
            user_id = user['id']
            username = user['userName']
            print(f"   âœ… User created with ID: {user_id}")
            
            # Step 2: Assign to groups (if specified)
            if groups:
                print(f"2ï¸âƒ£ Assigning to groups: {', '.join(groups)}")
                if self.assign_groups(username, groups):
                    print("   âœ… Groups assigned successfully")
                else:
                    print("   âš ï¸ Failed to assign some groups")
            
            # Step 3: Ensure user is enabled
            if user['enable'] != 'Y':
                print("3ï¸âƒ£ Enabling user account...")
                if self.enable_user(username):
                    print("   âœ… User enabled")
            
            print(f"\nâœ… User provisioning complete for: {username}")
            return user
            
        except Exception as e:
            print(f"\nâŒ Provisioning failed: {e}")
            raise

# Usage example
provisioner = UserProvisioning(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7"
)

# Define new user
new_user = {
    "userName": "JSMITH",
    "firstName": "John",
    "lastName": "Smith",
    "email": "jsmith@company.com",
    "description": "Finance Manager - Budget Planning"
}

# Provision with group assignments
user = provisioner.provision_user(
    user_info=new_user,
    groups=["Planners", "Reviewers", "Finance_Team"]
)
```

## Bulk User Creation

### CSV Import Example

```python
import csv
import time

def bulk_create_users_from_csv(csv_file, base_url, token):
    """
    Create multiple users from CSV file
    
    CSV Format:
    userName,firstName,lastName,email,description,groups
    """
    provisioner = UserProvisioning(base_url, token)
    results = []
    
    with open(csv_file, 'r') as file:
        reader = csv.DictReader(file)
        total = sum(1 for _ in reader)
        file.seek(0)
        reader = csv.DictReader(file)
        
        print(f"ðŸ“‹ Processing {total} users from CSV...")
        
        for idx, row in enumerate(reader, 1):
            print(f"\n[{idx}/{total}] Processing: {row['userName']}")
            
            user_data = {
                "userName": row['userName'],
                "firstName": row['firstName'],
                "lastName": row.get('lastName', ''),
                "email": row['email'],
                "description": row.get('description', '')
            }
            
            # Parse groups if provided
            groups = []
            if row.get('groups'):
                groups = [g.strip() for g in row['groups'].split(';')]
            
            try:
                user = provisioner.provision_user(user_data, groups)
                results.append({
                    'username': user['userName'],
                    'status': 'Success',
                    'id': user['id']
                })
                print(f"âœ… Created: {user['userName']}")
                
            except Exception as e:
                results.append({
                    'username': row['userName'],
                    'status': 'Failed',
                    'error': str(e)
                })
                print(f"âŒ Failed: {e}")
            
            # Rate limiting
            time.sleep(0.5)
    
    # Summary
    print("\n" + "="*50)
    print("ðŸ“Š Bulk Creation Summary:")
    success_count = sum(1 for r in results if r['status'] == 'Success')
    failed_count = len(results) - success_count
    
    print(f"âœ… Successful: {success_count}")
    print(f"âŒ Failed: {failed_count}")
    
    # Save results
    output_file = 'user_creation_results.csv'
    with open(output_file, 'w', newline='') as file:
        fieldnames = ['username', 'status', 'id', 'error']
        writer = csv.DictWriter(file, fieldnames=fieldnames)
        writer.writeheader()
        writer.writerows(results)
    
    print(f"\nðŸ“„ Results saved to: {output_file}")
    
    return results

# Usage
results = bulk_create_users_from_csv(
    csv_file='new_users.csv',
    base_url='https://demo.epmwarecloud.com',
    token='your-token-here'
)
```

## Validation and Error Handling

### User Data Validation

```python
import re

def validate_user_data(user_data):
    """Validate user data before creation"""
    errors = []
    
    # Required fields
    required = ['userName', 'firstName', 'email']
    for field in required:
        if not user_data.get(field):
            errors.append(f"Missing required field: {field}")
    
    # Username validation (alphanumeric and underscore only)
    if 'userName' in user_data:
        username = user_data['userName']
        if not re.match(r'^[A-Za-z0-9_]+$', username):
            errors.append("Username can only contain letters, numbers, and underscores")
        if len(username) < 3:
            errors.append("Username must be at least 3 characters")
        if len(username) > 50:
            errors.append("Username cannot exceed 50 characters")
    
    # Email validation
    if 'email' in user_data:
        email = user_data['email']
        email_pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
        if not re.match(email_pattern, email):
            errors.append("Invalid email format")
    
    # Name validation
    for field in ['firstName', 'lastName']:
        if field in user_data and user_data[field]:
            if len(user_data[field]) > 100:
                errors.append(f"{field} cannot exceed 100 characters")
    
    if errors:
        raise ValueError(f"Validation failed: {', '.join(errors)}")
    
    return True

# Usage
try:
    user_data = {
        "userName": "JSMITH",
        "firstName": "John",
        "email": "jsmith@company.com"
    }
    
    if validate_user_data(user_data):
        # Proceed with user creation
        create_sso_user(base_url, token, user_data)
        
except ValueError as e:
    print(f"âŒ Validation error: {e}")
```

## Best Practices

### 1. Username Conventions

```python
def generate_username(first_name, last_name, domain_prefix="SSO"):
    """Generate standardized username"""
    # Remove special characters and spaces
    first = re.sub(r'[^a-zA-Z]', '', first_name)[:1].upper()
    last = re.sub(r'[^a-zA-Z]', '', last_name)[:7].upper()
    
    # Generate base username
    base_username = f"{domain_prefix}_{first}{last}"
    
    # You might want to check for uniqueness here
    # and append numbers if needed
    
    return base_username

# Example
username = generate_username("John", "Smith", "FIN")
# Returns: "FIN_JSMITH"
```

### 2. Audit Trail

```python
import logging
from datetime import datetime

def setup_audit_logging():
    """Configure audit logging for user creation"""
    logging.basicConfig(
        filename=f'user_creation_{datetime.now().strftime("%Y%m%d")}.log',
        level=logging.INFO,
        format='%(asctime)s - %(levelname)s - %(message)s'
    )

def audit_user_creation(user_data, result, created_by):
    """Log user creation for audit purposes"""
    if result.get('id'):
        logging.info(
            f"User created - ID: {result['id']}, "
            f"Username: {user_data['userName']}, "
            f"Email: {user_data['email']}, "
            f"Created by: {created_by}"
        )
    else:
        logging.error(
            f"User creation failed - Username: {user_data['userName']}, "
            f"Error: {result.get('error', 'Unknown')}"
        )
```

### 3. Integration with Identity Providers

```python
class IdentityProviderSync:
    """Sync users from external identity provider"""
    
    def __init__(self, epmware_url, token):
        self.epmware = UserProvisioning(epmware_url, token)
    
    def sync_from_active_directory(self, ad_users):
        """Sync users from Active Directory"""
        results = []
        
        for ad_user in ad_users:
            # Map AD attributes to EPMware format
            epmware_user = {
                "userName": ad_user['sAMAccountName'],
                "firstName": ad_user['givenName'],
                "lastName": ad_user['sn'],
                "email": ad_user['mail'],
                "description": f"AD User - {ad_user.get('department', '')}"
            }
            
            try:
                # Check if user exists
                existing = self.check_user_exists(epmware_user['userName'])
                
                if not existing:
                    # Create new user
                    result = self.epmware.create_user(epmware_user)
                    results.append(('created', result))
                else:
                    # Update existing user
                    result = self.epmware.update_user(epmware_user)
                    results.append(('updated', result))
                    
            except Exception as e:
                results.append(('error', str(e)))
        
        return results
```

## Related Operations

- [Update User](update.md) - Modify existing user details
- [Enable/Disable User](enable-disable.md) - Manage user status
- [Assign Groups](../groups/assign-groups.md) - Add users to security groups
- [Get Users](get-users.md) - List all users

## Next Steps

After creating a user:
1. Assign the user to appropriate security groups
2. Configure application access permissions
3. Send welcome email with login instructions
4. Monitor user activity through audit logs