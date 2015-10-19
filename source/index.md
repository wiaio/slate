---
title: Wia Documentation

language_tabs:
  - shell: curl
  - javascript: Node.js

toc_footers:
  - <a href='https://www.wia.io/signup' target="_blank">Signup for Wia</a>
  - <a href='http://twitter.com/wiaio' target="_blank">Follow us @wiaio</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Wia Documentation! You can use our API to access Wia API endpoints, which can get information on devices, events and users.

We have client libraries in Node.js and Objective-C. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

> Official libraries

```
Node.js - https://github.com/wiaio/wia-nodejs-sdk
```

> REST API Endpoint

```
https://api.wia.io
```

> MQTT Endpoint

```
mqtt://mqtt.wia.io (ports 1883 or 8883)
```

# Objects

Wia data are structured around objects. You’ll find these objects in the format described below.

## Device

> Example of a Device object

```
{
	"deviceKey": "sdf892mdfgj238kdfg",
	"name": "Device One",
	"isOnline": true,
	"events": {
	    "sensor": {
	        "timestamp": 1444995825836,
	        "ambientTemperature": {
	            "value": 19.7867,
	            "timestamp": 1444995825836
	        },
	        "light": {
	            "value": 123.65,
	            "timestamp": 1444995825836
	        }
	    }
	},
	"createdAt": "1445198559000",
	"updatedAt": "1445198559000"
}
```

All devices are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
deviceKey | String | Unique key of the device
name | String | Name of the device
events | Object | List of most recent events
isOnline | Boolean | Online status of the device
createdAt | Timestamp | Timestamp of when device was created
updatedAt | Timestamp | Timestamp of when device was updated


## Event

> Example of an Event object

```
{
	"name": "eventName",
	"data": {
		"key": "value"
	},
	"timestamp": 1440597871
}
```

All events are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
name | String | Name of the event. Alphanumeric characters only. We recommend using camel case with the first letter being lower case (e.g. thisIsMyFirstEvent).
data | Object | Event data object
timestamp | Timestamp | Timestamp of the event in milliseconds


### Reserved Events
A number of event names are reserved. This means that they must use particular data type names and only those names will be supported.

At the moment this is just for "Sensor" and "Location" events.

#### Sensor

> Example of a Sensor Event object

```
{
	"name": "sensor",
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
	"timestamp": 1440597871675
}
```

List of permitted data key-value pairs for a Sensor event.

Parameter | Type | Unit | Description
--------- | ---- | ---- | -----------
objectTemperature | Number | Celsius | Temperature of object.
ambientTemperature | Number | Celsius | Ambient temperature.
absoluteHumidity | Number | Percentage | Absolute humidity.
relativeHumidity | Number | Percentage | Relative humidity.
specificHumidity | Number | Percentage | Specific humidity.
gyroscope | Object | Radian per second (rad/s) | Gyroscope. Use keys x, y and z.
accelerometer | Object | Metre per second squared (m/s2) | Accelerometer. Use keys x, y and z.
pressure | Number | Millibar (hPa) | Air pressure.
magnetometer | Object | Microtesla (μT) | Magnetometer. Use keys x, y and z.
gravity | Object | Metre per second squared (m/s2) | Gravity. Use keys x, y and z.
light | Number | Lux (lx) | Light.
proximity | Number | Centimeters (cm) | Proximity.
heartRate | Number | Beat per minute (BPM) | Heart rate.
orientation | Object | Degrees (°) | Orientation. Use keys x, y and z.

#### Location

> Example of a Location Event object

```
{
	"name": "location",
	"data": {
		"latitude": 54.60247,
	    "longitude": -5.92717,
	    "altitude": 30,
	    "speed": 29.5,
	    "heading": 94,
	    "climb": 3
	},
	"timestamp": 1440597871546
}
```

List of permitted data key-value pairs for a Location event.

Parameter | Type | Unit | Description
--------- | ---- | ---- | -----------
latitude | Number | - | Latitude of the location.
longitude | Number | - | Longitude of the location.
altitude | Number | Feet (ft) | Altitude.
speed | Number | Miles per hour (mph) | Speed.
heading | Number | Degrees (°) | Direction.
climb | Number | Feet per minute (mph) | Climb.


## User

> Example of a User object

```
{
	"userKey": "asdjfunmdf002imfg",
	"username": "elliot@alderson.com",
	"fullName": "Elliot Alderson",
	"email": "elliot@alderson.com",
	"createdAt": 1440597871546,
	"updatedAt": 1440597871154
}
```

Parameter | Type | Description
--------- | ----------- | -----------
userKey | String | Unique identifier for the user.
username | String | Username of the user.
fullName | String | Full name of the user.
email | String | Email address of the user.
createdAt | Timestamp | Timestamp of the when the user was created.
updatedAt | Timestamp | Timestamp of the when the user was updated.

# Authorization
> To authorize, use this code:

```shell
curl https://api.wia.io/v1 \
	-H "Authorization: Bearer u_jsdf812jkdf01kdf"
```

```javascript
// To create a UserClient
var Wia = require('wia');
var userClient = new Wia.UserClient('userToken');

// To create a DeviceClient
var Wia = require('wia');
var deviceClient = new Wia.DeviceClient('deviceToken');
```

Wia uses access tokens to allow access to the API. When you create an account or a device, an access token is automatically generated.

You can find your user access token in My Account and your device access token in the device's settings.

Wia expects the access token to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer u_ksdf912jnmdfg9k123lk90`

<aside class="warning">Some requests are user or device specific. If you are not using the correct type of token, you will get a 401 Unauthorized response.</aside>

# Devices
## Create a Device

> Example Request

```shell
shell "https://api.wia.io/v1/devices"
	-H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"My first device"}'
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_jsdf812jkdf01kdf');
client.createDevice({
	name: "My first device"
}, function(err, device) {
  if (err) console.log(err);
	if (device) console.log(device);
});
```

> Example Response

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"isOnline": false,
	"createdAt": 1444062135000,
	"updatedAt": 1444062135000
}
```

This endpoint creates a device. Requires a User token.

### HTTP Request

`POST https://api.wia.io/v1/devices`

### URL Parameters

Parameter | Description
--------- | -----------
name | Name of the device to be created

## List Devices
> Example Request

```shell
shell "https://api.wia.io/v1/devices?limit=20"
	-H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_jsdf812jkdf01kdf');
client.listDevices({
	limit: 20
}, function(err, data) {
  if (err) console.log(err);
	if (data) console.log(data);
});
```

> Example Response

```json
[
  {
    "deviceKey": "Jas8snj1msdf89k83jdf",
    "name": "Device One",
    "isOnline": true,
    "createdAt": 1445199652859,
    "updatedAt": 1445199652234
  },
  {
    "deviceKey": "Jas8snj1msdf89k83jdf",
    "name": "Device Two",
    "isOnline": false,
    "createdAt": 1445199652859,
    "updatedAt": 1445199652234
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

## Retrieve a Device

> Example Request

```shell
shell "https://api.wia.io/v1/devices/jnsdf8nmdg09kdfg"
	-H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_jsdf812jkdf01kdf');
client.getDevice("jnsdf8nmdg09kdfg", function(err, device) {
  if (err) console.log(err);
  if (device) console.log(device);
});
```

> Example Response

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"isOnline": true,
	"events": {
	    "sensor": {
	        "timestamp": 1444995825836,
	        "ambientTemperature": {
	            "value": 19.7867,
	            "timestamp": 1444995825836
	        },
	        "light": {
	            "value": 123.65,
	            "timestamp": 1444995825836
	        }
	    }
	},
	"createdAt": 1445199652859,
	"updatedAt": 1445199652234
}
```

This endpoint retrieves a device. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/devices/:deviceKey`

## Retrieve Current Device

> Example Request

```shell
shell "https://api.wia.io/v1/devices/me"
	-H "Authorization: Bearer d_8jdflsdf912kasdf2dffg"
```

```javascript
var Wia = require('wia');
var client = new Wia.DeviceClient('d_8jdflsdf912kasdf2dffg');
client.getMe(function(err, device) {
  if (err) console.log(err);
  if (device) console.log(device);
});
```

> Example Response

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"isOnline": true,
	"createdAt": 1445199652859,
	"updatedAt": 1445199652234
}
```

This endpoint retrieves the current device. Requires a Device token.

### HTTP Request

`GET https://api.wia.io/v1/devices/me`

## List Device Events

> Example Request

```shell
shell "https://api.wia.io/v1/devices/9mdflg982jdmdfglw89dfgn/events?limit=20"
	-H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_kasd9ldsjsdf823fgdfgwdfdfs');
client.listDeviceEvents("9mdflg982jdmdfglw89dfgn", {
  limit: 20
}, function(err, data) {
  if (err) console.log(err);
  if (data) console.log(data);
});
```

> Example Response

```json
[
  {
    "name": "sensor",
    "data": {
  		"temperature": 14.5,
  		"humidity": 56.3
  	},
  	"timestamp": 1440683447
  },
  {
    "name": "location",
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


# Events
## Publish an Event

> Example Request

```shell
curl https://api.wia.io/v1/events \
	-H "Authorization: Bearer d_bzw35dvId2x4Esf23sdgf3fgOUdp16ysUqoig" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"Sensor","data":{"ambientTemperature":24.5}}'
```

```javascript
var Wia = require('wia');
var client = new Wia.DeviceClient('d_8jdflsdf912kasdf2dffg');
client.publishEvent(
	"sensor",
	{
		ambientTemperature: 34.5,
		humidity: 67.4
	},
	function(err) {
    if (err) console.log(err);
    else console.log("Event published.");
	}
);
```

> The above command returns status 200 OK when event has been created.

This endpoint publishes an event. Requires a Device token.

### HTTP Request

`POST https://api.wia.io/v1/events`

### URL Parameters

Parameter | Description
--------- | -----------
deviceKey | Key of device to get events for.

### Attributes

Parameter | Description
--------- | -----------
name | Name of the event. Either Sensor or Location
data | Event data.

## List all events

> Example Request

```shell
curl https://api.wia.io/v1/events \
	-H "Authorization: Bearer u_0123456789abcdef"
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_0123456789abcdef');
client.listEvents({
		limit: 20
	},
	function(err, data) {
    if (err) console.log(err);
    if (data) console.log(data);
	}
);
```

> The above command returns status 200 OK when event has been created.

This endpoint publishes an event. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/events`

### URL Parameters

Parameter | Description
--------- | -----------
name | Name of event.
limit | Number of events to return.
page | Page of events to return.
fromTimestamp | From timestamp in milliseconds.
toTimestamp | To timestamp in milliseconds.
deviceKey | Key of device to get events for.


### Attributes

Parameter | Description
--------- | -----------
name | Name of the event. Either Sensor or Location
data | Event data.


## Subscribe to Device Events

> Example

```shell
Not supported
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_0123456789abcdef');

// For all events use:
client.subscribeToAllDeviceEvents(
	"mndsf81knmsd9mndf",
	function(err, event) {
    if (err) console.log(err);
    if (event) console.log(event);
	}
);

// For a specific event use:
client.subscribeToDeviceEvent(
	"mndsf81knmsd9mndf",
  "sensor",
	function(err, event) {
    if (err) console.log(err);
    if (event) console.log(event);
	}
);
```

> The above command returns an Event object when one has been received.

This endpoint subscribes to device events. Requires a User token.

### HTTP Request

`Not supported.`

## Unsubscribe from Device Events

> Example

```shell
Not supported
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_0123456789abcdef');

// For all device events use:
client.unsubscribeFromAllDeviceEvents(
	"mndsf81knmsd9mndf",
	function(err) {
    if (err) console.log(err);
	}
);

// For a specific device event use:
client.unsubscribeFromDeviceEvent(
  "mndsf81knmsd9mndf",
  "sensor",
	function(err) {
    if (err) console.log(err);
	}
);
```

> The above command returns an Event object when one has been received.

This endpoint unsubscribes from device events. Requires a User token.

### HTTP Request

`Not supported.`

## Send a Ping

> Example Request

```shell
curl http://localhost:8080/v1/ping \
	-H "Authorization: Bearer d_bzw35dvId2x4Esf"
```

```javascript
var Wia = require('wia');
var client = new Wia.DeviceClient('d_bzw35dvId2x4Esf');
client.sendPing(
	function(err) {
    if (err) console.log(err);
	}
);
```

> The above command returns status 200 OK when a ping has been received.


This endpoint allows a device to let the service know it is online. It is not required to use this method when using a stream enabled client. Requires a Device token.

### HTTP Request

`GET https://api.wia.io/v1/ping`


# Commands
## Run a Command
> Example Request

```shell
shell "https://api.wia.io/v1/devices/92nkdng9mkdfg0mdfg/commands/helloCommand/run"
  -H "Authorization: Bearer u_abcdef0123456789" \
  -H "Content-Type: application/json" \
  -X POST
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_0123456789abcdef');
client.runCommand("myFirstDeviceKey", "helloCommand",
	function(err) {
    if (err) console.log(err);
	}
);
```

> Example Response

```
200 OK
```

This endpoint runs a command on a device. Requires a User token.

### HTTP Request

`POST https://api.wia.io/v1/devices/:deviceKey/commands/:commandName/run`

### URL Parameters

Parameter | Description
--------- | -----------
deviceKey | Key of the device
commandName | Name of the command to be run


## Register a Command
> Example Request

```shell
shell "https://api.wia.io/v1/commands/register"
	-H "Authorization: Bearer d_0123456789abcdef" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"commandName"}'
```

```javascript
var Wia = require('wia');
var client = new Wia.DeviceClient('d_0123456789abcdef');
client.registerCommand("helloCommand",
	function(data) {
    // Function to run
		// asynchronously called
	}
);
```

> Example Response

```json
{
	"name": "helloCommand",
	"isEnabled": true,
	"enabledAt": 1444995277000,
	"createdAt": 1444995244000,
	"updatedAt": 1444995277000
}
```

This endpoint register a command for a device. Requires a Device token.

### HTTP Request

`POST https://api.wia.io/v1/commands/register`

### URL Parameters

Parameter | Description
--------- | -----------
name | Name of the command to be registered


## Deregister a Command
> Example Request

```shell
shell "https://api.wia.io/v1/commands/deregister"
  -H "Authorization: Bearer d_0123456789abcdef" \
  -H "Content-Type: application/json" \
  -X PUT -d '{"name":"commandName"}'
```

```javascript
var Wia = require('wia');
var client = new Wia.DeviceClient('d_0123456789abcdef');
client.deregisterCommand("helloCommand", function(err) {
  if (err) console.log(err);
});
```

> Example Response

```json
200 OK
```

This endpoint deregisters a command for a device. Requires a Device token.

### HTTP Request

`PUT https://api.wia.io/v1/commands/deregister`

### URL Parameters

Parameter | Description
--------- | -----------
name | Name of the command to be deregistered


## Deregisters All Commands
> Example Request

```shell
shell "https://api.wia.io/v1/commands/deregisterAll"
  -H "Authorization: Bearer d_0123456789abcdef" \
  -X PUT
```

```javascript
var Wia = require('wia');
var client = new Wia.DeviceClient('d_0123456789abcdef');
client.deregisterAllCommands(function(err) {
  if (err) console.log(err);
});
```

> Example Response

```json
200 OK
```

This endpoint deregisters all commands for a device. Requires a Device token.

### HTTP Request

`PUT https://api.wia.io/v1/commands/deregisterAll`

### URL Parameters

No URL Parameters

# User
## Retrieve Current User

> Example Request

```shell
shell "https://api.wia.io/v1/users/me"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var Wia = require('wia');
var userClient = new Wia.UserClient('u_gh9dfmsdflkasdf2dffg');

userClient.getMe(
	function(err, user) {
    if (err) console.log(err);
		// asynchronously called
	}
);
```

> Example Response

```json
{
  "userKey": "jhdfg8ndfglk",
  "username": "elliot@fsociety.com",
  "firstName": "Elliot",
  "lastName": "Alderson",
  "fullName": "Elliot Alderson",
  "createdAt": 1444063382000
}
```

This endpoint retrieves the currently authenticated user. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/users/me`
