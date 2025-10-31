# Appendix A - Module Actions

Complete reference of all modules and their supported actions.

## Module Action Matrix

| Module | Action | Method | Description |
|--------|--------|--------|-------------|
| **Task** | get_status | GET | Get task status |
| **Task** | get_log_file | GET | Download task log |
| **ERP** | upload_file | POST | Upload file for import |
| **ERP** | run | POST | Execute import |
| **Deployment** | run | POST | Execute deployment |
| **Deployment** | get_execution_details | GET | Get execution history |
| **Export** | run | POST | Execute export |
| **Export** | download_file | GET | Download export file |
| **Export** | get_execution_details | GET | Get execution history |
| **Security** | users/sso/create | POST | Create SSO user |
| **Security** | users/sso/update | PUT | Update user |
| **Security** | users/sso/enable | PATCH | Enable user |
| **Security** | users/sso/disable | PATCH | Disable user |
| **Security** | users/sso/assign_groups | POST | Assign groups |
| **Security** | users/sso/unassign_groups | POST | Remove groups |
| **Security** | users | GET | List all users |
| **Security** | groups | GET | List all groups |

## Action Details

### Task Module

**get_status**
- Path: `/service/api/task/get_status/{task_id}`
- Returns: Task status, progress, messages

**get_log_file**
- Path: `/service/api/task/get_log_file/{task_id}`
- Returns: Complete task execution log

### ERP Module

**upload_file**
- Path: `/service/api/erp/upload_file`
- Parameters: file, profileName
- Returns: Upload task ID

**run**
- Path: `/service/api/erp/run`
- Parameters: profileName, parameters
- Returns: Import task ID

### Deployment Module

**run**
- Path: `/service/api/deployment/run`
- Parameters: deploymentName, version
- Returns: Deployment task ID

**get_execution_details**
- Path: `/service/api/deployment/get_execution_details`
- Parameters: name, latest_only
- Returns: Execution history

---

[← Back to Appendices](../) | [Sample Scripts →](../sample-scripts/)
