# Run Export

This action allows user to run Export task on demand.
Export Profile/ Export Book specified in the REST API must be configured in advance before calling this API.

## Endpoint Details


### URL Structure

```http
http(s)://<EPMWARE_URL>/service/api/export/run
```

### Method

`POST`

## Parameters

If Export profile needs to be executed use below parameter: 

| Parameter | Required | Description |
|-----------|----------|-------------|
| `name`    | Yes | Export profile or book name |


If Export Book needs to be executed use below parameter: 

| Parameter | Required | Description |
|-----------|-- -------|-------------|
| `book_name` | Yes | Export profile or book name |

This call will return TASK_ID in return. User will use this TASK_ID to probe its status.

## Request Example

To Execute export profile :

```bash

curl POST 'http://demo.EPMWAREcloud.com/service/api/export/run' \
 -H 'authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
 -H 'content-type: application/json' \
 -d '{"name" : "PBCS"}'

```


To Execute export book :

```bash

curl POST 'http://demo.EPMWAREcloud.com/service/api/export/run' \
 -H 'authorization: Token 15388ad5-c9af-4cf3-af47-8021c1ab3fb7' \
 -H 'content-type: application/json' \
 -d '{"book_name" : "PBCS_BOOK"}'

```

## Response Format

In JSON Format

```json
{
 "status":"S",
 "taskId":"244596"
}
```


---

[← Back to Export Module](../) | [Download File](../download-file/)
