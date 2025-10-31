# API Reference

Complete technical reference for all EPMware REST API endpoints, parameters, and responses.

## Overview

This reference section provides detailed technical documentation for every endpoint in the EPMware REST API. Use this guide for precise implementation details, parameter specifications, and response formats.

## Quick Navigation

<div class="reference-grid">

📋 **[Endpoints Summary](endpoints-summary/)**  
Complete list of all API endpoints

📊 **[Parameters Reference](parameters-reference/)**  
Detailed parameter specifications

🔢 **[Response Codes](response-codes/)**  
HTTP status codes and meanings

✅ **[Status Codes](status-codes/)**  
Task and operation status codes

</div>

## API Base URL

```
https://<EPMWARE_URL>/service/api/
```

## Authentication

All endpoints require token authentication:

```http
Authorization: Token <your-token-here>
```

## HTTP Methods

| Method | Usage | Idempotent |
|--------|-------|------------|
| `GET` | Retrieve resources | Yes |
| `POST` | Create resources or trigger operations | No |
| `PUT` | Update entire resources | Yes |
| `PATCH` | Partial resource updates | No |
| `DELETE` | Remove resources | Yes |

## Content Types

### Request Content Type
```
Content-Type: application/json
```

### Response Content Type
```
Content-Type: application/json
```

Exception: File downloads return appropriate MIME types.

---

[← Back to Home](../) | [Endpoints Summary →](endpoints-summary/)
