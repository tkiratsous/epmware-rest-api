# Error Handling

Robust error handling is essential for building reliable applications that interact with the EPMware REST API. This guide covers error types, handling strategies, and best practices.

## Overview

EPMware REST API errors can occur at multiple levels:
- **Network Level**: Connection failures, timeouts
- **HTTP Level**: 4xx and 5xx status codes
- **Application Level**: Business logic errors
- **Data Level**: Validation failures

## Error Response Format

### Standard Error Structure

```json
{
  "status": "E",
  "message": "Detailed error description",
  "errorCode": "ERR_001",
  "details": {
    "field": "userName",
    "reason": "Username already exists"
  },
  "timestamp": "2025-01-15T10:30:00Z"
}
```

### Error Response Fields

| Field | Type | Description | Always Present |
|-------|------|-------------|----------------|
| `status` | String | Always "E" for errors | Yes |
| `message` | String | Human-readable error message | Yes |
| `errorCode` | String | Machine-readable error code | Sometimes |
| `details` | Object | Additional error context | Sometimes |
| `timestamp` | String | When error occurred | Sometimes |

## HTTP Status Codes

### Client Errors (4xx)

| Code | Name | Common Causes | Example Response |
|------|------|---------------|------------------|
| `400` | Bad Request | Invalid parameters, malformed JSON | `{"status": "E", "message": "Invalid request format"}` |
| `401` | Unauthorized | Missing/invalid token | `{"status": "E", "message": "Authentication required"}` |
| `403` | Forbidden | Insufficient permissions | `{"status": "E", "message": "Access denied"}` |
| `404` | Not Found | Resource doesn't exist | `{"status": "E", "message": "Resource not found"}` |
| `409` | Conflict | Duplicate resource | `{"status": "E", "message": "Resource already exists"}` |
| `422` | Unprocessable Entity | Validation failure | `{"status": "E", "message": "Validation failed"}` |
| `429` | Too Many Requests | Rate limit exceeded | `{"status": "E", "message": "Rate limit exceeded"}` |

### Server Errors (5xx)

| Code | Name | Common Causes | Example Response |
|------|------|---------------|------------------|
| `500` | Internal Server Error | Server exception | `{"status": "E", "message": "Internal server error"}` |
| `502` | Bad Gateway | Proxy/gateway error | `{"status": "E", "message": "Gateway error"}` |
| `503` | Service Unavailable | Service down/maintenance | `{"status": "E", "message": "Service temporarily unavailable"}` |
| `504` | Gateway Timeout | Request timeout | `{"status": "E", "message": "Request timeout"}` |

## Module-Specific Errors

### Task Module Errors

```json
{
  "status": "E",
  "message": "Task not found",
  "errorCode": "TASK_404",
  "details": {
    "taskId": "999999",
    "suggestion": "Verify task ID and try again"
  }
}
```

### ERP Module Errors

```json
{
  "status": "E",
  "message": "ERP Import configuration not found",
  "errorCode": "ERP_CONFIG_404",
  "details": {
    "configName": "INVALID_CONFIG",
    "availableConfigs": ["ASOALL_Account", "GL_Data"]
  }
}
```

### Security Module Errors

```json
{
  "status": "E",
  "message": "User creation failed",
  "errorCode": "USR_CREATE_409",
  "details": {
    "field": "userName",
    "value": "JSMITH",
    "reason": "Username already exists"
  }
}
```

## Error Handling Strategies

### 1. Comprehensive Error Handler (Python)

```python
import requests
import time
from enum import Enum

class ErrorStrategy(Enum):
    RETRY = "retry"
    FAIL = "fail"
    FALLBACK = "fallback"
    IGNORE = "ignore"

class EPMwareAPIError(Exception):
    def __init__(self, status_code, message, details=None):
        self.status_code = status_code
        self.message = message
        self.details = details
        super().__init__(self.message)

class APIErrorHandler:
    def __init__(self, max_retries=3, retry_delay=5):
        self.max_retries = max_retries
        self.retry_delay = retry_delay
        
        # Define error handling strategies
        self.strategies = {
            400: ErrorStrategy.FAIL,
            401: ErrorStrategy.FAIL,
            403: ErrorStrategy.FAIL,
            404: ErrorStrategy.FAIL,
            409: ErrorStrategy.FAIL,
            429: ErrorStrategy.RETRY,
            500: ErrorStrategy.RETRY,
            502: ErrorStrategy.RETRY,
            503: ErrorStrategy.RETRY,
            504: ErrorStrategy.RETRY
        }
    
    def handle_error(self, response, attempt=1):
        """Handle API error with appropriate strategy"""
        
        status_code = response.status_code
        strategy = self.strategies.get(status_code, ErrorStrategy.FAIL)
        
        # Parse error details
        try:
            error_data = response.json()
            message = error_data.get('message', 'Unknown error')
            details = error_data.get('details')
        except:
            message = response.text or response.reason
            details = None
        
        # Log error
        self.log_error(status_code, message, details, attempt)
        
        # Apply strategy
        if strategy == ErrorStrategy.RETRY and attempt < self.max_retries:
            return self.retry_with_backoff(attempt)
        
        elif strategy == ErrorStrategy.FALLBACK:
            return self.use_fallback()
        
        elif strategy == ErrorStrategy.IGNORE:
            print(f"Warning: Ignoring error {status_code}: {message}")
            return None
        
        else:  # FAIL
            raise EPMwareAPIError(status_code, message, details)
    
    def retry_with_backoff(self, attempt):
        """Implement exponential backoff"""
        delay = self.retry_delay * (2 ** (attempt - 1))
        print(f"Retrying in {delay} seconds... (Attempt {attempt}/{self.max_retries})")
        time.sleep(delay)
        return True  # Signal to retry
    
    def log_error(self, status_code, message, details, attempt):
        """Log error details"""
        print(f"Error {status_code} (Attempt {attempt}): {message}")
        if details:
            print(f"Details: {details}")
    
    def use_fallback(self):
        """Implement fallback logic"""
        print("Using fallback mechanism...")
        return False

# Usage
def make_api_call(url, headers, data=None, method='GET'):
    handler = APIErrorHandler(max_retries=3, retry_delay=5)
    
    for attempt in range(1, handler.max_retries + 1):
        try:
            if method == 'GET':
                response = requests.get(url, headers=headers)
            elif method == 'POST':
                response = requests.post(url, headers=headers, json=data)
            
            # Check for HTTP errors
            if response.status_code >= 400:
                should_retry = handler.handle_error(response, attempt)
                if should_retry:
                    continue
                else:
                    break
            
            # Check for application errors
            result = response.json()
            if result.get('status') == 'E':
                raise EPMwareAPIError(
                    response.status_code,
                    result.get('message', 'Unknown error'),
                    result.get('details')
                )
            
            return result
            
        except requests.RequestException as e:
            print(f"Network error: {e}")
            if attempt < handler.max_retries:
                handler.retry_with_backoff(attempt)
            else:
                raise
```

### 2. JavaScript/Node.js Error Handler

```javascript
class EPMwareAPIError extends Error {
  constructor(statusCode, message, details = null) {
    super(message);
    this.name = 'EPMwareAPIError';
    this.statusCode = statusCode;
    this.details = details;
  }
}

class APIErrorHandler {
  constructor(options = {}) {
    this.maxRetries = options.maxRetries || 3;
    this.retryDelay = options.retryDelay || 5000;
    this.onError = options.onError || (() => {});
  }
  
  async handleResponse(response) {
    if (!response.ok) {
      await this.handleError(response);
    }
    
    const data = await response.json();
    
    if (data.status === 'E') {
      throw new EPMwareAPIError(
        response.status,
        data.message,
        data.details
      );
    }
    
    return data;
  }
  
  async handleError(response) {
    const statusCode = response.status;
    let errorData;
    
    try {
      errorData = await response.json();
    } catch {
      errorData = { message: response.statusText };
    }
    
    // Log error
    console.error(`API Error ${statusCode}: ${errorData.message}`);
    this.onError(statusCode, errorData);
    
    // Determine if we should retry
    if (this.shouldRetry(statusCode)) {
      throw new Error('RETRY_NEEDED');
    }
    
    throw new EPMwareAPIError(
      statusCode,
      errorData.message,
      errorData.details
    );
  }
  
  shouldRetry(statusCode) {
    const retryableCodes = [429, 500, 502, 503, 504];
    return retryableCodes.includes(statusCode);
  }
  
  async withRetry(fn) {
    let lastError;
    
    for (let attempt = 1; attempt <= this.maxRetries; attempt++) {
      try {
        return await fn();
      } catch (error) {
        lastError = error;
        
        if (error.message === 'RETRY_NEEDED' && attempt < this.maxRetries) {
          const delay = this.retryDelay * Math.pow(2, attempt - 1);
          console.log(`Retrying in ${delay}ms... (Attempt ${attempt}/${this.maxRetries})`);
          await new Promise(resolve => setTimeout(resolve, delay));
        } else {
          break;
        }
      }
    }
    
    throw lastError;
  }
}

// Usage
async function callAPI(url, options = {}) {
  const handler = new APIErrorHandler({
    maxRetries: 3,
    retryDelay: 5000,
    onError: (code, data) => {
      // Custom error logging
      console.log(`Custom log: Error ${code}`, data);
    }
  });
  
  return handler.withRetry(async () => {
    const response = await fetch(url, options);
    return handler.handleResponse(response);
  });
}

// Example call
callAPI('https://api.example.com/endpoint', {
  headers: { 'Authorization': 'Token xxx' }
})
.then(data => console.log('Success:', data))
.catch(error => {
  if (error instanceof EPMwareAPIError) {
    console.error(`API Error ${error.statusCode}: ${error.message}`);
    if (error.details) {
      console.error('Details:', error.details);
    }
  } else {
    console.error('Unexpected error:', error);
  }
});
```

### 3. PowerShell Error Handler

```powershell
function Invoke-EPMwareAPIWithErrorHandling {
    param(
        [string]$Uri,
        [string]$Method = "GET",
        [hashtable]$Headers,
        [object]$Body,
        [int]$MaxRetries = 3,
        [int]$RetryDelaySeconds = 5
    )
    
    $attempt = 0
    $lastError = $null
    
    while ($attempt -lt $MaxRetries) {
        $attempt++
        
        try {
            Write-Verbose "Attempt $attempt of $MaxRetries"
            
            $params = @{
                Uri = $Uri
                Method = $Method
                Headers = $Headers
                ErrorAction = "Stop"
            }
            
            if ($Body) {
                $params.Body = $Body | ConvertTo-Json
            }
            
            $response = Invoke-RestMethod @params
            
            # Check application-level status
            if ($response.status -eq 'E') {
                throw "API Error: $($response.message)"
            }
            
            return $response
            
        } catch {
            $lastError = $_
            
            # Parse error details
            $statusCode = $_.Exception.Response.StatusCode.value__
            $errorMessage = $_.ErrorDetails.Message
            
            Write-Warning "Error $statusCode on attempt $attempt: $errorMessage"
            
            # Determine if we should retry
            $retryableCodes = @(429, 500, 502, 503, 504)
            
            if ($statusCode -in $retryableCodes -and $attempt -lt $MaxRetries) {
                $delay = $RetryDelaySeconds * [Math]::Pow(2, $attempt - 1)
                Write-Host "Retrying in $delay seconds..." -ForegroundColor Yellow
                Start-Sleep -Seconds $delay
            } else {
                # Non-retryable error or max retries reached
                break
            }
        }
    }
    
    # All retries exhausted
    throw "API call failed after $attempt attempts: $lastError"
}

# Usage
try {
    $result = Invoke-EPMwareAPIWithErrorHandling `
        -Uri "https://api.example.com/endpoint" `
        -Method "POST" `
        -Headers @{ "Authorization" = "Token xxx" } `
        -Body @{ name = "test" } `
        -MaxRetries 3 `
        -RetryDelaySeconds 5
    
    Write-Host "Success: $($result.message)" -ForegroundColor Green
    
} catch {
    Write-Host "Failed: $_" -ForegroundColor Red
    
    # Log to file
    Add-Content -Path "api_errors.log" -Value "$(Get-Date): $_"
}
```

## Common Error Scenarios

### Authentication Errors

**Scenario**: Token expired or invalid

```python
def handle_auth_error(error):
    """Handle authentication errors"""
    
    if error.status_code == 401:
        # Try to refresh token
        new_token = refresh_token()
        
        if new_token:
            # Update headers and retry
            headers['Authorization'] = f'Token {new_token}'
            return True
        else:
            # Cannot refresh, user must re-authenticate
            raise Exception("Authentication failed. Please login again.")
    
    return False
```

### Rate Limiting

**Scenario**: Too many requests

```python
import time

def handle_rate_limit(response):
    """Handle rate limit errors with smart backoff"""
    
    # Check for Retry-After header
    retry_after = response.headers.get('Retry-After')
    
    if retry_after:
        # Server told us when to retry
        wait_time = int(retry_after)
    else:
        # Use exponential backoff
        wait_time = 60  # Default to 1 minute
    
    print(f"Rate limited. Waiting {wait_time} seconds...")
    time.sleep(wait_time)
    
    return True
```

### Validation Errors

**Scenario**: Invalid input data

```javascript
function handleValidationError(error) {
  if (error.statusCode === 422 || error.statusCode === 400) {
    const details = error.details || {};
    
    // Parse field-specific errors
    if (details.field) {
      console.error(`Validation error on field '${details.field}': ${details.reason}`);
      
      // Attempt to fix common issues
      if (details.field === 'email' && details.reason.includes('invalid format')) {
        // Validate and correct email format
        return sanitizeEmail(details.value);
      }
    }
    
    // Show user-friendly message
    alert(`Please check your input: ${error.message}`);
  }
  
  return null;
}
```

### Service Unavailable

**Scenario**: Service temporarily down

```python
def handle_service_unavailable():
    """Implement circuit breaker pattern"""
    
    class CircuitBreaker:
        def __init__(self, failure_threshold=5, timeout=60):
            self.failure_threshold = failure_threshold
            self.timeout = timeout
            self.failures = 0
            self.last_failure = None
            self.state = 'CLOSED'  # CLOSED, OPEN, HALF_OPEN
        
        def call(self, func, *args, **kwargs):
            if self.state == 'OPEN':
                if time.time() - self.last_failure > self.timeout:
                    self.state = 'HALF_OPEN'
                else:
                    raise Exception("Circuit breaker is OPEN")
            
            try:
                result = func(*args, **kwargs)
                
                if self.state == 'HALF_OPEN':
                    self.state = 'CLOSED'
                    self.failures = 0
                
                return result
                
            except Exception as e:
                self.failures += 1
                self.last_failure = time.time()
                
                if self.failures >= self.failure_threshold:
                    self.state = 'OPEN'
                    print(f"Circuit breaker opened after {self.failures} failures")
                
                raise e
    
    return CircuitBreaker()
```

## Error Recovery Patterns

### 1. Automatic Retry with Jitter

```python
import random

def retry_with_jitter(func, max_retries=3, base_delay=1):
    """Retry with randomized exponential backoff"""
    
    for attempt in range(max_retries):
        try:
            return func()
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            
            # Add jitter to prevent thundering herd
            jitter = random.uniform(0, base_delay)
            delay = base_delay * (2 ** attempt) + jitter
            
            print(f"Retry {attempt + 1} in {delay:.2f} seconds...")
            time.sleep(delay)
```

### 2. Fallback Mechanism

```python
def with_fallback(primary_func, fallback_func):
    """Execute with fallback option"""
    
    try:
        return primary_func()
    except Exception as e:
        print(f"Primary failed: {e}")
        print("Using fallback mechanism...")
        
        try:
            return fallback_func()
        except Exception as fallback_error:
            print(f"Fallback also failed: {fallback_error}")
            raise Exception("Both primary and fallback failed")
```

### 3. Queue Failed Requests

```python
import queue
import threading

class FailedRequestQueue:
    def __init__(self):
        self.queue = queue.Queue()
        self.processing = False
    
    def add_failed_request(self, request_data):
        """Add failed request to retry queue"""
        self.queue.put({
            'data': request_data,
            'timestamp': time.time(),
            'attempts': 0
        })
    
    def process_queue(self):
        """Process failed requests in background"""
        if self.processing:
            return
        
        self.processing = True
        thread = threading.Thread(target=self._process)
        thread.daemon = True
        thread.start()
    
    def _process(self):
        while not self.queue.empty():
            item = self.queue.get()
            
            try:
                # Retry the request
                result = make_api_call(item['data'])
                print(f"Successfully processed queued request: {result}")
                
            except Exception as e:
                item['attempts'] += 1
                
                if item['attempts'] < 3:
                    # Re-queue for another attempt
                    self.queue.put(item)
                else:
                    # Log permanently failed request
                    log_failed_request(item)
            
            time.sleep(5)  # Delay between retries
        
        self.processing = False
```

## Error Logging Best Practices

### Structured Logging

```python
import logging
import json
from datetime import datetime

class APIErrorLogger:
    def __init__(self, log_file='api_errors.log'):
        self.logger = logging.getLogger('EPMwareAPI')
        handler = logging.FileHandler(log_file)
        formatter = logging.Formatter(
            '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
        )
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)
        self.logger.setLevel(logging.ERROR)
    
    def log_error(self, error_data):
        """Log structured error data"""
        
        log_entry = {
            'timestamp': datetime.utcnow().isoformat(),
            'status_code': error_data.get('status_code'),
            'endpoint': error_data.get('endpoint'),
            'method': error_data.get('method'),
            'error_message': error_data.get('message'),
            'details': error_data.get('details'),
            'user': error_data.get('user'),
            'request_id': error_data.get('request_id')
        }
        
        self.logger.error(json.dumps(log_entry))
        
        # Also track metrics
        self.update_error_metrics(error_data)
    
    def update_error_metrics(self, error_data):
        """Track error metrics for monitoring"""
        
        # Increment error counters (integrate with monitoring system)
        status_code = error_data.get('status_code')
        endpoint = error_data.get('endpoint')
        
        # Example: Send to monitoring service
        # metrics.increment(f'api.errors.{status_code}')
        # metrics.increment(f'api.errors.endpoint.{endpoint}')
```

## Testing Error Handling

### Unit Test Example

```python
import unittest
from unittest.mock import Mock, patch

class TestErrorHandling(unittest.TestCase):
    
    def test_retry_on_500_error(self):
        """Test that 500 errors trigger retry"""
        
        handler = APIErrorHandler(max_retries=3)
        
        # Mock response with 500 error
        mock_response = Mock()
        mock_response.status_code = 500
        mock_response.json.return_value = {
            'status': 'E',
            'message': 'Internal server error'
        }
        
        with patch('time.sleep'):  # Skip actual delays
            should_retry = handler.handle_error(mock_response, attempt=1)
        
        self.assertTrue(should_retry)
    
    def test_no_retry_on_400_error(self):
        """Test that 400 errors don't trigger retry"""
        
        handler = APIErrorHandler(max_retries=3)
        
        mock_response = Mock()
        mock_response.status_code = 400
        mock_response.json.return_value = {
            'status': 'E',
            'message': 'Bad request'
        }
        
        with self.assertRaises(EPMwareAPIError) as context:
            handler.handle_error(mock_response, attempt=1)
        
        self.assertEqual(context.exception.status_code, 400)
```

## Next Steps

- [Response Formats](response-formats.md) - Understand response structures
- [API Reference](../reference/) - Complete endpoint documentation
- [Best Practices](../best-practices/) - Error handling best practices