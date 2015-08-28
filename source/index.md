---
title: Wia Documentation

language_tabs:
  - shell: curl
  - javascript: Node

toc_footers:
  - <a href='https://www.wia.io/requestInvite'>Request an Invite to Wia</a>
  - <a href='mailto:team@wia.io'>Or say hello at team@wia.io</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Wia Documentation! You can use our API to access Wia API endpoints, which can get information on devices, events and users.

We have client libraries in javascript (more coming soon!). You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

> Official libraries

```
Node.js - https://github.com/wiaio/wia/wia-nodejs-sdk
```

```
Objective C - https://github.com/wiaio/wia/wia-objective-c-sdk
```


> API Endpoint

```
https://api.wia.io
```

# Objects

Wia data are structured around 2 main types of objects: Device and Event. You’ll find these objects in the format described below.

## Device

> Example of a Device object

```
{
	"deviceKey": "sdf892mdfgj238kdfg",
	"name": "Device One",
	"isOnline": true,
	"createdAt": "2015-01-15 09:30:25"
}
```

Parameter | Type | Description
--------- | ----------- | -----------
deviceKey | String | Unique key of the device
name | String | Name of the device
isOnline | Boolean | Online status of the device
createdAt | Timestamp | Timestamp of when device was created (optional)


## Event

> Example of an Event object

```
{
	"name": "EventName",
	"data": {
		"key": "value"
	},
	"timestamp": 1440597871
}
```

Parameter | Type | Description
--------- | ----------- | -----------
name | String | Name of the event
data | Object | Event data
timestamp | Timestamp | Timestamp of the event (optional)


### Sensor

> Example of a Sensor Event object

```
{
	"name": "Sensor",
	"data": {
		"objectTemperature": 17.5,
		"ambientTemperature": 4.2,
		"absoluteHumidity": 17.6,
		"relativeHumidity": 12.4,
		"specificHumidity": 7.8,
		"gyroscope": {
			"x": 0.342,
			"y": 0.765,
			"z": 0.634
		},
		"accelerometer": {
			"x": 0.342,
			"y": 0.765,
			"z": 0.634
		},
		"pressure": 7.8,
		"magnetometer": {
			"x": 0.342,
			"y": 0.765,
			"z": 0.634
		},
		"gravity": {
			"x": 0.342,
			"y": 0.765,
			"z": 0.634
		},
		"light": 7.8,
		"proximity": 7.8,
		"heartRate": 7.8,
		"orientation": {
			"x": 0.342,
			"y": 0.765,
			"z": 0.634
		}
	},
	"timestamp": 1440597871
}
```

List of permitted data key-value pairs for a Sensor event.

Parameter | Type | Unit | Description
--------- | ---- | ---- | -----------
objectTemperature | Number | Celsius | Temperature of object.
ambientTemperature (or temperature) | Number | Celsius | Ambient temperature.
absoluteHumidity | Number | Percentage | Absolute humidity.
relativeHumidity (or humidity) | Number | Percentage | Relative humidity.
specificHumidity | Number | Percentage | Specific humidity.
gyroscope | Object | Radian per second (rad/s) | Gyroscope. Use keys x, y and z.
accelerometer | Object | Metre per second squared (m/s2) | Accelerometer. Use keys x, y and z.
pressure | Number | Millibar (hPa) | Air pressure.
magnetometer | Object | Microtesla (μT) | Magnetometer. Use keys x, y and z.
gravity | Object | Metre per second squared (m/s2) | Gravity. Use keys x, y and z.
light | Number | Lux (lx) | Light.
proximity | Number | Centimeters (cm) | Proximity.
heartRate | Number | Beat per minute (BPM) Heart rate.
orientation | Object | Degrees (°) | Orientation. Use keys x, y and z.


### Location

> Example of a Location Event object

```
{
	"name": "Location",
	"data": {
		"latitude": 54.60247,
		"longitude": -5.92717
	},
	"timestamp": 1440597871
}
```

List of permitted data key-value pairs for a Location event.

Parameter | Type | Description
--------- | ----------- | -----------
latitude | Number | Latitude of the location.
longitude | Number | Longitude of the location.


# Authentication

> To authorize a user, use this code:

```shell
curl https://api.wia.io/v1 \
	-H "Authorization: Bearer u_jsdf812jkdf01kdf"

```

```javascript
var userClient = require('wia-sdk')('u_jsdf812jkdf01kdf');

```

> To authorize a device, use this code:

```shell
curl https://api.wia.io/v1 \
	-H "Authorization: Bearer d_jsdf812jkdf01kdf"

```

```javascript
var deviceClient = require('wia-sdk')('d_jsdf812jkdf01kdf');

```

Wia uses access tokens to allow access to the API. When you create an account or a device, an access token is automatically generated.

You can find your user access token in My Account and your device access token in the device's settings.

Wia expects the access token to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer u_ksdf912jnmdfg9k123lk90`

<aside class="warning">Some requests are user or device specific. If you are not using the correct type of token, you will get a 401 Unauthorized response.</aside>


# Devices

## Get Devices

```shell
shell "https://api.wia.io/v1/devices?limit=20"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var userClient = require('wia-sdk')('u_8jdflsdf912kasdf2dffg');

userClient.getDevices(
	{ limit: 20 },
	function(err, devices) {
		// asynchronously called
	}
);

```

> The above command returns JSON structured like this:

```json
[
  {
    "deviceKey": "Jas8snj1msdf89k83jdf",
    "name": "Device One",
    "online": true
  },
  {
    "deviceKey": "Jas8snj1msdf89k83jdf",
    "name": "Device Two",
    "online": false
  }
]
```

This endpoint retrieves devices. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/devices`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
limit | 20 | Number of devices to return. Max value 200.
page | 0 | First page is 0.
order | name | Field to sort by. Valid values include name and lastUpdated.
sort | asc | Either ascending (asc) or descending (desc).
isOnline | No Default | If set to false, the device is not online. When a device is online, this is true.


## Get a Specific Device

```shell
shell "https://api.wia.io/v1/devices/jasAj09df9mmdfgh19ldf"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var userClient = require('wia-sdk')('u_8jdflsdf912kasdf2dffg');

userClient.getDevice(
	"jnasdf892knsdfolsd" ,
	function(err, device) {
		// asynchronously called
	}
);

```

> The above command returns JSON structured like this:

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"online": true
}
```

This endpoint retrieves a specific device. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/devices/:deviceKey`

### URL Parameters

Parameter | Description
--------- | -----------
deviceKey | The key of the device to retrieve.


## Get Current Device

```shell
shell "https://api.wia.io/v1/devices?limit=20"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var deviceClient = require('wia-sdk')('d_8jdflsdf912kasdf2dffg');

deviceClient.getDeviceMe(
	function(err, devices) {
		// asynchronously called
	}
);

```

> The above command returns JSON structured like this:

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"online": true
}
```

This endpoint retrieves the current device. Requires a Device token.

### HTTP Request

`GET https://api.wia.io/v1/devices/me`


## Create a Device

```shell
shell "https://api.wia.io/v1/devices?limit=20"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var userClient = require('wia-sdk')('u_8jdflsdf912kasdf2dffg');

userClient.createDevice(
	{ name: "Device Name" },
	function(err, device) {
		// asynchronously called
	}
);

```

> The above command returns JSON structured like this:

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"online": true
}
```

This endpoint creates a device. Requires a User token.

### HTTP Request

`POST https://api.wia.io/v1/devices`

### Attributes

Parameter | Description
--------- | -----------
name | Name of device to be created.


## Delete a Device

```shell
shell "https://api.wia.io/v1/devices?limit=20"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var userClient = require('wia-sdk')('u_8jdflsdf912kasdf2dffg');

userClient.deleteDevice(
	"asd9mnSL9mdf01m",
	function(err, device) {
		// asynchronously called
	}
);

```

> The above command returns JSON structured like this:

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"online": true
}
```

This endpoint deletes a device. Requires a User token.

### HTTP Request

`DELETE https://api.wia.io/v1/devices/:deviceKey`

### URL Parameters

Parameter | Description
--------- | -----------
deviceKey | Key of device to be deleted.


## Get Device Events

```shell
shell "https://api.wia.io/v1/devices?limit=20"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var userClient = require('wia-sdk')('u_8jdflsdf912kasdf2dffg');

userClient.getDeviceEvents(
	"d_9mdflg982jdmdfglw89dfgn",
	{ limit: 20 },
	function(err, events) {
		// asynchronously called
	}
);

```

> The above command returns JSON structured like this:

```json
[
  {
    "name": "Sensor",
    "data": {
		"temperature": 14.5,
		"humidity": 56.3
	},
	"timestamp": 1440683447
  },
  {
    "name": "Location",
    "data": {
		"latitude": 54.60247,
		"longitude": -5.92717
	},
	"timestamp": 1440683234
  }
]
```

This endpoint retrieves a list of events for a device. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/devices/:deviceKey/events`

### URL Parameters

Parameter | Description
--------- | -----------
deviceKey | Key of device to get events for.

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
limit | 20 | Number of devices to return. Max value 200.
page | 0 | First page is 0.
order | name | Field to sort by. Valid values include name and lastUpdated.
sort | asc | Either ascending (asc) or descending (desc).

# Ping

```shell
curl http://localhost:8080/v1/ping \
	-H "Authorization: Bearer d_bzw35dvId2x4Esf23sdgf3fgOUdp16ysUqoig"
```

```javascript
var deviceClient = require('wia-sdk')('d_bzw35dvId2x4Esf23sdgf3fgOUdp16ysUqoig');

userClient.ping(
	function(err) {
		// asynchronously called
	}
);

```

This endpoint allows a device to let the service know it is online. It is not required to use this method when using a stream client. Requires a Device token.

### HTTP Request

`GET https://api.wia.io/v1/ping` or
`POST https://api.wia.io/v1/ping`

