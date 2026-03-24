# Get Export Execution Details

This REST API will allow users to download Execution details for a given export name.


## Endpoint Details

### URL Structure

```
http(s)://<EPMWARE_URL>/service/api/export/get_execution_details
```


### Method

`GET`

## Parameters

| Parameter  | Required | Default | Description |
|-----------|----------|---------|-------------|
| `name`  | Yes | - | Export profile name |
| `latest_only`  | No | Y | Return only latest execution (Y/N) |

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

Windows Powershell Script Example

```Powershell

##########################################################################
# EPMWware Inc.
# Author: Deven Shah
# Date: 11-Sep-2021
# Purpose: REST API to get Export execution details.
##########################################################################

Param(
      [Parameter(Mandatory=$True)]
      [string]$url,
      [Parameter(Mandatory=$True)]
      [string]$token,
      [Parameter(Mandatory=$True)]
      [string]$exportProfileName,
      [Parameter(Mandatory=$True)]
      [string]$latest_only
     )

$module = 'export' 
$headers = @{"authorization"="Token $($token)"}
function log()
{
  Write-Host $args[0]
}
log "====================================================="
log "Parameters"
log "====================================================="
log "EPMWARE REST API URL  : $url"
log "User Token            : *******"
log "Export Profile Name   : $exportProfileName"
log "Latest Execution only : $latest_only"
log "====================================================="

##########################################################################
# Get Export Execution Details 
##########################################################################
# http://EPMWARE1.EPMWARE.com:8080/EPMWARE/service/api/export/get_execution_details?name=PBCS&latest_only=N  
try {
     $taskURL="$url/$module/get_execution_details?name=$exportProfileName&latest_only=$latest_only"
     $restResponse = Invoke-RestMethod -Uri $taskURL -Method Get -Headers $headers
     
     foreach ($record in $restResponse.vEWVExportExecutions) 
     {
        $exportName=$record.exportName
        $execId=$record.id
        $startDate=$record.startDate
        $endDate=$record.endDate
        $outputFileName=$record.outputFileName
        $outputFileSize=$record.outputFileSize
        $statusMeaning=$record.statusMeaning
        $message=$record.message
        
        log "Execution ID:$execId Status:$statusMeaning StartDate:$startDate EndDate:$endDate OutputFileName:$outputFileName OutputFileSize:$outputFileSize Message:$message"
     }
     
     
    } catch {
        log "EW_ERROR: EPMWARE REST API to get Export Execution Details failed. $details"
        $exceptionDetails = $_.Exception
        log $exceptionDetails
        exit
    }

log "================"
log "Script completed"



Call this Powershell script ( example ew_exp_get_details.ps1) file with parameters

powershell %CD%\ew_exp_get_details.ps1 -url "%URL%" -token %TOKEN% -exportProfileName "PBCS"  -latest_only "Y"

Replace URL and TOKEN parameters.



```


## Response Format

### Successful Response

```json
{
    "startIndex": "0",
    "pageSize": "0",
    "totalCount": "9",
    "resultSize": "0",
    "queryTimeMS": "1774295912519",
    "vEWVExportExecutions": [
        {
            "createTime": "2026-03-04T02:52:35Z",
            "updateTime": "2026-03-04T02:52:35Z",
            "createdBy": "41",
            "updatedBy": "41",
            "id": "2825",
            "expConfigId": "1707",
            "exportName": "ASO",
            "status": "S",
            "message": "Export Complete",
            "startDate": "2026-03-04T02:52:35Z",
            "endDate": "2026-03-04T02:52:35Z",
            "outputFileName": "ASO_2825_04_MAR_2026_02_52.csv",
            "userName": "DEVEN",
            "lastName": "shah",
            "firstName": "Deven",
            "name": "ASO",
            "statusMeaning": "Success",
            "outputFileSize": "8769",
            "taskId": "13436"
        }
    ]
}
```



## Related Operations

- [Run Export](run-export.md) - Execute exports
- [Download File](download-file.md) - Download export files
- [Get Task Status](../task/get-status.md) - Monitor export progress

