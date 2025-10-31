# Get Export Execution Details

The Get Export Execution Details API retrieves historical execution information for export tasks, including status, timing, file sizes, and performance metrics.

## Overview

This endpoint provides detailed information about export executions, useful for monitoring, auditing, and analyzing export performance over time.

## Endpoint Details

### URL Structure

```
GET /service/api/export/get_execution_details
```

### Full URL Example

```
https://demo.epmwarecloud.com/service/api/export/get_execution_details?name=PBCS&latest_only=Y
```

### Method

`GET`

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | String | Yes | - | Export profile name |
| `latest_only` | String | No | Y | Return only latest execution (Y/N) |

## Request Examples

### Using curl

```bash
# Get latest execution only
curl GET 'https://demo.epmwarecloud.com/service/api/export/get_execution_details?name=PBCS&latest_only=Y' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'

# Get all executions
curl GET 'https://demo.epmwarecloud.com/service/api/export/get_execution_details?name=PBCS&latest_only=N' \
  -H 'Authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7'
```

### Using Python

```python
import requests
from datetime import datetime, timedelta

def get_export_execution_details(base_url, token, export_name, latest_only=True):
    """
    Retrieve export execution details
    
    Args:
        base_url: EPMware base URL
        token: Authentication token  
        export_name: Name of the export profile
        latest_only: Boolean to get only latest execution
    
    Returns:
        dict: Execution details
    """
    url = f"{base_url}/service/api/export/get_execution_details"
    
    headers = {
        'Authorization': f'Token {token}'
    }
    
    params = {
        'name': export_name,
        'latest_only': 'Y' if latest_only else 'N'
    }
    
    response = requests.get(url, headers=headers, params=params)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to get execution details: {response.status_code} - {response.text}")

# Usage example
details = get_export_execution_details(
    base_url="https://demo.epmwarecloud.com",
    token="15388ad5-c9af-4cf3-af47-8021c1ab3fb7",
    export_name="PBCS",
    latest_only=True
)

if details and 'vEWVExportExecutions' in details:
    for execution in details['vEWVExportExecutions']:
        print(f"Export Name: {execution['exportName']}")
        print(f"Execution ID: {execution['id']}")
        print(f"Status: {execution['statusMeaning']}")
        print(f"Start Date: {execution['startDate']}")
        print(f"End Date: {execution['endDate']}")
        print(f"Output File: {execution['outputFileName']}")
        print(f"File Size: {execution['outputFileSize']:,} bytes")
        print(f"Message: {execution.get('message', 'N/A')}")
        print("-" * 40)
```

### Using PowerShell

```powershell
function Get-EPMwareExportDetails {
    param(
        [Parameter(Mandatory=$true)]
        [string]$BaseUrl,
        
        [Parameter(Mandatory=$true)]
        [string]$Token,
        
        [Parameter(Mandatory=$true)]
        [string]$ExportName,
        
        [string]$LatestOnly = "Y"
    )
    
    $url = "$BaseUrl/service/api/export/get_execution_details"
    
    $headers = @{
        "Authorization" = "Token $Token"
    }
    
    $params = @{
        name = $ExportName
        latest_only = $LatestOnly
    }
    
    try {
        $response = Invoke-RestMethod `
            -Uri $url `
            -Method Get `
            -Headers $headers `
            -Body $params
        
        if ($response.vEWVExportExecutions) {
            Write-Host "✅ Found $($response.vEWVExportExecutions.Count) execution(s)" -ForegroundColor Green
            Write-Host ""
            
            foreach ($execution in $response.vEWVExportExecutions) {
                Write-Host "Export Execution Details:" -ForegroundColor Cyan
                Write-Host "  Export Name: $($execution.exportName)"
                Write-Host "  Execution ID: $($execution.id)"
                Write-Host "  Status: $($execution.statusMeaning)"
                Write-Host "  Start: $($execution.startDate)"
                Write-Host "  End: $($execution.endDate)"
                Write-Host "  Output File: $($execution.outputFileName)"
                
                # Format file size
                $sizeMB = [math]::Round($execution.outputFileSize / 1MB, 2)
                Write-Host "  File Size: $sizeMB MB"
                
                if ($execution.message) {
                    Write-Host "  Message: $($execution.message)"
                }
                
                Write-Host "-" * 40
            }
        }
        else {
            Write-Host "No execution records found" -ForegroundColor Yellow
        }
        
        return $response
    }
    catch {
        Write-Host "❌ Failed to get export details: $_" -ForegroundColor Red
        throw
    }
}

# Usage
$details = Get-EPMwareExportDetails `
    -BaseUrl "https://demo.epmwarecloud.com" `
    -Token "15388ad5-c9af-4cf3-af47-8021c1ab3fb7" `
    -ExportName "PBCS" `
    -LatestOnly "Y"
```

## Response Format

### Successful Response

```json
{
  "vEWVExportExecutions": [
    {
      "id": "67890",
      "exportName": "PBCS",
      "startDate": "2025-01-15T02:00:00Z",
      "endDate": "2025-01-15T02:15:30Z",
      "outputFileName": "PBCS_export_20250115_020000.csv",
      "outputFileSize": 15234567,
      "statusCode": "S",
      "statusMeaning": "Success",
      "message": "Export completed successfully",
      "recordCount": 45000,
      "executedBy": "admin",
      "scheduleType": "Scheduled",
      "exportType": "Profile",
      "format": "CSV",
      "compressionType": "NONE"
    }
  ],
  "status": "S",
  "recordCount": 1
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `vEWVExportExecutions` | Array | Array of execution records |
| `id` | String | Execution ID |
| `exportName` | String | Export profile/book name |
| `startDate` | DateTime | Execution start time |
| `endDate` | DateTime | Execution end time |
| `outputFileName` | String | Generated file name |
| `outputFileSize` | Integer | File size in bytes |
| `statusCode` | String | Status code (S/E/W) |
| `statusMeaning` | String | Human-readable status |
| `message` | String | Execution message |
| `recordCount` | Integer | Number of records exported |
| `executedBy` | String | User who executed |
| `scheduleType` | String | Schedule type (Manual/Scheduled) |
| `exportType` | String | Export type (Profile/Book) |
| `format` | String | File format (CSV/TXT/XML) |
| `compressionType` | String | Compression (NONE/ZIP/GZIP) |

## Export Performance Analysis

### Comprehensive Analysis

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime, timedelta

class ExportAnalyzer:
    """Analyze export execution performance"""
    
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.token = token
        sns.set_style("whitegrid")
    
    def get_execution_history(self, export_name, days=30):
        """Get export execution history"""
        
        details = get_export_execution_details(
            self.base_url, 
            self.token, 
            export_name, 
            latest_only=False
        )
        
        if not details or 'vEWVExportExecutions' not in details:
            return pd.DataFrame()
        
        df = pd.DataFrame(details['vEWVExportExecutions'])
        
        # Parse dates
        df['startDate'] = pd.to_datetime(df['startDate'])
        df['endDate'] = pd.to_datetime(df['endDate'])
        
        # Filter to recent period
        cutoff = datetime.now() - timedelta(days=days)
        df = df[df['startDate'] > cutoff]
        
        # Calculate metrics
        df['duration_seconds'] = (df['endDate'] - df['startDate']).dt.total_seconds()
        df['duration_minutes'] = df['duration_seconds'] / 60
        df['file_size_mb'] = df['outputFileSize'] / (1024 * 1024)
        df['records_per_second'] = df['recordCount'] / df['duration_seconds']
        df['mb_per_minute'] = df['file_size_mb'] / df['duration_minutes']
        
        return df
    
    def analyze_performance(self, export_name, days=30):
        """Analyze export performance metrics"""
        
        df = self.get_execution_history(export_name, days)
        
        if df.empty:
            print("No execution data available")
            return None
        
        print(f"📊 Export Performance Analysis: {export_name}")
        print("=" * 60)
        
        # Overall statistics
        stats = {
            'total_executions': len(df),
            'success_rate': (df['statusCode'] == 'S').mean() * 100,
            'avg_duration': df['duration_minutes'].mean(),
            'max_duration': df['duration_minutes'].max(),
            'min_duration': df['duration_minutes'].min(),
            'avg_file_size': df['file_size_mb'].mean(),
            'total_data_exported': df['file_size_mb'].sum(),
            'avg_records': df['recordCount'].mean(),
            'avg_throughput': df['records_per_second'].mean()
        }
        
        print(f"\n📈 Statistics (Last {days} days):")
        print(f"  Total Executions: {stats['total_executions']}")
        print(f"  Success Rate: {stats['success_rate']:.1f}%")
        print(f"  Avg Duration: {stats['avg_duration']:.2f} minutes")
        print(f"  Duration Range: {stats['min_duration']:.2f} - {stats['max_duration']:.2f} minutes")
        print(f"  Avg File Size: {stats['avg_file_size']:.2f} MB")
        print(f"  Total Data Exported: {stats['total_data_exported']:.2f} MB")
        print(f"  Avg Records: {stats['avg_records']:,.0f}")
        print(f"  Avg Throughput: {stats['avg_throughput']:,.0f} records/sec")
        
        # Trend analysis
        if len(df) > 5:
            recent = df.tail(5)
            older = df.head(5)
            
            duration_trend = (recent['duration_minutes'].mean() - older['duration_minutes'].mean()) / older['duration_minutes'].mean() * 100
            size_trend = (recent['file_size_mb'].mean() - older['file_size_mb'].mean()) / older['file_size_mb'].mean() * 100
            
            print(f"\n📉 Trends:")
            print(f"  Duration Trend: {duration_trend:+.1f}%")
            print(f"  File Size Trend: {size_trend:+.1f}%")
            
            if duration_trend > 20:
                print("  ⚠️ Export duration increasing significantly")
            if size_trend > 50:
                print("  📈 Data volume growing rapidly")
        
        return stats, df
    
    def visualize_trends(self, export_name, days=30):
        """Create visualization of export trends"""
        
        df = self.get_execution_history(export_name, days)
        
        if df.empty or len(df) < 2:
            print("Insufficient data for visualization")
            return
        
        fig, axes = plt.subplots(2, 3, figsize=(15, 10))
        fig.suptitle(f'Export Performance Dashboard: {export_name}', fontsize=16, fontweight='bold')
        
        # Duration over time
        axes[0, 0].plot(df['startDate'], df['duration_minutes'], marker='o', linewidth=2)
        axes[0, 0].set_title('Export Duration Trend')
        axes[0, 0].set_xlabel('Date')
        axes[0, 0].set_ylabel('Duration (minutes)')
        axes[0, 0].tick_params(axis='x', rotation=45)
        axes[0, 0].grid(True, alpha=0.3)
        
        # File size over time
        axes[0, 1].bar(df['startDate'], df['file_size_mb'], alpha=0.7)
        axes[0, 1].set_title('Export File Sizes')
        axes[0, 1].set_xlabel('Date')
        axes[0, 1].set_ylabel('File Size (MB)')
        axes[0, 1].tick_params(axis='x', rotation=45)
        
        # Records exported
        axes[0, 2].plot(df['startDate'], df['recordCount'], marker='s', color='green', linewidth=2)
        axes[0, 2].set_title('Records Exported')
        axes[0, 2].set_xlabel('Date')
        axes[0, 2].set_ylabel('Record Count')
        axes[0, 2].tick_params(axis='x', rotation=45)
        axes[0, 2].grid(True, alpha=0.3)
        
        # Duration distribution
        axes[1, 0].hist(df['duration_minutes'], bins=20, edgecolor='black', alpha=0.7)
        axes[1, 0].set_title('Duration Distribution')
        axes[1, 0].set_xlabel('Duration (minutes)')
        axes[1, 0].set_ylabel('Frequency')
        axes[1, 0].axvline(df['duration_minutes'].mean(), color='red', 
                          linestyle='dashed', linewidth=2, label=f'Mean: {df["duration_minutes"].mean():.2f}')
        axes[1, 0].legend()
        
        # Success rate by hour of day
        df['hour'] = df['startDate'].dt.hour
        hourly_success = df.groupby('hour')['statusCode'].apply(lambda x: (x == 'S').mean() * 100)
        axes[1, 1].bar(hourly_success.index, hourly_success.values, color='skyblue', edgecolor='navy')
        axes[1, 1].set_title('Success Rate by Hour')
        axes[1, 1].set_xlabel('Hour of Day')
        axes[1, 1].set_ylabel('Success Rate (%)')
        axes[1, 1].set_ylim([0, 105])
        
        # Throughput over time
        axes[1, 2].scatter(df['startDate'], df['records_per_second'], alpha=0.6, s=50)
        axes[1, 2].set_title('Export Throughput')
        axes[1, 2].set_xlabel('Date')
        axes[1, 2].set_ylabel('Records/Second')
        axes[1, 2].tick_params(axis='x', rotation=45)
        
        # Add trend line
        z = np.polyfit(df.index, df['records_per_second'], 1)
        p = np.poly1d(z)
        axes[1, 2].plot(df['startDate'], p(df.index), "r--", alpha=0.8, label='Trend')
        axes[1, 2].legend()
        
        plt.tight_layout()
        plt.show()
        
        # Save figure
        filename = f"export_analysis_{export_name}_{datetime.now():%Y%m%d}.png"
        fig.savefig(filename, dpi=300, bbox_inches='tight')
        print(f"\n📊 Chart saved as: {filename}")

# Usage
analyzer = ExportAnalyzer(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

stats, df = analyzer.analyze_performance("PBCS", days=30)
analyzer.visualize_trends("PBCS", days=30)
```

### Export Comparison

```python
def compare_exports(export_names, days=7):
    """Compare multiple export profiles"""
    
    comparison_data = []
    
    for export_name in export_names:
        details = get_export_execution_details(
            base_url, token, export_name, latest_only=False
        )
        
        if details and 'vEWVExportExecutions' in details:
            executions = details['vEWVExportExecutions']
            
            # Filter to recent period
            cutoff = (datetime.now() - timedelta(days=days)).isoformat()
            recent = [e for e in executions if e['startDate'] > cutoff]
            
            if recent:
                # Calculate metrics
                durations = [(datetime.fromisoformat(e['endDate'].replace('Z', '+00:00')) - 
                             datetime.fromisoformat(e['startDate'].replace('Z', '+00:00'))).total_seconds() / 60 
                            for e in recent]
                
                sizes = [e['outputFileSize'] / (1024 * 1024) for e in recent]
                success_rate = sum(1 for e in recent if e['statusCode'] == 'S') / len(recent) * 100
                
                comparison_data.append({
                    'Export': export_name,
                    'Executions': len(recent),
                    'Success Rate': f"{success_rate:.1f}%",
                    'Avg Duration': f"{sum(durations)/len(durations):.2f} min",
                    'Avg Size': f"{sum(sizes)/len(sizes):.2f} MB",
                    'Total Size': f"{sum(sizes):.2f} MB"
                })
    
    # Display comparison table
    if comparison_data:
        df = pd.DataFrame(comparison_data)
        
        print(f"\n📊 Export Comparison (Last {days} days)")
        print("=" * 80)
        print(df.to_string(index=False))
    else:
        print("No data available for comparison")

# Usage
compare_exports(["PBCS", "HFM", "ESSBASE"], days=7)
```

## Monitoring and Alerts

```python
class ExportMonitor:
    """Monitor export executions and send alerts"""
    
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.token = token
        self.alert_thresholds = {
            'duration_minutes': 30,  # Alert if export takes > 30 minutes
            'failure_count': 2,      # Alert after 2 consecutive failures
            'file_size_mb': 1000     # Alert if file > 1GB
        }
    
    def check_latest_execution(self, export_name):
        """Check latest execution for issues"""
        
        details = get_export_execution_details(
            self.base_url, self.token, export_name, latest_only=True
        )
        
        if not details or 'vEWVExportExecutions' not in details:
            return None
        
        execution = details['vEWVExportExecutions'][0]
        alerts = []
        
        # Check status
        if execution['statusCode'] != 'S':
            alerts.append({
                'level': 'ERROR',
                'message': f"Export failed: {execution.get('message', 'Unknown error')}"
            })
        
        # Check duration
        start = datetime.fromisoformat(execution['startDate'].replace('Z', '+00:00'))
        end = datetime.fromisoformat(execution['endDate'].replace('Z', '+00:00'))
        duration = (end - start).total_seconds() / 60
        
        if duration > self.alert_thresholds['duration_minutes']:
            alerts.append({
                'level': 'WARNING',
                'message': f"Export took {duration:.2f} minutes (threshold: {self.alert_thresholds['duration_minutes']})"
            })
        
        # Check file size
        file_size_mb = execution['outputFileSize'] / (1024 * 1024)
        if file_size_mb > self.alert_thresholds['file_size_mb']:
            alerts.append({
                'level': 'WARNING',
                'message': f"Large file size: {file_size_mb:.2f} MB"
            })
        
        return alerts
    
    def monitor_all_exports(self, export_names):
        """Monitor multiple exports"""
        
        print("🔍 Export Monitoring Report")
        print("=" * 60)
        print(f"Time: {datetime.now():%Y-%m-%d %H:%M:%S}")
        print("-" * 60)
        
        all_alerts = []
        
        for export_name in export_names:
            print(f"\n📋 Checking: {export_name}")
            
            alerts = self.check_latest_execution(export_name)
            
            if alerts:
                for alert in alerts:
                    icon = "❌" if alert['level'] == 'ERROR' else "⚠️"
                    print(f"  {icon} {alert['level']}: {alert['message']}")
                    all_alerts.append({
                        'export': export_name,
                        **alert
                    })
            else:
                print("  ✅ No issues detected")
        
        # Send notifications if alerts exist
        if all_alerts:
            self.send_notifications(all_alerts)
        
        return all_alerts
    
    def send_notifications(self, alerts):
        """Send alert notifications"""
        
        # Implement your notification logic here
        # (Email, Slack, Teams, etc.)
        
        error_count = sum(1 for a in alerts if a['level'] == 'ERROR')
        warning_count = sum(1 for a in alerts if a['level'] == 'WARNING')
        
        print(f"\n📧 Sending notifications: {error_count} errors, {warning_count} warnings")

# Usage
monitor = ExportMonitor(
    base_url="https://demo.epmwarecloud.com",
    token="your-token-here"
)

alerts = monitor.monitor_all_exports(["PBCS", "HFM", "PLANNING"])
```

## Best Practices

### 1. Regular Health Checks

```python
def export_health_check(export_name, days=7):
    """Perform health check on export profile"""
    
    analyzer = ExportAnalyzer(base_url, token)
    stats, df = analyzer.analyze_performance(export_name, days)
    
    health_score = 100
    issues = []
    
    if stats:
        # Check success rate
        if stats['success_rate'] < 95:
            health_score -= 20
            issues.append(f"Low success rate: {stats['success_rate']:.1f}%")
        
        # Check duration trend
        if len(df) > 5:
            recent_duration = df.tail(3)['duration_minutes'].mean()
            overall_avg = df['duration_minutes'].mean()
            
            if recent_duration > overall_avg * 1.5:
                health_score -= 15
                issues.append("Performance degradation detected")
        
        # Check file sizes
        if stats['avg_file_size'] > 500:  # MB
            health_score -= 10
            issues.append(f"Large file sizes: {stats['avg_file_size']:.2f} MB average")
    
    print(f"\n🏥 Health Check: {export_name}")
    print(f"  Score: {health_score}/100")
    
    if issues:
        print("  Issues:")
        for issue in issues:
            print(f"    - {issue}")
    else:
        print("  ✅ No issues detected")
    
    return health_score, issues
```

### 2. Capacity Planning

```python
def export_capacity_forecast(export_name, forecast_days=30):
    """Forecast future export capacity needs"""
    
    df = get_execution_history(export_name, days=90)
    
    if len(df) < 10:
        print("Insufficient data for forecasting")
        return
    
    # Trend analysis
    df['day_number'] = (df['startDate'] - df['startDate'].min()).dt.days
    
    # Linear regression for file size
    from scipy import stats
    slope, intercept, r_value, p_value, std_err = stats.linregress(
        df['day_number'], df['file_size_mb']
    )
    
    # Forecast
    future_day = df['day_number'].max() + forecast_days
    predicted_size = slope * future_day + intercept
    
    current_avg = df['file_size_mb'].mean()
    growth_rate = (predicted_size - current_avg) / current_avg * 100
    
    print(f"\n📈 Capacity Forecast: {export_name}")
    print(f"  Current Avg Size: {current_avg:.2f} MB")
    print(f"  Predicted Size ({forecast_days} days): {predicted_size:.2f} MB")
    print(f"  Growth Rate: {growth_rate:.1f}%")
    
    if growth_rate > 50:
        print("  ⚠️ Significant growth expected - plan for additional capacity")
```

## Related Operations

- [Run Export](run-export.md) - Execute exports
- [Download File](download-file.md) - Download export files
- [Get Task Status](../task/get-status.md) - Monitor export progress

## Next Steps

After retrieving execution details:
1. Analyze performance trends
2. Set up monitoring alerts
3. Optimize export schedules
4. Plan capacity requirements