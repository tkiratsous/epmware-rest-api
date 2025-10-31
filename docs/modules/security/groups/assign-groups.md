# Assign Groups

The Assign Groups API allows you to add SSO users to one or more security groups in EPMware, granting them the associated permissions and access rights.

## Overview

This endpoint enables bulk assignment of users to security groups. Groups define access permissions and roles within EPMware, making this API essential for user provisioning and access management.

!!! info "Group Assignment"
    - Users can belong to multiple groups simultaneously
    - Group permissions are cumulative - users get all permissions from all assigned groups
    - Changes take effect immediately upon successful assignment

## Endpoint Details

### URL Structure

```
POST /service/api/security/users/sso/assign_groups
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/security/users/sso/assign_groups
```

### Method

`POST`

### Content Type

`application/json`

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userName` | String | Yes | Username to assign to groups |
| `groupNames` | Array | Yes | List of group names to assign |

## Request Examples

### Using curl

```bash
curl POST 'https://demo.epmwarecloud.com/service/api/security/users/sso/assign_groups' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "SSO_USER1",
    "groupNames": ["Approver", "Reviewer", "Admin"]
  }'
```

### Using Python

```python
import requests
from typing import List, Dict

def assign_user_to_groups(base_url: str, token: str, username: str, 
                         groups: List[str]) -> Dict:
    """
    Assign user to security groups
    
    Args:
        base_url: EPMware base URL
        token: Authentication token
        username: Username to assign to groups
        groups: List of group names
    
    Returns:
        dict: Response message
    """
    url = f"{base_url}/service/api/security/users/sso/assign_groups"
    
    headers = {
        'Authorization': f'Token {token}',
        'Content-Type': 'application/json'
    }
    
    data = {
        'userName': username,
        'groupNames': groups
    }
    
    response = requests.post(url, headers=headers, json=data)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to assign groups: {response.status_code} - {response.text}")

# Usage
result = assign_user_to_groups(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    username="SSO_USER1",
    groups=["Approver", "Reviewer", "Admin"]
)

print(result['value'])  # "User successfully assigned to the specified groups"
```

### Using PowerShell

```powershell
function Add-EPMwareUserToGroups {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$Username,
        
        [Parameter(Mandatory=$true)]
        [string[]]$GroupNames
    )
    
    $url = "$BaseUrl/service/api/security/users/sso/assign_groups"
    
    $headers = @{
        "Authorization" = "Token $Token"
        "Content-Type" = "application/json"
    }
    
    $body = @{
        userName = $Username
        groupNames = $GroupNames
    } | ConvertTo-Json
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Post `
            -Headers $headers `
            -Body $body
        
        Write-Host "âœ… User assigned to groups successfully!" -ForegroundColor Green
        Write-Host "User: $Username"
        Write-Host "Groups: $($GroupNames -join ', ')"
        Write-Host "Message: $($response.value)"
        
        return $response
    }
    catch {
        Write-Host "âŒ Failed to assign groups: $_" -ForegroundColor Red
        throw
    }
}

# Usage
Add-EPMwareUserToGroups `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -Username "SSO_USER1" `
    -GroupNames @("Approver", "Reviewer", "Admin")
```

## Response Format

### Successful Response

```json
{
  "value": "User successfully assigned to the specified groups"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `value` | String | Success message |

## Response Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `200` | Groups assigned successfully | Operation complete |
| `401` | Unauthorized - Invalid token | Verify authentication |
| `404` | User or group not found | Check username and group names |
| `409` | User already in group | No action needed |

## Bulk Group Assignment

### Assign Multiple Users to Groups

```python
import csv
import time
from typing import List, Dict, Optional
from concurrent.futures import ThreadPoolExecutor, as_completed

class GroupAssignmentManager:
    """Manage group assignments for multiple users"""
    
    def __init__(self, base_url: str, token: str):
        self.base_url = base_url
        self.token = token
        self.headers = {
            'Authorization': f'Token {token}',
            'Content-Type': 'application/json'
        }
    
    def assign_single_user(self, username: str, groups: List[str]) -> Dict:
        """Assign single user to groups"""
        
        url = f"{self.base_url}/service/api/security/users/sso/assign_groups"
        
        data = {
            'userName': username,
            'groupNames': groups
        }
        
        try:
            response = requests.post(url, headers=self.headers, json=data)
            
            if response.status_code == 200:
                return {
                    'username': username,
                    'groups': groups,
                    'success': True,
                    'message': response.json().get('value')
                }
            else:
                return {
                    'username': username,
                    'groups': groups,
                    'success': False,
                    'error': response.text
                }
        except Exception as e:
            return {
                'username': username,
                'groups': groups,
                'success': False,
                'error': str(e)
            }
    
    def bulk_assign_same_groups(self, usernames: List[str], groups: List[str]) -> List[Dict]:
        """Assign same groups to multiple users"""
        
        results = []
        total = len(usernames)
        
        print(f"ðŸ“‹ Assigning {len(groups)} groups to {total} users...")
        
        for idx, username in enumerate(usernames, 1):
            print(f"[{idx}/{total}] Processing: {username}")
            
            result = self.assign_single_user(username, groups)
            results.append(result)
            
            if result['success']:
                print(f"  âœ… Success: {username}")
            else:
                print(f"  âŒ Failed: {username} - {result.get('error')}")
            
            # Rate limiting
            time.sleep(0.5)
        
        return results
    
    def bulk_assign_different_groups(self, assignments: List[Dict]) -> List[Dict]:
        """
        Assign different groups to different users
        
        Args:
            assignments: List of dicts with 'username' and 'groups' keys
        """
        
        results = []
        total = len(assignments)
        
        print(f"ðŸ“‹ Processing {total} group assignments...")
        
        for idx, assignment in enumerate(assignments, 1):
            username = assignment['username']
            groups = assignment['groups']
            
            print(f"[{idx}/{total}] {username} â†’ {', '.join(groups)}")
            
            result = self.assign_single_user(username, groups)
            results.append(result)
            
            time.sleep(0.5)
        
        self.print_summary(results)
        return results
    
    def assign_from_csv(self, csv_file: str) -> List[Dict]:
        """
        Assign groups from CSV file
        
        CSV format:
        username,groups
        user1,"Group1;Group2;Group3"
        user2,"Group1;Group4"
        """
        
        assignments = []
        
        with open(csv_file, 'r') as file:
            reader = csv.DictReader(file)
            
            for row in reader:
                username = row['username']
                groups = [g.strip() for g in row['groups'].split(';')]
                
                assignments.append({
                    'username': username,
                    'groups': groups
                })
        
        return self.bulk_assign_different_groups(assignments)
    
    def parallel_assign(self, assignments: List[Dict], max_workers: int = 5) -> List[Dict]:
        """Process assignments in parallel"""
        
        results = []
        
        with ThreadPoolExecutor(max_workers=max_workers) as executor:
            future_to_assignment = {
                executor.submit(
                    self.assign_single_user,
                    assignment['username'],
                    assignment['groups']
                ): assignment
                for assignment in assignments
            }
            
            for future in as_completed(future_to_assignment):
                assignment = future_to_assignment[future]
                
                try:
                    result = future.result()
                    results.append(result)
                    
                    status = "âœ…" if result['success'] else "âŒ"
                    print(f"{status} {assignment['username']}")
                    
                except Exception as e:
                    results.append({
                        'username': assignment['username'],
                        'groups': assignment['groups'],
                        'success': False,
                        'error': str(e)
                    })
        
        return results
    
    def print_summary(self, results: List[Dict]):
        """Print assignment summary"""
        
        total = len(results)
        success_count = sum(1 for r in results if r['success'])
        failed_count = total - success_count
        
        print("\n" + "="*50)
        print("ðŸ“Š Group Assignment Summary:")
        print(f"  Total: {total}")
        print(f"  âœ… Successful: {success_count}")
        print(f"  âŒ Failed: {failed_count}")
        
        if failed_count > 0:
            print("\nFailed assignments:")
            for result in results:
                if not result['success']:
                    print(f"  - {result['username']}: {result.get('error')}")
    
    def save_results(self, results: List[Dict], filename: str = 'assignment_results.csv'):
        """Save results to CSV"""
        
        with open(filename, 'w', newline='') as file:
            fieldnames = ['username', 'groups', 'success', 'message', 'error']
            writer = csv.DictWriter(file, fieldnames=fieldnames)
            writer.writeheader()
            
            for result in results:
                row = {
                    'username': result['username'],
                    'groups': ';'.join(result['groups']),
                    'success': result['success'],
                    'message': result.get('message', ''),
                    'error': result.get('error', '')
                }
                writer.writerow(row)
        
        print(f"\nðŸ“„ Results saved to: {filename}")

# Usage
manager = GroupAssignmentManager(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Assign same groups to multiple users
results = manager.bulk_assign_same_groups(
    usernames=["USER1", "USER2", "USER3"],
    groups=["Developers", "Reviewers"]
)

# Assign different groups to different users
assignments = [
    {'username': 'USER1', 'groups': ['Admin', 'Developers']},
    {'username': 'USER2', 'groups': ['Reviewers', 'Approvers']},
    {'username': 'USER3', 'groups': ['Viewers']}
]
results = manager.bulk_assign_different_groups(assignments)

# Assign from CSV file
results = manager.assign_from_csv('group_assignments.csv')

# Save results
manager.save_results(results)
```

## Role-Based Assignment Patterns

### Department-Based Assignment

```python
class RoleBasedAssignment:
    """Assign groups based on user roles and departments"""
    
    def __init__(self, base_url: str, token: str):
        self.manager = GroupAssignmentManager(base_url, token)
        
        # Define role-to-groups mapping
        self.role_mappings = {
            'developer': ['Developers', 'Code_Reviewers', 'Git_Users'],
            'manager': ['Managers', 'Approvers', 'Report_Viewers'],
            'analyst': ['Analysts', 'Report_Viewers', 'Data_Access'],
            'admin': ['Admins', 'All_Access'],
            'contractor': ['Contractors', 'Limited_Access'],
            'finance': ['Finance_Team', 'Approvers', 'Report_Viewers'],
            'hr': ['HR_Team', 'User_Management', 'Report_Viewers']
        }
        
        # Define department-to-groups mapping
        self.department_mappings = {
            'IT': ['IT_Department', 'Technical_Users'],
            'Finance': ['Finance_Department', 'Financial_Access'],
            'HR': ['HR_Department', 'Employee_Data_Access'],
            'Sales': ['Sales_Department', 'CRM_Access'],
            'Marketing': ['Marketing_Department', 'Campaign_Access']
        }
    
    def assign_by_role(self, username: str, role: str) -> Dict:
        """Assign groups based on user role"""
        
        groups = self.role_mappings.get(role.lower(), [])
        
        if not groups:
            return {
                'username': username,
                'role': role,
                'success': False,
                'error': f"Unknown role: {role}"
            }
        
        result = self.manager.assign_single_user(username, groups)
        result['role'] = role
        
        return result
    
    def assign_by_department(self, username: str, department: str) -> Dict:
        """Assign groups based on department"""
        
        groups = self.department_mappings.get(department, [])
        
        if not groups:
            return {
                'username': username,
                'department': department,
                'success': False,
                'error': f"Unknown department: {department}"
            }
        
        result = self.manager.assign_single_user(username, groups)
        result['department'] = department
        
        return result
    
    def assign_by_role_and_department(self, username: str, role: str, 
                                     department: str) -> Dict:
        """Assign groups based on both role and department"""
        
        role_groups = self.role_mappings.get(role.lower(), [])
        dept_groups = self.department_mappings.get(department, [])
        
        # Combine groups (remove duplicates)
        all_groups = list(set(role_groups + dept_groups))
        
        if not all_groups:
            return {
                'username': username,
                'success': False,
                'error': f"No groups found for role '{role}' and department '{department}'"
            }
        
        result = self.manager.assign_single_user(username, all_groups)
        result['role'] = role
        result['department'] = department
        
        print(f"Assigned {username} ({role}, {department}) to {len(all_groups)} groups")
        
        return result
    
    def bulk_assign_by_user_data(self, user_data: List[Dict]) -> List[Dict]:
        """
        Bulk assign based on user data
        
        Args:
            user_data: List of dicts with 'username', 'role', and 'department'
        """
        
        results = []
        
        for user in user_data:
            username = user['username']
            role = user.get('role', '')
            department = user.get('department', '')
            
            if role and department:
                result = self.assign_by_role_and_department(username, role, department)
            elif role:
                result = self.assign_by_role(username, role)
            elif department:
                result = self.assign_by_department(username, department)
            else:
                result = {
                    'username': username,
                    'success': False,
                    'error': 'No role or department specified'
                }
            
            results.append(result)
        
        return results
    
    def get_recommended_groups(self, role: str, department: str) -> List[str]:
        """Get recommended groups for a role and department"""
        
        role_groups = self.role_mappings.get(role.lower(), [])
        dept_groups = self.department_mappings.get(department, [])
        
        return list(set(role_groups + dept_groups))

# Usage
role_manager = RoleBasedAssignment(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Assign by role
result = role_manager.assign_by_role("JSMITH", "developer")

# Assign by department
result = role_manager.assign_by_department("JDOE", "Finance")

# Assign by both role and department
result = role_manager.assign_by_role_and_department(
    "AUSER", "analyst", "Marketing"
)

# Bulk assignment with user data
user_data = [
    {'username': 'USER1', 'role': 'developer', 'department': 'IT'},
    {'username': 'USER2', 'role': 'manager', 'department': 'Finance'},
    {'username': 'USER3', 'role': 'analyst', 'department': 'Marketing'}
]

results = role_manager.bulk_assign_by_user_data(user_data)
```

## Permission Inheritance

### Hierarchical Group Assignment

```python
class HierarchicalGroups:
    """Manage hierarchical group assignments"""
    
    def __init__(self, base_url: str, token: str):
        self.manager = GroupAssignmentManager(base_url, token)
        
        # Define group hierarchy
        self.group_hierarchy = {
            'Super_Admin': ['Admin', 'Power_User', 'User'],
            'Admin': ['Power_User', 'User'],
            'Power_User': ['User'],
            'Department_Head': ['Team_Lead', 'Team_Member'],
            'Team_Lead': ['Team_Member']
        }
    
    def get_all_groups_for_level(self, level: str) -> List[str]:
        """Get all groups for a hierarchy level including inherited"""
        
        groups = [level]
        
        if level in self.group_hierarchy:
            for child_group in self.group_hierarchy[level]:
                groups.extend(self.get_all_groups_for_level(child_group))
        
        # Remove duplicates while preserving order
        seen = set()
        unique_groups = []
        for group in groups:
            if group not in seen:
                seen.add(group)
                unique_groups.append(group)
        
        return unique_groups
    
    def assign_with_inheritance(self, username: str, level: str) -> Dict:
        """Assign user to group level with all inherited groups"""
        
        all_groups = self.get_all_groups_for_level(level)
        
        print(f"Assigning {username} to level '{level}' with inheritance:")
        print(f"  Groups: {', '.join(all_groups)}")
        
        result = self.manager.assign_single_user(username, all_groups)
        result['level'] = level
        result['inherited_groups'] = all_groups[1:] if len(all_groups) > 1 else []
        
        return result
    
    def promote_user(self, username: str, from_level: str, to_level: str) -> Dict:
        """Promote user to higher level"""
        
        from_groups = self.get_all_groups_for_level(from_level)
        to_groups = self.get_all_groups_for_level(to_level)
        
        # Find new groups to add
        new_groups = list(set(to_groups) - set(from_groups))
        
        if not new_groups:
            return {
                'username': username,
                'success': False,
                'message': f"User already has all groups for level '{to_level}'"
            }
        
        result = self.manager.assign_single_user(username, new_groups)
        result['promoted_from'] = from_level
        result['promoted_to'] = to_level
        result['new_groups'] = new_groups
        
        print(f"Promoted {username} from '{from_level}' to '{to_level}'")
        print(f"  New groups added: {', '.join(new_groups)}")
        
        return result

# Usage
hierarchy = HierarchicalGroups(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Assign with inheritance
result = hierarchy.assign_with_inheritance("JSMITH", "Admin")
# This will assign: ['Admin', 'Power_User', 'User']

# Promote user
result = hierarchy.promote_user("JDOE", "Team_Member", "Team_Lead")
```

## Conditional Assignment

### Time-Based Group Assignment

```python
from datetime import datetime, timedelta
import schedule
import time

class TimedGroupAssignment:
    """Manage time-based group assignments"""
    
    def __init__(self, base_url: str, token: str):
        self.manager = GroupAssignmentManager(base_url, token)
        self.scheduled_assignments = []
    
    def assign_temporary_groups(self, username: str, groups: List[str], 
                              duration_hours: int):
        """Assign groups temporarily"""
        
        # Assign groups immediately
        result = self.manager.assign_single_user(username, groups)
        
        if result['success']:
            # Schedule removal
            remove_time = datetime.now() + timedelta(hours=duration_hours)
            
            self.scheduled_assignments.append({
                'username': username,
                'groups': groups,
                'action': 'remove',
                'scheduled_time': remove_time
            })
            
            print(f"Temporarily assigned {username} to groups for {duration_hours} hours")
            print(f"Groups will be removed at: {remove_time}")
        
        return result
    
    def assign_project_groups(self, project_members: List[Dict], project_name: str):
        """Assign project-specific groups to team members"""
        
        project_groups = {
            'lead': [f'{project_name}_Lead', f'{project_name}_Member'],
            'developer': [f'{project_name}_Developer', f'{project_name}_Member'],
            'tester': [f'{project_name}_Tester', f'{project_name}_Member'],
            'viewer': [f'{project_name}_Viewer']
        }
        
        results = []
        
        for member in project_members:
            username = member['username']
            role = member['role']
            
            groups = project_groups.get(role, [f'{project_name}_Member'])
            
            result = self.manager.assign_single_user(username, groups)
            result['project'] = project_name
            result['project_role'] = role
            
            results.append(result)
        
        return results
    
    def assign_based_on_conditions(self, username: str, user_data: Dict) -> Dict:
        """Assign groups based on user conditions"""
        
        groups_to_assign = []
        
        # Check various conditions
        if user_data.get('is_contractor'):
            groups_to_assign.append('Contractors')
        
        if user_data.get('has_budget_authority'):
            groups_to_assign.append('Budget_Approvers')
        
        if user_data.get('security_clearance') == 'high':
            groups_to_assign.append('Classified_Access')
        
        if user_data.get('training_completed'):
            groups_to_assign.append('Certified_Users')
        
        if user_data.get('location') == 'remote':
            groups_to_assign.append('Remote_Workers')
        
        if not groups_to_assign:
            groups_to_assign = ['Basic_Users']
        
        result = self.manager.assign_single_user(username, groups_to_assign)
        result['conditions'] = user_data
        
        return result

# Usage
timed_manager = TimedGroupAssignment(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Assign temporary groups
timed_manager.assign_temporary_groups(
    username="CONTRACTOR1",
    groups=["Project_Access", "Temp_Permissions"],
    duration_hours=168  # 1 week
)

# Assign project groups
project_members = [
    {'username': 'LEAD1', 'role': 'lead'},
    {'username': 'DEV1', 'role': 'developer'},
    {'username': 'DEV2', 'role': 'developer'},
    {'username': 'TEST1', 'role': 'tester'}
]

results = timed_manager.assign_project_groups(project_members, "PROJECT_ALPHA")
```

## Validation and Audit

### Assignment Validation

```python
class GroupAssignmentValidator:
    """Validate group assignments"""
    
    def __init__(self, base_url: str, token: str):
        self.base_url = base_url
        self.token = token
    
    def validate_groups_exist(self, groups: List[str]) -> Dict:
        """Check if groups exist before assignment"""
        
        # Get all available groups
        all_groups = self.get_all_groups()
        available_group_names = [g['name'] for g in all_groups]
        
        valid_groups = []
        invalid_groups = []
        
        for group in groups:
            if group in available_group_names:
                valid_groups.append(group)
            else:
                invalid_groups.append(group)
        
        return {
            'valid': valid_groups,
            'invalid': invalid_groups,
            'all_valid': len(invalid_groups) == 0
        }
    
    def validate_assignment_rules(self, username: str, groups: List[str]) -> Dict:
        """Validate assignment against business rules"""
        
        violations = []
        
        # Rule 1: Contractors cannot be in Admin groups
        if 'Contractors' in groups and 'Admin' in groups:
            violations.append("Contractors cannot have Admin access")
        
        # Rule 2: Certain groups are mutually exclusive
        exclusive_pairs = [
            ('Approvers', 'Requesters'),
            ('Auditors', 'Finance_Team')
        ]
        
        for group1, group2 in exclusive_pairs:
            if group1 in groups and group2 in groups:
                violations.append(f"Groups '{group1}' and '{group2}' are mutually exclusive")
        
        # Rule 3: Minimum groups required
        if len(groups) == 0:
            violations.append("User must be assigned to at least one group")
        
        # Rule 4: Maximum groups limit
        if len(groups) > 10:
            violations.append("User cannot be in more than 10 groups")
        
        return {
            'valid': len(violations) == 0,
            'violations': violations
        }
    
    def audit_assignment(self, username: str, groups: List[str], 
                        assigned_by: str, reason: str = None):
        """Create audit log for assignment"""
        
        import json
        from datetime import datetime
        
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'username': username,
            'groups_assigned': groups,
            'assigned_by': assigned_by,
            'reason': reason,
            'ip_address': self.get_client_ip()
        }
        
        # Log to file
        with open('group_assignment_audit.log', 'a') as file:
            file.write(json.dumps(audit_entry) + '\n')
        
        return audit_entry
    
    def get_client_ip(self):
        """Get client IP address"""
        import socket
        return socket.gethostbyname(socket.gethostname())

# Usage
validator = GroupAssignmentValidator(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

# Validate groups exist
groups_to_assign = ["Admin", "Developers", "InvalidGroup"]
validation = validator.validate_groups_exist(groups_to_assign)

if not validation['all_valid']:
    print(f"âš ï¸ Invalid groups found: {validation['invalid']}")

# Validate business rules
rule_validation = validator.validate_assignment_rules("USER1", groups_to_assign)

if not rule_validation['valid']:
    print("âŒ Business rule violations:")
    for violation in rule_validation['violations']:
        print(f"  - {violation}")

# Create audit log
audit = validator.audit_assignment(
    username="USER1",
    groups=["Developers", "Reviewers"],
    assigned_by="admin",
    reason="New team member onboarding"
)
```

## Integration Examples

### Active Directory Integration

```python
def sync_ad_groups_to_epmware(ad_user_groups: Dict, manager: GroupAssignmentManager):
    """Sync Active Directory groups to EPMware"""
    
    # Mapping of AD groups to EPMware groups
    ad_to_epmware_mapping = {
        'Domain Users': ['Basic_Users'],
        'IT Staff': ['IT_Department', 'Technical_Users'],
        'Finance Users': ['Finance_Department', 'Financial_Access'],
        'Managers': ['Managers', 'Approvers'],
        'Executives': ['Executives', 'All_Reports']
    }
    
    results = []
    
    for username, ad_groups in ad_user_groups.items():
        epmware_groups = []
        
        # Map AD groups to EPMware groups
        for ad_group in ad_groups:
            if ad_group in ad_to_epmware_mapping:
                epmware_groups.extend(ad_to_epmware_mapping[ad_group])
        
        # Remove duplicates
        epmware_groups = list(set(epmware_groups))
        
        if epmware_groups:
            result = manager.assign_single_user(username, epmware_groups)
            result['ad_groups'] = ad_groups
            results.append(result)
            
            print(f"Synced {username}: {len(ad_groups)} AD groups â†’ {len(epmware_groups)} EPMware groups")
    
    return results
```

## Best Practices

### 1. Pre-Assignment Validation

```python
def safe_assign_groups(manager: GroupAssignmentManager, username: str, 
                       groups: List[str]) -> Dict:
    """Safely assign groups with validation"""
    
    # Remove duplicates
    groups = list(set(groups))
    
    # Remove empty strings
    groups = [g for g in groups if g.strip()]
    
    # Validate groups exist (implement based on your needs)
    # valid_groups = validate_groups_exist(groups)
    
    # Check user exists (implement based on your needs)
    # if not user_exists(username):
    #     return {'success': False, 'error': 'User not found'}
    
    # Perform assignment
    return manager.assign_single_user(username, groups)
```

### 2. Error Recovery

```python
def assign_with_retry(manager: GroupAssignmentManager, username: str, 
                     groups: List[str], max_retries: int = 3) -> Dict:
    """Assign groups with retry on failure"""
    
    for attempt in range(max_retries):
        try:
            result = manager.assign_single_user(username, groups)
            
            if result['success']:
                return result
                
        except Exception as e:
            if attempt < max_retries - 1:
                print(f"Attempt {attempt + 1} failed, retrying...")
                time.sleep(2 ** attempt)  # Exponential backoff
            else:
                return {
                    'username': username,
                    'groups': groups,
                    'success': False,
                    'error': f"Failed after {max_retries} attempts: {str(e)}"
                }
```

### 3. Rollback Capability

```python
class GroupAssignmentWithRollback:
    """Group assignment with rollback capability"""
    
    def __init__(self, base_url: str, token: str):
        self.manager = GroupAssignmentManager(base_url, token)
        self.backup = {}
    
    def backup_current_groups(self, username: str):
        """Backup current group assignments"""
        
        # Get current groups (implement based on your API)
        current_groups = self.get_user_groups(username)
        self.backup[username] = current_groups
        
        return current_groups
    
    def assign_with_backup(self, username: str, groups: List[str]) -> Dict:
        """Assign groups with backup for rollback"""
        
        # Backup current state
        self.backup_current_groups(username)
        
        # Perform assignment
        result = self.manager.assign_single_user(username, groups)
        
        if not result['success']:
            print(f"Assignment failed for {username}, current groups preserved")
        
        return result
    
    def rollback(self, username: str) -> Dict:
        """Rollback to previous group assignments"""
        
        if username not in self.backup:
            return {
                'success': False,
                'error': f"No backup found for {username}"
            }
        
        original_groups = self.backup[username]
        
        # First remove all current groups
        # Then assign original groups
        result = self.manager.assign_single_user(username, original_groups)
        
        if result['success']:
            print(f"âœ… Rolled back {username} to original groups")
            del self.backup[username]
        
        return result
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `404 Not Found` | User or group doesn't exist | Verify username and group names |
| `401 Unauthorized` | Invalid token | Check authentication |
| `409 Conflict` | User already in group | No action needed |
| `400 Bad Request` | Invalid request format | Check JSON structure |

## Related Operations

- [Unassign Groups](unassign-groups.md) - Remove users from groups
- [Get Groups](get-groups.md) - List all available groups
- [Get Group Users](get-group-users.md) - List users in groups
- [Get User Groups](get-user-groups.md) - List groups for a user

## Next Steps

After assigning groups:
1. Verify the assignment was successful
2. Update audit logs
3. Send notification to user
4. Document the change reason