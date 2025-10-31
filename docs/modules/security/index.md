# Security Module

Manage users, groups, and permissions through the EPMware REST API.

## Overview

The Security module provides comprehensive APIs for user management, group assignments, and access control. It enables automated provisioning, role management, and security administration.

## Key Features

- **User Management**: Create, update, enable/disable users
- **Group Management**: Assign and manage security groups
- **Role-Based Access**: Configure permissions and access rights
- **SSO Integration**: Support for Single Sign-On users
- **Audit Trail**: Track security changes

## Available Endpoints

### User Management
- `/service/api/security/users/sso/create` - Create SSO user
- `/service/api/security/users/sso/update/{username}` - Update user details
- `/service/api/security/users/sso/enable/{username}` - Enable user
- `/service/api/security/users/sso/disable/{username}` - Disable user
- `/service/api/security/users` - Get all users

### Group Management
- `/service/api/security/users/sso/assign_groups` - Assign groups to user
- `/service/api/security/users/sso/unassign_groups` - Remove groups from user
- `/service/api/security/groups` - Get all groups
- `/service/api/security/users/groups` - Get user's groups
- `/service/api/security/groups/users` - Get group members

---

[← Back to Modules](../) | [User Management](users/create/)
