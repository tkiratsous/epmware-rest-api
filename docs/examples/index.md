# Examples

Practical code examples and implementation patterns for the EPMware REST API.

## Overview

This section provides complete, working examples in multiple programming languages to help you quickly integrate the EPMware REST API into your applications.

## Available Examples

<div class="examples-grid">

### 🔐 [Authentication Examples](authentication/)
Token generation and management patterns

### 📋 [Task Management](task-management/)
Monitor and manage asynchronous tasks

### 🏢 [ERP Integration](erp-integration/)
Upload files and run imports

### 🚀 [Deployment Automation](deployment-automation/)
Automate deployment workflows

### 📤 [Export Automation](export-automation/)
Schedule and manage data exports

### 🔒 [Security Management](security-management/)
User and group administration

### 💻 [PowerShell Scripts](powershell-scripts/)
Windows automation scripts

### 🐍 [Python Examples](python-examples/)
Complete Python implementations

### 📮 [Postman Collections](postman-collections/)
Ready-to-use API collections

</div>

## Quick Start Examples

### Basic API Call

```python
import requests

# Configuration
BASE_URL = "https://demo.epmwarecloud.com"
TOKEN = "your-token-here"

# Make API call
headers = {"Authorization": f"Token {TOKEN}"}
response = requests.get(
    f"{BASE_URL}/service/api/task/get_status/12345",
    headers=headers
)

print(response.json())
```

### Error Handling

```python
try:
    response = requests.get(url, headers=headers)
    response.raise_for_status()
    data = response.json()
except requests.exceptions.HTTPError as e:
    print(f"HTTP Error: {e}")
except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
```

---

[← Back to Home](../) | [Authentication Examples →](authentication/)
