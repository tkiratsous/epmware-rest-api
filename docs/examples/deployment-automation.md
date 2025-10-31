# Deployment Automation Examples

Automate EPMware deployments with these examples.

## Deployment Pipeline

```python
import requests
import json
from datetime import datetime

class DeploymentAutomation:
    """Automate EPMware deployments"""
    
    def __init__(self, environments):
        self.environments = environments
    
    def deploy(self, env_name, package_name):
        """Deploy to specific environment"""
        env = self.environments[env_name]
        
        url = f"{env['url']}/service/api/deployment/run"
        headers = {'Authorization': f"Token {env['token']}"}
        
        payload = {
            'deploymentName': package_name,
            'environment': env_name,
            'timestamp': datetime.now().isoformat()
        }
        
        response = requests.post(url, json=payload, headers=headers)
        return response.json()
    
    def promote(self, package_name):
        """Promote through environments"""
        results = {}
        
        for env in ['dev', 'test', 'prod']:
            if env in self.environments:
                print(f"Deploying to {env}...")
                result = self.deploy(env, package_name)
                results[env] = result
                
                if result['status'] != 'S':
                    print(f"Deployment to {env} failed")
                    break
        
        return results

# Configuration
environments = {
    'dev': {
        'url': 'https://dev.epmwarecloud.com',
        'token': 'dev-token'
    },
    'test': {
        'url': 'https://test.epmwarecloud.com',
        'token': 'test-token'
    },
    'prod': {
        'url': 'https://prod.epmwarecloud.com',
        'token': 'prod-token'
    }
}

# Usage
automation = DeploymentAutomation(environments)
results = automation.promote('Q1_2025_Release')
```

---

[← Back to Examples](../) | [Export Automation →](../export-automation/)
