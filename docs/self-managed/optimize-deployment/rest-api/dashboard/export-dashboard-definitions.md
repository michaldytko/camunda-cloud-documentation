---
id: export-dashboard-definitions
title: "Export dashboard definitions"
description: "The REST API to export dashboard definitions."
---

## Purpose

This API allows users to export dashboard definitions which can later be imported into another Optimize system. Note that exporting a dashboard also exports all reports contained within the dashboard. The dashboards to be exported may be within a Collection or private entities, the API has access to both.

The obtained list of entity exports can be imported into other Optimize systems either using the dedicated [import API](../../import-entities) or [via UI](../../../../../components/optimize/userguide/additional-features/export-import#importing-entities).

## Method & HTTP target resource

POST `/api/public/export/dashboard/definition/json`

## Request headers

The following request headers have to be provided with every request:

|Header|Constraints|Value|
|--- |--- |--- |
|Authorization|REQUIRED|[Authorization](../../authorization)|

## Query parameters

No query parameters available.

## Request body

The request body should contain a JSON array of dashboard IDs to be exported.

## Result

The response contains a list of exported dashboard definitions as well as all report definitions contained within the dashboards.

## Response codes

Possible HTTP response status codes:

|Code|Description|
|--- |--- |
|204|Request successful.|
|401|Secret incorrect or missing in HTTP Header. See [Authorization](../../authorization) on how to authenticate.|
|404|At least one of the given dashboard IDs does not exist.|
|500|Some error occurred while processing the request, best check the Optimize log.|

## Example

### Export two dashboards

Assuming you want to export the two dashboards with IDs `123` and `456` and have configured the accessToken `mySecret`, this is what it would look like:

POST `/api/public/export/dashboard/definition/json`

#### Request header

`Authorization: Bearer mySecret`

#### Request body

```
[ "123", "456" ]
```

#### Response

Status 200.

#### Response content

The response contains the two exported dashboard definitions as well as all three process reports contained within the two dashboards.

```
[
    {
        "id": "61ae2232-51e1-4c35-b72c-c7152ba264f9",
        "exportEntityType": "single_process_report",
        "name": "Number: Process instance duration",
        "sourceIndexVersion": 8,
        "collectionId": null,
        "data": {...}
    },
    {
        "id": "625c2411-b95f-4442-936b-1976b9511d4a",
        "exportEntityType": "single_process_report",
        "name": "Heatmap: Flownode count",
        "sourceIndexVersion": 8,
        "collectionId": null,
        "data": {...}
    },
    {
        "id": "94a7252e-d5c3-45ea-9906-75271cc0cac2",
        "exportEntityType": "single_process_report",
        "name": "Data Table: User task count",
        "sourceIndexVersion": 8,
        "collectionId": null,
        "data": {...}
    },
    {
        "id": "123",
        "exportEntityType": "dashboard",
        "name": "Dashboard 1",
        "sourceIndexVersion": 5,
        "reports": [
            {
                "id": "61ae2232-51e1-4c35-b72c-c7152ba264f9",
                ...
            },
            {
                "id": "625c2411-b95f-4442-936b-1976b9511d4a",
                ...
            }
        ],
        "availableFilters": [...],
        "collectionId": null
    },
    {
        "id": "456",
        "exportEntityType": "dashboard",
        "name": "Dashboard 2",
        "sourceIndexVersion": 5,
        "reports": [
            {
                "id": "94a7252e-d5c3-45ea-9906-75271cc0cac2",
                ...
            }
        ],
        "availableFilters": [...],
        "collectionId": null
    }
]
```
