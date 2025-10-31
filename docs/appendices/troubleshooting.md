# Appendix C - Troubleshooting

Common issues and solutions for EPMware REST API integration.

## Common Issues

### Authentication Issues

**Problem**: 401 Unauthorized error
```json
{
  "status": "E",
  "message": "Authentication required"
}
```

**Solutions**:
1. Verify token is correct
2. Check token hasn't expired
3. Ensure Authorization header format is correct: `Token <your-token>`
4. Regenerate token in EPMware Security Module

---

### Connection Issues

**Problem**: Connection timeout or refused

**Solutions**:
1. Verify EPMware URL is correct
2. Check network connectivity
3. Verify firewall rules allow HTTPS traffic
4. Test with curl: `curl -I https://your-epmware-url`

---

### Rate Limiting

**Problem**: 429 Too Many Requests

**Solutions**:
1. Implement rate limiting in your code
2. Add delays between requests
3. Use batch operations where possible
4. Check Retry-After header for wait time

---

### Import Failures

**Problem**: Import fails with validation errors

**Solutions**:
1. Check file format matches import profile
2. Verify column mappings are correct
3. Ensure required fields are present
4. Review error log for specific validation issues

---

### Performance Issues

**Problem**: Slow API responses

**Solutions**:
1. Use pagination for large result sets
2. Implement caching where appropriate
3. Enable compression
4. Process large files in chunks

---

## Debug Checklist

### For Any Issue

1. ✅ Check API endpoint URL is correct
2. ✅ Verify authentication token
3. ✅ Review request headers and body
4. ✅ Check response status code
5. ✅ Parse error message from response
6. ✅ Review server logs if available
7. ✅ Test with minimal example
8. ✅ Check EPMware system status

### Logging for Debugging

```python
import logging
import json

logging.basicConfig(level=logging.DEBUG)

def debug_api_call(method, url, headers, data, response):
    """Log API call details for debugging"""
    
    debug_info = {
        'request': {
            'method': method,
            'url': url,
            'headers': {k: v for k, v in headers.items() if k != 'Authorization'},
            'data': data
        },
        'response': {
            'status_code': response.status_code,
            'headers': dict(response.headers),
            'body': response.text[:1000]  # First 1000 chars
        }
    }
    
    logging.debug(json.dumps(debug_info, indent=2))
```

---

[← Back to Appendices](../) | [Migration Guide →](../migration-guide/)
