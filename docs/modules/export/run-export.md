# Run Export

Execute export profiles to extract data from EPMware.

## Endpoint Details

```http
POST /service/api/export/run
```

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | String | Yes | Export profile or book name |
| `format` | String | No | Output format (XLSX, CSV, TXT) |
| `delimiter` | String | No | Delimiter for text files |
| `compress` | Boolean | No | Compress output file |
| `email` | String | No | Email for notification |

## Examples

### Basic Export

```python
import requests

def run_export(profile_name, token):
    url = "https://demo.epmwarecloud.com/service/api/export/run"
    headers = {"Authorization": f"Token {token}"}
    payload = {"name": profile_name}
    
    response = requests.post(url, json=payload, headers=headers)
    return response.json()
```

### Export with Parameters

```json
{
  "name": "Monthly_Report",
  "format": "XLSX",
  "parameters": {
    "period": "Jan-2025",
    "entity": "US001",
    "scenario": "Actual"
  },
  "compress": true,
  "email": "analyst@company.com"
}
```

---

[← Back to Export Module](../) | [Download File](../download-file/)
