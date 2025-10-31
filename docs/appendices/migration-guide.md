# Appendix D - Migration Guide

Guide for migrating from previous versions of the EPMware REST API.

## Version History

| Version | Release Date | Status | Support End |
|---------|-------------|--------|-------------|
| v1.0 | Jan 2023 | Deprecated | Dec 2023 |
| v1.1 | Jun 2023 | Deprecated | Jun 2024 |
| v1.2 | Jan 2024 | Supported | Jan 2025 |
| v1.3 | May 2024 | Current | - |

## Migration from v1.2 to v1.3

### Breaking Changes

1. **Authentication Header Format**
   - Old: `Authorization: Bearer <token>`
   - New: `Authorization: Token <token>`

2. **Endpoint Changes**
   - Old: `/api/v1/security/users`
   - New: `/service/api/security/users`

3. **Response Format**
   - Status field values changed from 0/1 to S/E/W

### Migration Steps

1. **Update Authentication**
```python
# Old
headers = {'Authorization': f'Bearer {token}'}

# New
headers = {'Authorization': f'Token {token}'}
```

2. **Update Base URLs**
```python
# Old
BASE_URL = 'https://demo.epmwarecloud.com/api/v1'

# New
BASE_URL = 'https://demo.epmwarecloud.com/service/api'
```

3. **Update Status Checks**
```python
# Old
if response['status'] == 1:
    # Success

# New
if response['status'] == 'S':
    # Success
```

## Deprecated Features

### Removed in v1.3

- `/api/v1/legacy/*` endpoints
- XML response format
- Basic authentication

### Deprecation Timeline

| Feature | Deprecated | Removal Date |
|---------|------------|--------------|
| Legacy endpoints | v1.2 | v1.3 |
| XML format | v1.1 | v1.3 |
| Basic auth | v1.0 | v1.2 |

## Migration Tools

### Compatibility Layer

```python
class CompatibilityAdapter:
    """Adapter for backward compatibility"""
    
    def __init__(self, client):
        self.client = client
    
    def legacy_get_users(self):
        """Map old method to new"""
        response = self.client.get_users()
        
        # Transform response to old format
        if response.success:
            old_format = {
                'status': 1 if response.data['status'] == 'S' else 0,
                'data': response.data.get('users', [])
            }
            return old_format
        
        return {'status': 0, 'error': response.message}
```

## Testing Migration

### Validation Script

```python
def validate_migration():
    """Validate API migration"""
    
    tests = []
    
    # Test authentication
    tests.append(test_authentication())
    
    # Test each endpoint
    for endpoint in ENDPOINTS:
        tests.append(test_endpoint(endpoint))
    
    # Generate report
    passed = sum(1 for t in tests if t['passed'])
    print(f"Migration validation: {passed}/{len(tests)} tests passed")
    
    return all(t['passed'] for t in tests)
```

## Support Resources

- Migration assistance: [migration@epmware.com](mailto:migration@epmware.com)
- Technical documentation: [docs.epmware.com](https://docs.epmware.com)
- Support portal: [support.epmware.com](https://support.epmware.com)

---

[← Back to Appendices](../)
