---
title: Wia Documentation

language_tabs:
  - curl
  - Node

toc_footers:
  - <a href='https://www.wia.io/requestInvite'>Request an Invite to Wia</a>
  - <a href='mailto:team@wia.io'>Or say hello at team@wia.io</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Wia Documentation! You can use our API to access Wia API endpoints, which can get information on devices, events and users.

We have client libraries in Node (more coming soon!). You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

> API Endpoint

```
https://api.wia.io
```

# Objects

Wia data are structured around 3 main types of objects: Device, Event and User. You’ll find these objects in the format described below.

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

Parameter | Type | Description
--------- | ----------- | -----------
name | String | Name of the event
data | Object | Event data
timestamp | Timestamp | Timestamp of the event (optional)

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

### Sensor

List of permitted data key-value pairs for a Sensor event.

Parameter | Type | Unit | Description
--------- | ----------- | -----------
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

> Example of a Sensor Event object

```
{
	"name": "Sensor",
	"data": {
		"temperature": 17.5,
		"humdidity": 4.2,
		"pressure": 17.6,
		"gyroscope": {
			"x": 0.342,
			"y": 0.765,
			"z": 0.634
		}
	},
	"timestamp": 1440597871
}
```

### Location

List of permitted data key-value pairs for a Location event.

Parameter | Type | Description
--------- | ----------- | -----------
latitude | Number | Latitude of the location
longitude | Number | Longitude of the location

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

## User

> Example of a User object

```
{
	"userKey": "sd8msdfk9ksdfl",
	"fullName": "Elliot Alderson",
	"firstName": "Elliot",
	"lastName": "Alderson",
	"numberOfDevices": 3,
	"maxNumberOfDevices": 5,
	"eventsThisMonth": 128490,
	"maxEventsPerMonth": 250000
}
```

# Authentication

> To authorize a user, use this code:

```curl
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```Node
var userClient = require('wia-sdk')('u_jsdf812jkdf01kdf');

```

> To authorize a device, use this code:

```curl
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```Node
var deviceClient = require('wia-sdk')('d_jsdf812jkdf01kdf');

```

Wia uses access tokens to allow access to the API. When you create an account or a device, an access token is automatically generated.

You can find your user token in My Account and your device token in the device's settings.

Wia expects the access token to be included in all API requests to the server in a header that looks like the following:

`Authorization: bearer: <TOKEN>`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Devices

## Get Devices

```curl
curl "https://api.wia.io/v1/devices?limit=20"
  -H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```Node
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

This endpoint retrieves devices.

### HTTP Request

`GET https://api.wia.io/v1/devices`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
limit | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.


## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Isis",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">If you're not using an administrator API key, note that some kittens will return 403 Forbidden if they are hidden for admins only.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve
