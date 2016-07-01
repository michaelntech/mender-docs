---
title: PUT /devices/{id}/status
taxonomy:
    category: docs
---

<a name="devices-id-status-put"></a>
### PUT /devices/{id}/status

#### Description
Update device admission state


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Path**|**id**  <br>*required*|Device identifier|string||
|**Body**|**status**  <br>*required*|New status|[Status](#status)||


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**303**|Device updated  <br>**Headers** :   <br>`Location` (string) : URI of updated device.|No Content|
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
