# Rate Limiting Best Practices

Strategies for handling API rate limits effectively.

## Rate Limits

- Standard endpoints: 100 requests per minute
- Bulk operations: 10 requests per minute
- File uploads: 50 requests per minute

## Best Practices

1. **Implement request queuing**
2. **Use exponential backoff for retries**
3. **Monitor rate limit headers**
4. **Batch operations when possible**

---

[← Back to Best Practices](../) | [Security →](../security/)
