# API Modules

The EPMware REST API is organized into functional modules, each providing specific capabilities for different aspects of the system.

## Available Modules

Modules and their related Actions are as listed below. Actions are listed inside Modules.


| Module     | Action                | Notes                                                                 |
|------------|----------------------|-----------------------------------------------------------------------|
| Task       | get_status           | Get status for a given task ID                                       |
| Task       | get_log_file         | Download task details                                                |
| ERP        | upload_file          | Upload metadata file and load its data into ERP import interface     |
| ERP        | run                  | Execute ERP import (one-time scheduled)                              |
| Deployment | run                  | Execute deployment (one-time scheduled)                              |
| Deployment | get_execution_details| Get execution details for a given deployment name                    |
| Export     | run                  | Execute export for a profile or book                                 |
| Export     | download_file        | Download export file                                                 |
| Export     | get_execution_details| Get execution details for a given export name                        |
| Security   | create               | Create a new SSO user                                                |
| Security   | update               | Update details of an existing SSO user                               |
| Security   | disable              | Disable an existing SSO user                                         |
| Security   | enable               | Enable an existing SSO user                                          |
| Security   | assign_groups        | Add SSO user to given security groups                                |
| Security   | unassign_groups      | Remove SSO user from given security groups                           |
| Security   | users                | Get details of all native or SAML users                              |
| Security   | usergroups           | Get list of security groups assigned to a given user                 |
| Security   | groups               | Get details of all security groups                                   |
| Security   | groupuser            | Get list of users tagged to a security group                         |


## Next Steps

Ready to explore specific modules?

<div class="module-links" markdown="1">

📋 **[Task Module](task/)** - Learn about task monitoring

🏢 **[ERP Module](erp/)** - Master data import operations

🚀 **[Deployment Module](deployment/)** - Automate deployments

📤 **[Export Module](export/)** - Configure data exports

🔐 **[Security Module](security/)** - Manage users and permissions

</div>
---

[← Back to Getting Started](../getting-started/) | [API Reference →](../reference/)
