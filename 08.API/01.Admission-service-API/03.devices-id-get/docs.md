---
title: GET /devices/{id}
taxonomy:
    category: docs
---

<a name="devices-id-get"></a>
### GET /devices/{id}

#### Description
Gets device by ID


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Path**|**id**  <br>*required*|Device identifier|string||


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Device|[Device](#device)|
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