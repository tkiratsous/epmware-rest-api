# ERP Integration Examples

Complete examples for integrating EPMware with ERP systems.

## ERP Integration Class

```python
import requests
import pandas as pd
from pathlib import Path

class ERPIntegration:
    """Handle ERP data imports to EPMware"""
    
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.headers = {'Authorization': f'Token {token}'}
    
    def upload_file(self, file_path, profile_name):
        """Upload file for import"""
        url = f"{self.base_url}/service/api/erp/upload_file"
        
        with open(file_path, 'rb') as f:
            files = {'file': f}
            data = {'profileName': profile_name}
            
            response = requests.post(
                url, 
                files=files, 
                data=data,
                headers={'Authorization': self.headers['Authorization']}
            )
        
        return response.json()
    
    def run_import(self, profile_name, parameters=None):
        """Execute import process"""
        url = f"{self.base_url}/service/api/erp/run"
        
        payload = {
            'profileName': profile_name,
            'parameters': parameters or {}
        }
        
        response = requests.post(url, json=payload, headers=self.headers)
        return response.json()
    
    def process_csv_import(self, csv_file, profile_name):
        """Complete CSV import workflow"""
        # Upload file
        upload_result = self.upload_file(csv_file, profile_name)
        
        if upload_result['status'] == 'S':
            # Run import
            import_result = self.run_import(profile_name)
            return import_result['taskId']
        else:
            raise Exception(f"Upload failed: {upload_result['message']}")

# Usage
erp = ERPIntegration('https://demo.epmwarecloud.com', 'your-token')
task_id = erp.process_csv_import('data/trial_balance.csv', 'TB_IMPORT')
print(f"Import started with task ID: {task_id}")
```

---

[← Back to Examples](../) | [Deployment Automation →](../deployment-automation/)
