# PowerShell Scripts

Windows PowerShell automation scripts for EPMware REST API.

## Complete PowerShell Module

```powershell
# EPMware-API.psm1
# PowerShell module for EPMware REST API

$Script:BaseUrl = $null
$Script:Token = $null

function Connect-EPMware {
    param(
        [Parameter(Mandatory=$true)]
        [string]$Url,
        
        [Parameter(Mandatory=$true)]
        [string]$ApiToken
    )
    
    $Script:BaseUrl = $Url
    $Script:Token = $ApiToken
    
    # Test connection
    $testUrl = "$Url/service/api/security/users"
    $headers = @{"Authorization"="Token $ApiToken"}
    
    try {
        $response = Invoke-RestMethod -Uri $testUrl -Headers $headers -Method Get
        Write-Host "Connected to EPMware successfully" -ForegroundColor Green
        return $true
    } catch {
        Write-Error "Failed to connect: $_"
        return $false
    }
}

function Get-EPMwareTaskStatus {
    param(
        [Parameter(Mandatory=$true)]
        [string]$TaskId
    )
    
    $url = "$Script:BaseUrl/service/api/task/get_status/$TaskId"
    $headers = @{"Authorization"="Token $Script:Token"}
    
    $response = Invoke-RestMethod -Uri $url -Headers $headers -Method Get
    return $response
}

function Start-EPMwareImport {
    param(
        [Parameter(Mandatory=$true)]
        [string]$ProfileName,
        
        [hashtable]$Parameters = @{}
    )
    
    $url = "$Script:BaseUrl/service/api/erp/run"
    $headers = @{
        "Authorization"="Token $Script:Token"
        "Content-Type"="application/json"
    }
    
    $body = @{
        profileName = $ProfileName
        parameters = $Parameters
    } | ConvertTo-Json
    
    $response = Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body
    return $response
}

function Wait-EPMwareTask {
    param(
        [Parameter(Mandatory=$true)]
        [string]$TaskId,
        
        [int]$TimeoutSeconds = 3600,
        [int]$CheckInterval = 5
    )
    
    $startTime = Get-Date
    
    while ((Get-Date) -lt $startTime.AddSeconds($TimeoutSeconds)) {
        $status = Get-EPMwareTaskStatus -TaskId $TaskId
        
        Write-Progress -Activity "Waiting for task $TaskId" `
                      -Status "$($status.status)" `
                      -PercentComplete $status.percentCompleted
        
        if ($status.status -in @('S', 'E', 'W', 'C')) {
            Write-Progress -Activity "Task $TaskId" -Completed
            return $status
        }
        
        Start-Sleep -Seconds $CheckInterval
    }
    
    throw "Task $TaskId timed out after $TimeoutSeconds seconds"
}

# Export functions
Export-ModuleMember -Function Connect-EPMware, Get-EPMwareTaskStatus, 
                              Start-EPMwareImport, Wait-EPMwareTask
```

## Usage Example

```powershell
# Import module
Import-Module .\EPMware-API.psm1

# Connect
Connect-EPMware -Url "https://demo.epmwarecloud.com" -ApiToken "your-token"

# Run import
$result = Start-EPMwareImport -ProfileName "GL_IMPORT" -Parameters @{
    period = "Jan-2025"
    entity = "US001"
}

# Wait for completion
$finalStatus = Wait-EPMwareTask -TaskId $result.taskId

if ($finalStatus.status -eq 'S') {
    Write-Host "Import completed successfully" -ForegroundColor Green
} else {
    Write-Host "Import failed: $($finalStatus.message)" -ForegroundColor Red
}
```

---

[← Back to Examples](../) | [Python Examples →](../python-examples/)
