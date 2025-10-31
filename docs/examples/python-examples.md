# Python Examples

Complete Python implementation for EPMware REST API integration.

## EPMware API Client Library

```python
# epmware_api.py
"""
EPMware REST API Python Client
"""

import requests
import json
import logging
from typing import Dict, List, Optional, Any
from dataclasses import dataclass
from enum import Enum
import time

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)


class TaskStatus(Enum):
    """Task status enumeration"""
    PENDING = 'P'
    RUNNING = 'R'
    SUCCESS = 'S'
    ERROR = 'E'
    WARNING = 'W'
    CANCELLED = 'C'


@dataclass
class APIResponse:
    """Standard API response"""
    success: bool
    data: Optional[Dict]
    message: str
    status_code: int


class EPMwareClient:
    """EPMware REST API Client"""
    
    def __init__(self, base_url: str, token: str, timeout: int = 30):
        self.base_url = base_url.rstrip('/')
        self.token = token
        self.timeout = timeout
        self.session = self._create_session()
    
    def _create_session(self) -> requests.Session:
        """Create HTTP session with authentication"""
        session = requests.Session()
        session.headers.update({
            'Authorization': f'Token {self.token}',
            'Content-Type': 'application/json'
        })
        return session
    
    def _make_request(self, method: str, endpoint: str, 
                     **kwargs) -> APIResponse:
        """Make HTTP request to API"""
        url = f"{self.base_url}/service/api/{endpoint}"
        
        try:
            response = self.session.request(
                method, url, timeout=self.timeout, **kwargs
            )
            response.raise_for_status()
            
            return APIResponse(
                success=True,
                data=response.json() if response.content else None,
                message="Success",
                status_code=response.status_code
            )
            
        except requests.exceptions.HTTPError as e:
            logger.error(f"HTTP error: {e}")
            return APIResponse(
                success=False,
                data=None,
                message=str(e),
                status_code=response.status_code
            )
        except requests.exceptions.RequestException as e:
            logger.error(f"Request failed: {e}")
            return APIResponse(
                success=False,
                data=None,
                message=str(e),
                status_code=0
            )
    
    # Task Management
    def get_task_status(self, task_id: str) -> APIResponse:
        """Get task status"""
        return self._make_request('GET', f'task/get_status/{task_id}')
    
    def wait_for_task(self, task_id: str, timeout: int = 3600,
                     interval: int = 5) -> APIResponse:
        """Wait for task completion"""
        start_time = time.time()
        
        while time.time() - start_time < timeout:
            response = self.get_task_status(task_id)
            
            if response.success and response.data:
                status = response.data.get('status')
                
                if status in ['S', 'E', 'W', 'C']:
                    return response
                
                logger.info(f"Task {task_id}: {response.data.get('percentCompleted', 0)}% complete")
            
            time.sleep(interval)
        
        return APIResponse(
            success=False,
            data=None,
            message=f"Task {task_id} timed out",
            status_code=0
        )
    
    # ERP Module
    def upload_file(self, file_path: str, profile_name: str) -> APIResponse:
        """Upload file for import"""
        with open(file_path, 'rb') as f:
            files = {'file': f}
            data = {'profileName': profile_name}
            
            # Temporarily remove Content-Type for multipart
            del self.session.headers['Content-Type']
            response = self._make_request('POST', 'erp/upload_file',
                                        files=files, data=data)
            self.session.headers['Content-Type'] = 'application/json'
            
            return response
    
    def run_import(self, profile_name: str, 
                  parameters: Optional[Dict] = None) -> APIResponse:
        """Execute ERP import"""
        payload = {
            'profileName': profile_name,
            'parameters': parameters or {}
        }
        return self._make_request('POST', 'erp/run', json=payload)
    
    # Security Module
    def create_user(self, user_data: Dict) -> APIResponse:
        """Create SSO user"""
        return self._make_request('POST', 'security/users/sso/create',
                                json=user_data)
    
    def get_users(self) -> APIResponse:
        """Get all users"""
        return self._make_request('GET', 'security/users')
    
    def assign_groups(self, username: str, groups: List[str]) -> APIResponse:
        """Assign user to groups"""
        payload = {
            'userName': username,
            'groups': groups
        }
        return self._make_request('POST', 'security/users/sso/assign_groups',
                                json=payload)
    
    # Export Module
    def run_export(self, export_name: str,
                  parameters: Optional[Dict] = None) -> APIResponse:
        """Execute export"""
        payload = {
            'name': export_name,
            'parameters': parameters or {}
        }
        return self._make_request('POST', 'export/run', json=payload)
    
    def download_export(self, task_id: str, output_path: str) -> bool:
        """Download exported file"""
        url = f"{self.base_url}/service/api/export/download_file/{task_id}"
        
        try:
            response = self.session.get(url, stream=True)
            response.raise_for_status()
            
            with open(output_path, 'wb') as f:
                for chunk in response.iter_content(chunk_size=8192):
                    f.write(chunk)
            
            logger.info(f"File downloaded to {output_path}")
            return True
            
        except Exception as e:
            logger.error(f"Download failed: {e}")
            return False


# Example usage
if __name__ == "__main__":
    # Initialize client
    client = EPMwareClient(
        base_url="https://demo.epmwarecloud.com",
        token="your-token-here"
    )
    
    # Run import workflow
    upload_response = client.upload_file("data.csv", "GL_IMPORT")
    
    if upload_response.success:
        import_response = client.run_import("GL_IMPORT", {
            "period": "Jan-2025",
            "validate": True
        })
        
        if import_response.success and import_response.data:
            task_id = import_response.data.get('taskId')
            
            # Wait for completion
            result = client.wait_for_task(task_id)
            
            if result.success and result.data.get('status') == 'S':
                print("Import completed successfully!")
            else:
                print(f"Import failed: {result.message}")
```

---

[← Back to Examples](../) | [Postman Collections](../postman-collections/)
