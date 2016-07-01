---
title: POST /devices
taxonomy:
    category: docs
---

<a name="devices-post"></a>
### POST /devices

#### Description
Adds new device for admission


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Body**|**Device**  <br>*required*|New device for admission|[NewDevice](#newdevice)||


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**201**|New device for admission created  <br>**Headers** :   <br>`Location` (string) : URI for the new created resource.|No Content|
|**400**|Invalid request|[SimpleError](#simpleerror)|
|**500**|Internal Server Error|[SimpleError](#simpleerror)|


#### Example HTTP response

##### Response 500
```
json :
{
  "application/json" : {
    "error" : "Detailed error message"
  }
}
```

