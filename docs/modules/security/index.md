# Security Module

Manage users, groups, and permissions through the EPMware REST API.

## Overview

The Security module provides comprehensive APIs for user management, group assignments, and access control. It enables automated provisioning, role management, and security administration.


## Available Endpoints

### User Management
- `http(s)://<EPMWARE_URL>/service/api/security/users/sso/create` - Create SSO user
- `http(s)://<EPMWARE_URL>/service/api/security/users/sso/update/{username}` - Update user details
- `http(s)://<EPMWARE_URL>/service/api/security/users/sso/enable/{username}` - Enable user
- `http(s)://<EPMWARE_URL>/service/api/security/users/sso/disable/{username}` - Disable user
- `http(s)://<EPMWARE_URL>/service/api/security/users` - Get all users

### Group Management
- `http(s)://<EPMWARE_URL>/service/api/security/users/sso/assign_groups` - Assign groups to user
- `http(s)://<EPMWARE_URL>/service/api/security/users/sso/unassign_groups` - Remove groups from user
- `http(s)://<EPMWARE_URL>/service/api/security/groups` - Get all groups
- `http(s)://<EPMWARE_URL>/service/api/security/users/groups?userName=<usernanme>` - Get user's groups
- `http(s)://<EPMWARE_URL>/service/api/security/groups/users` - Get group members

---

[← Back to Modules](../) | [User Management](users/create/)
