# Postman Collections

This page provides Postman collection examples for testing and exploring the EPMware REST API. Import these collections into Postman to quickly get started with API testing.

## Getting Started with Postman

### Prerequisites

1. Download and install [Postman](https://www.postman.com/downloads/)
2. Obtain your EPMware API token
3. Know your EPMware instance URL

### Setting Up Environment

Create a Postman environment with these variables:

```json
{
  "name": "EPMware Production",
  "values": [
    {
      "key": "base_url",
      "value": "https://demo.epmwarecloud.com",
      "enabled": true
    },
    {
      "key": "token",
      "value": "your-token-here",
      "enabled": true,
      "type": "secret"
    },
    {
      "key": "current_task_id",
      "value": "",
      "enabled": true
    },
    {
      "key": "current_user",
      "value": "",
      "enabled": true
    }
  ]
}
```

## Complete EPMware API Collection

### Collection Structure

```json
{
  "info": {
    "name": "EPMware REST API",
    "description": "Complete EPMware REST API collection",
    "version": "1.3.0",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "auth": {
    "type": "apikey",
    "apikey": [
      {
        "key": "value",
        "value": "Token {{token}}",
        "type": "string"
      },
      {
        "key": "key",
        "value": "Authorization",
        "type": "string"
      }
    ]
  },
  "item": []
}
```

## Task Module Collection

### Get Task Status

```json
{
  "name": "Task Module",
  "item": [
    {
      "name": "Get Task Status",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{base_url}}/service/api/task/get_status/{{current_task_id}}",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "task", "get_status", "{{current_task_id}}"]
        }
      },
      "response": [],
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status code is 200', function () {",
              "    pm.response.to.have.status(200);",
              "});",
              "",
              "pm.test('Response has required fields', function () {",
              "    const response = pm.response.json();",
              "    pm.expect(response).to.have.property('taskId');",
              "    pm.expect(response).to.have.property('status');",
              "    pm.expect(response).to.have.property('percentCompleted');",
              "});",
              "",
              "// Save status for next requests",
              "const response = pm.response.json();",
              "pm.environment.set('task_status', response.status);",
              "pm.environment.set('task_percent', response.percentCompleted);"
            ]
          }
        }
      ]
    },
    {
      "name": "Get Task Log",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{base_url}}/service/api/task/get_log_file/{{current_task_id}}",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "task", "get_log_file", "{{current_task_id}}"]
        }
      }
    }
  ]
}
```

## ERP Module Collection

### Upload File and Run Import

```json
{
  "name": "ERP Module",
  "item": [
    {
      "name": "Upload ERP File",
      "request": {
        "method": "POST",
        "header": [],
        "body": {
          "mode": "formdata",
          "formdata": [
            {
              "key": "file",
              "type": "file",
              "src": "/path/to/your/file.csv"
            },
            {
              "key": "name",
              "value": "ASOALL_Account",
              "type": "text"
            },
            {
              "key": "delimiter_char",
              "value": ",",
              "type": "text"
            },
            {
              "key": "enclosing_char",
              "value": "\"",
              "type": "text"
            }
          ]
        },
        "url": {
          "raw": "{{base_url}}/service/api/erp/upload_file",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "erp", "upload_file"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('File uploaded successfully', function () {",
              "    pm.response.to.have.status(200);",
              "    const response = pm.response.json();",
              "    pm.expect(response.status).to.equal('S');",
              "});",
              "",
              "// Save task ID",
              "const response = pm.response.json();",
              "pm.environment.set('current_task_id', response.taskId);",
              "console.log('Task ID saved: ' + response.taskId);"
            ]
          }
        }
      ]
    },
    {
      "name": "Run ERP Import",
      "request": {
        "method": "POST",
        "header": [
          {
            "key": "Content-Type",
            "value": "application/json"
          }
        ],
        "body": {
          "mode": "raw",
          "raw": "{\n    \"name\": \"ASOALL_Account\",\n    \"timeout_min\": 60\n}"
        },
        "url": {
          "raw": "{{base_url}}/service/api/erp/run",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "erp", "run"]
        }
      }
    }
  ]
}
```

## Export Module Collection

### Complete Export Workflow

```json
{
  "name": "Export Module",
  "item": [
    {
      "name": "Run Export Profile",
      "request": {
        "method": "POST",
        "header": [
          {
            "key": "Content-Type",
            "value": "application/json"
          }
        ],
        "body": {
          "mode": "raw",
          "raw": "{\n    \"name\": \"PBCS\"\n}"
        },
        "url": {
          "raw": "{{base_url}}/service/api/export/run",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "export", "run"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Export started successfully', function () {",
              "    pm.response.to.have.status(200);",
              "});",
              "",
              "const response = pm.response.json();",
              "pm.environment.set('export_task_id', response.taskId);",
              "",
              "// Set up automatic polling",
              "pm.environment.set('poll_count', 0);",
              "pm.environment.set('max_polls', 60);"
            ]
          }
        }
      ]
    },
    {
      "name": "Check Export Status (with retry)",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{base_url}}/service/api/task/get_status/{{export_task_id}}",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "task", "get_status", "{{export_task_id}}"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "const response = pm.response.json();",
              "const status = response.status;",
              "const pollCount = pm.environment.get('poll_count') || 0;",
              "const maxPolls = pm.environment.get('max_polls') || 60;",
              "",
              "pm.test('Response received', function () {",
              "    pm.response.to.have.status(200);",
              "});",
              "",
              "if (status === 'N' && pollCount < maxPolls) {",
              "    // Still running, schedule retry",
              "    pm.environment.set('poll_count', pollCount + 1);",
              "    console.log(`Task still running (${response.percentCompleted}%). Poll ${pollCount + 1}/${maxPolls}`);",
              "    ",
              "    setTimeout(() => {",
              "        postman.setNextRequest('Check Export Status (with retry)');",
              "    }, 5000); // Wait 5 seconds",
              "} else if (status === 'S') {",
              "    console.log('Export completed successfully!');",
              "    pm.test('Export completed', function () {",
              "        pm.expect(status).to.equal('S');",
              "    });",
              "    postman.setNextRequest('Download Export File');",
              "} else if (status === 'E') {",
              "    pm.test('Export failed', function () {",
              "        pm.expect(status).to.not.equal('E');",
              "    });",
              "} else {",
              "    console.log('Max polling attempts reached');",
              "}"
            ]
          }
        }
      ]
    },
    {
      "name": "Download Export File",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{base_url}}/service/api/export/download_file/{{export_task_id}}",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "export", "download_file", "{{export_task_id}}"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('File downloaded successfully', function () {",
              "    pm.response.to.have.status(200);",
              "});",
              "",
              "// Check file size",
              "const contentLength = pm.response.headers.get('Content-Length');",
              "console.log(`File size: ${contentLength} bytes`);"
            ]
          }
        }
      ]
    },
    {
      "name": "Get Export Execution Details",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{base_url}}/service/api/export/get_execution_details?name=PBCS&latest_only=Y",
          "host": ["{{base_url}}"],
          "path": ["service", "api", "export", "get_execution_details"],
          "query": [
            {
              "key": "name",
              "value": "PBCS"
            },
            {
              "key": "latest_only",
              "value": "Y"
            }
          ]
        }
      }
    }
  ]
}
```

## Security Module Collection

### User Management

```json
{
  "name": "Security Module",
  "item": [
    {
      "name": "User Management",
      "item": [
        {
          "name": "Create SSO User",
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n    \"userName\": \"{{$randomUserName}}\",\n    \"firstName\": \"{{$randomFirstName}}\",\n    \"lastName\": \"{{$randomLastName}}\",\n    \"email\": \"{{$randomEmail}}\",\n    \"description\": \"Created via Postman\"\n}"
            },
            "url": {
              "raw": "{{base_url}}/service/api/security/users/sso/create",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "users", "sso", "create"]
            }
          },
          "event": [
            {
              "listen": "test",
              "script": {
                "exec": [
                  "pm.test('User created successfully', function () {",
                  "    pm.response.to.have.status(200);",
                  "});",
                  "",
                  "const response = pm.response.json();",
                  "pm.environment.set('created_user', response.userName);",
                  "pm.environment.set('user_id', response.id);"
                ]
              }
            },
            {
              "listen": "prerequest",
              "script": {
                "exec": [
                  "// Generate unique username",
                  "const timestamp = new Date().getTime();",
                  "const username = `TEST_USER_${timestamp}`;",
                  "pm.environment.set('test_username', username);"
                ]
              }
            }
          ]
        },
        {
          "name": "Update User",
          "request": {
            "method": "PUT",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n    \"firstName\": \"Updated\",\n    \"lastName\": \"Name\",\n    \"description\": \"Updated via API\"\n}"
            },
            "url": {
              "raw": "{{base_url}}/service/api/security/users/sso/update/{{created_user}}",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "users", "sso", "update", "{{created_user}}"]
            }
          }
        },
        {
          "name": "Enable User",
          "request": {
            "method": "PATCH",
            "header": [],
            "url": {
              "raw": "{{base_url}}/service/api/security/users/sso/enable/{{created_user}}",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "users", "sso", "enable", "{{created_user}}"]
            }
          }
        },
        {
          "name": "Disable User",
          "request": {
            "method": "PATCH",
            "header": [],
            "url": {
              "raw": "{{base_url}}/service/api/security/users/sso/disable/{{created_user}}",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "users", "sso", "disable", "{{created_user}}"]
            }
          }
        },
        {
          "name": "Get All Users",
          "request": {
            "method": "GET",
            "header": [],
            "url": {
              "raw": "{{base_url}}/service/api/security/users?userType=NATIVE",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "users"],
              "query": [
                {
                  "key": "userType",
                  "value": "NATIVE",
                  "description": "Filter by NATIVE or SAML"
                }
              ]
            }
          }
        }
      ]
    },
    {
      "name": "Group Management",
      "item": [
        {
          "name": "Assign Groups",
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n    \"userName\": \"{{created_user}}\",\n    \"groupNames\": [\"Reviewers\", \"Developers\"]\n}"
            },
            "url": {
              "raw": "{{base_url}}/service/api/security/users/sso/assign_groups",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "users", "sso", "assign_groups"]
            }
          }
        },
        {
          "name": "Get User Groups",
          "request": {
            "method": "GET",
            "header": [],
            "url": {
              "raw": "{{base_url}}/service/api/security/users/groups?userName={{created_user}}",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "users", "groups"],
              "query": [
                {
                  "key": "userName",
                  "value": "{{created_user}}"
                }
              ]
            }
          }
        },
        {
          "name": "Get All Groups",
          "request": {
            "method": "GET",
            "header": [],
            "url": {
              "raw": "{{base_url}}/service/api/security/groups",
              "host": ["{{base_url}}"],
              "path": ["service", "api", "security", "groups"]
            }
          }
        }
      ]
    }
  ]
}
```

## Pre-request Scripts

### Generate Dynamic Data

```javascript
// Generate unique identifiers
const timestamp = new Date().getTime();
const randomString = Math.random().toString(36).substring(7);

pm.environment.set('timestamp', timestamp);
pm.environment.set('random_string', randomString);
pm.environment.set('unique_name', `TEST_${timestamp}_${randomString}`);

// Generate test data
pm.environment.set('test_email', `test.${timestamp}@example.com`);
pm.environment.set('test_description', `Generated at ${new Date().toISOString()}`);
```

### Set Dynamic Headers

```javascript
// Add custom headers
pm.request.headers.add({
    key: 'X-Request-ID',
    value: pm.variables.replaceIn('{{$guid}}')
});

// Add timestamp
pm.request.headers.add({
    key: 'X-Timestamp',
    value: new Date().toISOString()
});
```

## Test Scripts

### Comprehensive Response Testing

```javascript
// Test response time
pm.test("Response time is less than 2000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});

// Test status code
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

// Test response structure
pm.test("Response has correct structure", function () {
    const response = pm.response.json();
    pm.expect(response).to.be.an('object');
    pm.expect(response).to.have.property('status');
});

// Test specific values
pm.test("Operation successful", function () {
    const response = pm.response.json();
    pm.expect(response.status).to.equal('S');
});

// Save data for next request
if (pm.response.code === 200) {
    const response = pm.response.json();
    if (response.taskId) {
        pm.environment.set('last_task_id', response.taskId);
        console.log('Saved task ID:', response.taskId);
    }
}
```

### Error Handling Tests

```javascript
// Test error scenarios
if (pm.response.code !== 200) {
    pm.test("Error response has message", function () {
        const response = pm.response.json();
        pm.expect(response).to.have.property('message');
        console.error('Error:', response.message);
    });
}

// Retry logic
const maxRetries = 3;
let retryCount = pm.environment.get('retry_count') || 0;

if (pm.response.code === 503 && retryCount < maxRetries) {
    retryCount++;
    pm.environment.set('retry_count', retryCount);
    console.log(`Retrying... (${retryCount}/${maxRetries})`);
    setTimeout(() => {
        postman.setNextRequest(pm.info.requestName);
    }, 2000);
} else {
    pm.environment.unset('retry_count');
}
```

## Collection Variables

### Environment-Specific Variables

```json
{
  "dev": {
    "base_url": "https://dev.epmwarecloud.com",
    "timeout": "30000",
    "retry_count": "3"
  },
  "test": {
    "base_url": "https://test.epmwarecloud.com",
    "timeout": "60000",
    "retry_count": "5"
  },
  "prod": {
    "base_url": "https://prod.epmwarecloud.com",
    "timeout": "120000",
    "retry_count": "3"
  }
}
```

## Workflow Examples

### Complete Export Workflow

```javascript
// Workflow runner collection
{
  "name": "Export Workflow",
  "item": [
    {
      "name": "1. Start Export",
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.environment.set('workflow_step', 1);",
              "pm.environment.set('workflow_status', 'running');",
              "postman.setNextRequest('2. Check Status');"
            ]
          }
        }
      ]
    },
    {
      "name": "2. Check Status",
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "const response = pm.response.json();",
              "if (response.status === 'S') {",
              "    pm.environment.set('workflow_step', 2);",
              "    postman.setNextRequest('3. Download File');",
              "} else if (response.status === 'N') {",
              "    setTimeout(() => {",
              "        postman.setNextRequest('2. Check Status');",
              "    }, 5000);",
              "} else {",
              "    pm.environment.set('workflow_status', 'failed');",
              "    postman.setNextRequest(null);",
              "}"
            ]
          }
        }
      ]
    },
    {
      "name": "3. Download File",
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.environment.set('workflow_step', 3);",
              "pm.environment.set('workflow_status', 'completed');",
              "console.log('Workflow completed successfully!');"
            ]
          }
        }
      ]
    }
  ]
}
```

## Newman Integration

### Running Collections from Command Line

```bash
# Install Newman
npm install -g newman

# Run collection
newman run EPMware_API.postman_collection.json \
  -e EPMware_Prod.postman_environment.json \
  --reporters cli,json \
  --reporter-json-export output.json

# Run with data file
newman run EPMware_API.postman_collection.json \
  -d users.csv \
  -n 10 \
  --delay-request 1000

# Run specific folder
newman run EPMware_API.postman_collection.json \
  --folder "Security Module" \
  -e EPMware_Prod.postman_environment.json
```

### CI/CD Integration

```yaml
# GitHub Actions example
name: API Tests
on: [push]
jobs:
  api-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install Newman
        run: npm install -g newman
      - name: Run API Tests
        run: |
          newman run postman/EPMware_API.json \
            -e postman/environment.json \
            --reporters cli,junit \
            --reporter-junit-export results.xml
      - name: Upload Results
        uses: actions/upload-artifact@v2
        with:
          name: test-results
          path: results.xml
```

## Monitoring with Postman

### Setting Up Monitors

```javascript
// Monitor configuration
{
  "name": "EPMware API Health Check",
  "schedule": {
    "cron": "0 */6 * * *"  // Every 6 hours
  },
  "collection": "EPMware_Health_Check",
  "environment": "Production",
  "notifications": {
    "onFailure": ["email@example.com"],
    "onSuccess": false
  }
}
```

## Best Practices

### 1. Use Collection Variables

```javascript
// Define at collection level
pm.collectionVariables.set("api_version", "1.3");
pm.collectionVariables.set("default_timeout", 30000);
```

### 2. Implement Request Chaining

```javascript
// Save response data for next request
const response = pm.response.json();
pm.collectionVariables.set("previous_response", JSON.stringify(response));

// Use in next request
const previousData = JSON.parse(pm.collectionVariables.get("previous_response"));
```

### 3. Error Recovery

```javascript
// Implement circuit breaker pattern
const failures = pm.collectionVariables.get("failure_count") || 0;

if (pm.response.code !== 200) {
    pm.collectionVariables.set("failure_count", failures + 1);
    
    if (failures > 5) {
        console.error("Circuit breaker triggered - stopping tests");
        postman.setNextRequest(null);
    }
} else {
    pm.collectionVariables.set("failure_count", 0);
}
```

## Sharing Collections

### Export Collection

1. Right-click collection in Postman
2. Select "Export" 
3. Choose Collection v2.1 format
4. Save JSON file

### Import Collection

1. Click "Import" in Postman
2. Select collection JSON file
3. Import environment file separately
4. Update environment variables

## Next Steps

- Download the [complete collection](https://github.com/epmware/api-collections)
- Review [API Reference](../reference/)
- Explore [Python Examples](python-examples.md)
- Check [PowerShell Scripts](powershell-scripts.md)