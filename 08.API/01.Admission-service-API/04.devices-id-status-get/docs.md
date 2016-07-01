---
title: GET /devices/{id}/status
taxonomy:
    category: docs
---

<a name="devices-id-status-get"></a>
### GET /devices/{id}/status

#### Description
Checks admission status for the device


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Path**|**id**  <br>*required*|Device identifier|string||


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Admission status of the device|[Status](#status)|
|**404**|Not Found|[SimpleError](#simpleerror)|
|**500**|Internal Server Error|[SimpleError](#simpleerror)|


#### Example HTTP response

##### Response 404
```
json :
{
  "application/json" : {
    "error" : "Detailed error message"
  }
}
```


##### Response 500
```
json :
{
  "application/json" : {
    "error" : "Detailed error message"
  }
}
```