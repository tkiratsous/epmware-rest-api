# Appendix B - Sample Scripts

Ready-to-use scripts for common EPMware API scenarios.

## Complete Automation Script

```python
#!/usr/bin/env python3
"""
EPMware Daily Automation Script
Runs daily imports, exports, and reports
"""

import os
import sys
import logging
import schedule
import time
from datetime import datetime
from epmware_api import EPMwareClient

# Configuration
CONFIG = {
    'base_url': os.environ.get('EPMWARE_URL'),
    'token': os.environ.get('EPMWARE_TOKEN'),
    'email_recipients': ['admin@company.com'],
    'log_file': '/var/log/epmware_automation.log'
}

# Setup logging
logging.basicConfig(
    filename=CONFIG['log_file'],
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

def daily_import():
    """Run daily data import"""
    client = EPMwareClient(CONFIG['base_url'], CONFIG['token'])
    
    try:
        # Upload file
        upload_result = client.upload_file(
            '/data/daily_import.csv',
            'DAILY_IMPORT'
        )
        
        # Run import
        import_result = client.run_import('DAILY_IMPORT')
        task_id = import_result.data['taskId']
        
        # Wait for completion
        result = client.wait_for_task(task_id)
        
        if result.data['status'] == 'S':
            logging.info(f"Daily import completed: {task_id}")
        else:
            logging.error(f"Daily import failed: {result.data['message']}")
            
    except Exception as e:
        logging.error(f"Import error: {e}")

def generate_reports():
    """Generate and distribute reports"""
    client = EPMwareClient(CONFIG['base_url'], CONFIG['token'])
    
    reports = [
        'Daily_Summary',
        'Exception_Report',
        'Performance_Metrics'
    ]
    
    for report in reports:
        try:
            result = client.run_export(report)
            task_id = result.data['taskId']
            
            # Wait and download
            client.wait_for_task(task_id)
            client.download_export(
                task_id,
                f"/reports/{report}_{datetime.now():%Y%m%d}.xlsx"
            )
            
            logging.info(f"Report generated: {report}")
            
        except Exception as e:
            logging.error(f"Report generation failed for {report}: {e}")

def main():
    """Main execution"""
    # Schedule tasks
    schedule.every().day.at("06:00").do(daily_import)
    schedule.every().day.at("07:00").do(generate_reports)
    
    logging.info("EPMware automation started")
    
    while True:
        schedule.run_pending()
        time.sleep(60)

if __name__ == "__main__":
    main()
```

## Monitoring Dashboard

```javascript
// dashboard.js - Real-time monitoring dashboard
const API_URL = 'https://demo.epmwarecloud.com/service/api';
const TOKEN = process.env.EPMWARE_TOKEN;

class EPMwareDashboard {
    constructor() {
        this.activeTasks = new Map();
        this.updateInterval = 5000; // 5 seconds
    }
    
    async updateTaskStatuses() {
        for (const [taskId, taskInfo] of this.activeTasks) {
            const status = await this.getTaskStatus(taskId);
            this.updateDisplay(taskId, status);
            
            if (status.status in ['S', 'E', 'W', 'C']) {
                this.activeTasks.delete(taskId);
            }
        }
    }
    
    async getTaskStatus(taskId) {
        const response = await fetch(
            `${API_URL}/task/get_status/${taskId}`,
            {
                headers: {
                    'Authorization': `Token ${TOKEN}`
                }
            }
        );
        return response.json();
    }
    
    updateDisplay(taskId, status) {
        const element = document.getElementById(`task-${taskId}`);
        if (element) {
            element.querySelector('.status').textContent = status.status;
            element.querySelector('.progress').style.width = `${status.percentCompleted}%`;
            element.querySelector('.message').textContent = status.message;
        }
    }
    
    start() {
        setInterval(() => this.updateTaskStatuses(), this.updateInterval);
    }
}

// Initialize dashboard
const dashboard = new EPMwareDashboard();
dashboard.start();
```

---

[← Back to Appendices](../) | [Troubleshooting →](../troubleshooting/)
