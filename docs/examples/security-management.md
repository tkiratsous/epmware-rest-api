# Security Management Examples

Examples for managing users and groups via the EPMware API.

## User Management

```python
import requests
import csv

class SecurityManager:
    """Manage EPMware users and groups"""
    
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.headers = {'Authorization': f'Token {token}'}
    
    def create_user(self, user_data):
        """Create new SSO user"""
        url = f"{self.base_url}/service/api/security/users/sso/create"
        response = requests.post(url, json=user_data, headers=self.headers)
        return response.json()
    
    def bulk_create_users(self, csv_file):
        """Create multiple users from CSV"""
        results = []
        
        with open(csv_file, 'r') as f:
            reader = csv.DictReader(f)
            for row in reader:
                user_data = {
                    'userName': row['username'],
                    'email': row['email'],
                    'firstName': row['first_name'],
                    'lastName': row['last_name'],
                    'groups': row['groups'].split(';')
                }
                
                result = self.create_user(user_data)
                results.append(result)
        
        return results
    
    def assign_groups(self, username, groups):
        """Assign user to groups"""
        url = f"{self.base_url}/service/api/security/users/sso/assign_groups"
        
        payload = {
            'userName': username,
            'groups': groups
        }
        
        response = requests.post(url, json=payload, headers=self.headers)
        return response.json()

# Usage
security = SecurityManager('https://demo.epmwarecloud.com', 'your-token')

# Create single user
new_user = {
    'userName': 'JDOE',
    'email': 'jdoe@company.com',
    'firstName': 'John',
    'lastName': 'Doe'
}
result = security.create_user(new_user)

# Assign to groups
security.assign_groups('JDOE', ['FIN_ANALYSTS', 'REPORT_VIEWERS'])
```

---

[← Back to Examples](../) | [PowerShell Scripts →](../powershell-scripts/)
