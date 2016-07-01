---
title: GET /devices
taxonomy:
    category: docs
---

<a name="devices-get"></a>
### GET /devices

#### Description
Gets `Device` objects.


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Query**|**page**  <br>*optional*|Starting page|number(integer)|`"1"`|
|**Query**|**per_page**  <br>*optional*|Number of results per page|number(integer)|`"10"`|
|**Query**|**status**  <br>*optional*|Filters devices by admission status (pending, accepted, rejected)|enum (pending, accepted, rejected)||


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Successful response  <br>**Headers** :   <br>`Link` (string) : Standard header, we support 'first', 'next', and 'prev'.|< [Device](#device) > array|
|**202**|No Content|No Content|
|**400**|Invalid parameters|[SimpleError](#simpleerror)|
|**500**|Internal Server Error|[SimpleError](#simpleerror)|


#### Example HTTP response

##### Response 200
```
json :
{
  "application/json" : [ {
    "id" : "db1a77019af2e103dbaac8d1e740da98",
    "device_identity" : "{\"SN\":\"131313131\", \"cpuid\":\"12331-ABC\", \"mac\":\"00:11:22:33:44:55\"}",
    "key" : "5f36d271484c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a21",
    "status" : "accepted",
    "attributes" : [ {
      "SN" : "131313131"
    }, {
      "cpuid" : "12331-ABC"
    }, {
      "mac" : "00:11:22:33:44:56"
    } ]
  } ]
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