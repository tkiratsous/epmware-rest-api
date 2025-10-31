# Export Automation Examples

Automate data exports from EPMware with these examples.

## Export Scheduler

```python
import requests
import schedule
import time
from datetime import datetime

class ExportAutomation:
    """Automate EPMware exports"""
    
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.headers = {'Authorization': f'Token {token}'}
    
    def run_export(self, export_name, parameters=None):
        """Execute export"""
        url = f"{self.base_url}/service/api/export/run"
        
        payload = {
            'name': export_name,
            'parameters': parameters or {},
            'format': 'XLSX'
        }
        
        response = requests.post(url, json=payload, headers=self.headers)
        return response.json()
    
    def download_file(self, task_id, output_path):
        """Download exported file"""
        url = f"{self.base_url}/service/api/export/download_file/{task_id}"
        
        response = requests.get(url, headers=self.headers, stream=True)
        
        with open(output_path, 'wb') as f:
            for chunk in response.iter_content(chunk_size=8192):
                f.write(chunk)
        
        return output_path
    
    def schedule_daily_exports(self):
        """Schedule daily export jobs"""
        schedule.every().day.at("06:00").do(
            self.run_export, "Daily_Report"
        )
        schedule.every().monday.at("08:00").do(
            self.run_export, "Weekly_Summary"
        )
        
        while True:
            schedule.run_pending()
            time.sleep(60)

# Usage
export = ExportAutomation('https://demo.epmwarecloud.com', 'your-token')
result = export.run_export('Monthly_Report', {'period': 'Jan-2025'})
print(f"Export started: {result['taskId']}")
```

---

[← Back to Examples](../) | [Security Management →](../security-management/)
