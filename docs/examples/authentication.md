# Authentication Examples

Complete examples for implementing EPMware REST API authentication.

## Token Management Class

```python
import requests
import os
from datetime import datetime, timedelta

class EPMwareAuth:
    """Manage EPMware API authentication"""
    
    def __init__(self, base_url, token=None):
        self.base_url = base_url
        self.token = token or os.environ.get('EPMWARE_TOKEN')
        self.headers = self._create_headers()
    
    def _create_headers(self):
        """Create authentication headers"""
        return {
            'Authorization': f'Token {self.token}',
            'Content-Type': 'application/json'
        }
    
    def test_connection(self):
        """Test API connection"""
        try:
            response = requests.get(
                f"{self.base_url}/service/api/security/users",
                headers=self.headers
            )
            return response.status_code == 200
        except:
            return False
    
    def refresh_token(self):
        """Placeholder for token refresh logic"""
        # Implement token refresh if supported
        pass

# Usage
auth = EPMwareAuth('https://demo.epmwarecloud.com')
if auth.test_connection():
    print("Authentication successful")
```

## Secure Token Storage

```python
import keyring
import getpass

class SecureTokenManager:
    """Securely store and retrieve API tokens"""
    
    SERVICE_NAME = "EPMware_API"
    
    @staticmethod
    def store_token(username, token):
        """Store token securely"""
        keyring.set_password(
            SecureTokenManager.SERVICE_NAME,
            username,
            token
        )
    
    @staticmethod
    def get_token(username):
        """Retrieve stored token"""
        return keyring.get_password(
            SecureTokenManager.SERVICE_NAME,
            username
        )
    
    @staticmethod
    def delete_token(username):
        """Remove stored token"""
        keyring.delete_password(
            SecureTokenManager.SERVICE_NAME,
            username
        )

# Store token
token = getpass.getpass("Enter API Token: ")
SecureTokenManager.store_token("api_user", token)

# Retrieve token
stored_token = SecureTokenManager.get_token("api_user")
```

---

[← Back to Examples](../) | [Task Management →](../task-management/)
