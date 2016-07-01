---
title: Device admission service
taxonomy:
    category: docs
---

<a name="overview"></a>
## Overview
*Version* : 0.1.0

*BasePath* : /api/0.1.0

Device admission service. More info text about device admission.

<a name="paths"></a>
### Paths
- [POST /devices](#devices-post)
- [GET /devices](#devices-get)
- [GET /devices/{id}](#devices-id-get)
- [GET /devices/{id}/status](#devices-id-status-get)
- [PUT /devices/{id}/status](#devices-id-status-put)

___


<a name="devices-post"></a>
### POST /devices

Adds new device for admission.

Example: `POST /api/0.1.0/devices`

#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Body**|**Device**  <br>*required*|New device for admission|[NewDevice](#newdevice)| |


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**201**|New device for admission created  <br>**Headers** :   <br>`Location` (string) : URI for the new created resource.|No Content|
|**400**|Invalid request|[SimpleError](#simpleerror)|
|**500**|Internal Server Error|[SimpleError](#simpleerror)|


#### Example HTTP request

##### Request body
```
json :
{
  "application/json" : {
    "id" : "00a0c91e6-7dec-11d0-a765-f81d4faebf1",
    "device_identity" : "{\"SN\":\"131313131\", \"cpuid\":\"12331-ABC\", \"mac\":\"00:11:22:33:44:55\"}",
    "key" : "5f36d271484c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a21"
  }
}
```


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

___
<a name="devices-get"></a>
### GET /devices

Gets 'Device' objects.

Example: `GET /api/0.1.0/devices?page=1&per_page=20&status=accepted`

#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Query**|**page**  <br>*optional*|Starting page|number(integer)|`"1"`|
|**Query**|**per_page**  <br>*optional*|Number of results per page|number(integer)|`"10"`|
|**Query**|**status**  <br>*optional*|Filters devices by admission status (pending, accepted, rejected)|enum (pending, accepted, rejected)| |


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
    "id" : "db1a77019-af2e-103d-baac-8d1e740da98",
    "device_identity" : "{\"SN\":\"131313131\", \"cpuid\":\"12331-ABC\", \"mac\":\"00:11:22:33:44:55\"}",
    "key" : "5f36d271484c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a21",
    "status" : "accepted",
    "attributes" : {
      "SN" : "131313131",
      "cpuid" : "12331-ABC",
      "mac" : "00:11:22:33:44:56"
    }
  }, {
    "id" : "00a0c91e6-7dec-11d0-a765-f81d4faebf1",
    "device_identity" : "{\"SN\":\"131313131\", \"cpuid\":\"12331-ABC\", \"mac\":\"00:11:22:33:44:55\"}",
    "key" : "5f36d271484c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a21",
    "status" : "accepted",
    "attributes" : {
      "SN" : "131313131",
      "cpuid" : "12331-ABC",
      "mac" : "00:11:22:33:44:56"
    }
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

___
<a name="devices-id-get"></a>
### GET /devices/{id}

Gets device.

Example: `GET /api/0.1.0/devices/00a0c91e6-7dec-11d0-a765-f81d4faebf1`

#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Path**|**id**  <br>*required*|Device identifier|string| |


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

___
<a name="devices-id-status-get"></a>
### GET /devices/{id}/status

Checks admission status for the device.

Example: `GET /devices/3d98um-nxxdd3-3te223g-23r4/status`
#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Path**|**id**  <br>*required*|Device identifier|string| |


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Admission status of the device|[Status](#status)|
|**404**|Not Found|[SimpleError](#simpleerror)|
|**500**|Internal Server Error|[SimpleError](#simpleerror)|


#### Example HTTP response

##### Response 200
```
json :
{
  "application/json" : {
    "status" : "accepted"
  }
}
```


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

___
<a name="devices-id-status-put"></a>
### PUT /devices/{id}/status

Update device admission state.

Example: `PUT devices/00a0c91e6-7dec-11d0-a765-f81d4faebf1/status`


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Path**|**id**  <br>*required*|Device identifier|string| |
|**Body**|**status**  <br>*required*|New status|[Status](#status)| |


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**303**|Device updated  <br>**Headers** :   <br>`Location` (string) : URI of updated device.|No Content|
|**404**|Not Found|[SimpleError](#simpleerror)|
|**500**|Internal Server Error|[SimpleError](#simpleerror)|



#### Example HTTP request

##### Request body
```
json :
{
  "application/json" : {
    "status" : "accepted"
  }
}
```


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


___


<a name="definitions"></a>
## Definitions

<a name="attributes"></a>
### Attributes
Human readable attributes of the device.
Attributes can have more or different properties then defined here.


|Name|Description|Schema|
|---|---|---|
|**SKU**  <br>*optional*|Stock keeping unit  <br>**Example** : `"My Device 1"`|string|
|**SN**  <br>*optional*|Serial Number  <br>**Example** : `"13132313"`|string|
|**mac_address**  <br>*optional*|Device MAC address  <br>**Example** : `"aa:bb:cc:dd:00:11"`|string|


<a name="device"></a>
### Device
Device


|Name|Description|Schema|
|---|---|---|
|**attributes**  <br>*required*| |[Attributes](#attributes)|
|**device_identity**  <br>*optional*|Identity data|string|
|**id**  <br>*required*|Hash created based on the device identity data|string|
|**key**  <br>*optional*|Device public key|string|
|**status**  <br>*required*|Status of the admission process for the device|enum (pending, accepted, rejected)|


<a name="newdevice"></a>
### NewDevice
New device for admission process


|Name|Description|Schema|
|---|---|---|
|**device_identity**  <br>*required*|Device identity data|string|
|**id**  <br>*required*|Hash created based on the device identity data|string|
|**key**  <br>*required*|Device public key|string|


<a name="simpleerror"></a>
### SimpleError
Simple error descriptor


|Name|Description|Schema|
|---|---|---|
|**error**  <br>*optional*|Description of error|string|


<a name="status"></a>
### Status
Admission status of the device


|Name|Description|Schema|
|---|---|---|
|**status**  <br>*required*| |enum (pending, accepted, rejected)|




