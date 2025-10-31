# Export Module

Automate data extraction and file generation from EPMware systems.

## Overview

The Export module provides APIs for running export profiles, generating data extracts, and downloading exported files. It supports various formats including Excel, CSV, and custom delimited files for integration with downstream systems.

## Key Features

- **Multiple Export Formats**: Support for Excel, CSV, TXT, XML, and JSON
- **Scheduled Exports**: Automate recurring data extractions
- **Large File Support**: Stream large datasets efficiently
- **Compression**: Automatic file compression for bandwidth optimization
- **Book Exports**: Export multiple reports in a single operation

## Available Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/service/api/export/run` | POST | Execute export profile or book |
| `/service/api/export/download_file/{task_id}` | GET | Download exported file |
| `/service/api/export/get_execution_details` | GET | Get export execution history |

## Export Types

### Data Exports
- Financial statements
- Trial balances
- Budget vs Actual reports
- Variance analysis
- Consolidation results

### Metadata Exports
- Dimension hierarchies
- Member properties
- Security assignments
- Business rules
- Form definitions

### System Exports
- Audit logs
- User activity reports
- Performance metrics
- Error logs

## Common Use Cases

### Scheduled Financial Reporting

```python
def schedule_monthly_reports():
    """Generate monthly financial reports"""
    
    exports = [
        {'name': 'P&L_Statement', 'format': 'XLSX'},
        {'name': 'Balance_Sheet', 'format': 'XLSX'},
        {'name': 'Cash_Flow', 'format': 'XLSX'}
    ]
    
    for export in exports:
        response = run_export(export['name'], {
            'period': current_period(),
            'format': export['format'],
            'email': 'cfo@company.com'
        })
        
        track_export(response['taskId'])
```

---

[← Back to Modules](../) | [Run Export](run-export/)
