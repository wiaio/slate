---
title: Wia Documentation

language_tabs:
  - shell: curl
  - javascript: Node.js
  - objective_c: iOS

toc_footers:
  - <a href='https://www.wia.io/signup' target="_blank">Create a Wia account</a>
  - <a href='http://twitter.com/wiaio' target="_blank">Follow us on Twitter @wiaio</a>

includes:
  - errors

search: false
---

# Introduction
> REST Endpoint

```
https://api.wia.io (port 443)
```

> MQTT Endpoint

```
mqtt://api.wia.io (port 1883)
mqtts://api.wia.io (port 8883)
```

The Wia API is designed to be as intuitive as possible. We offer both a REST API and streaming over MQTT. If you have any questions, would like to see a new feature or find something is broken then <a href="mailto:support@wia.io">let us know</a>.

We have official client libraries that can be found on our <a href="https://github.com/wiaio" target="_blank">GitHub page</a>.

You can view code examples in the dark area to the right, and switch the programming language of the examples with the tabs in the top right.

<aside class="warning">The Wia API is currently under development and breaking changes may be introduced.</aside>

# Authentication
## Generate an access token
> To generate a user access token, use this code:

```shell
curl https://api.wia.io/v1/auth/token \
	-d username=<username> \
	-d password=<password> \
	-d grantType=password \
	-d scope=user
```

```javascript
var wia = require('wia')('');
var accessToken = wia.generateAccessToken({
		username: "username",
		password: "password",
		grantType: "password",
		scope: "user"
	}, function(err, accessToken) {
		if (err) // Handle error
		else if (accessToken) // Got token!
	});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] generateAccessToken:@"username"
  password:@"password"
  grantType:@"password"
  scope:@"user"
  success:^(WiaAccessToken *accessToken) {
  // Got access token
} failure:(void (^)(NSError *error))failure{
  // Failed
}];
```

> Example of an access token

```
{
	"accessToken": "u_kna8MAsd92ksdf00ksla0k2mhndf",
	"refreshToken": "u_mdfg8MMAS8msd912kasdf89mj",
	"tokenType": "bearer",
	"expiresIn": "0",
	"scope": "user"
}
```

An access token may be generated for a user.

By default, access tokens expire after 24 hours unless specified using `expiresIn` parameter. For an access token that lives forever, set `expiresIn` to `0`.

Access token request parameters:

Parameter | Type | Description
--------- | ----------- | -----------
username | String | Username of the entity requesting access.
password | String | Password of the entity requesting access.
expiresIn | Number | Number of seconds this token is valid for. 0 means forever.
grantType | String | One of either 'password' or 'refreshToken'.
scope | String | Scope of the access token that is being requested. Default is 'user'.

All access tokens are made up of the same object structure.

The Access Token object

Parameter | Type | Description
--------- | ----------- | -----------
accessToken | String | Unique access token that is used for all requests.
tokenType | String | The type of access token.
expiresIn | Number | Number of seconds this token is valid for. 0 means forever.
refreshToken | String | Unique refresh token that is used to generate a new access token.
scope | String | Scope of the access token.

## Authenticating requests

The Wia API expects the access token to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer token`

<aside class="warning">If you are not using the correct type of token, you will get a 401 Unauthorized response.</aside>

If you are connecting using your own MQTT client, put your access token in the username field and blank space for the password.

`Endpoint: mqtt://api.wia.io:1883 or mqtts://api.wia.io:8883`  
`Username: "token"`  
`Password: " " // Note the space`  

# Stream
## Connect to stream

> To connect to the stream, use this code:

```shell
Not supported
```

```javascript
var wia = require('wia')('token');
wia.stream.connect();
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] connectToStream];
```

We recommend sending all events and logs down the Wia stream (i.e. MQTT), instead of via REST. In order to do so, you must connect to the stream before publishing any data or you will get an error.

## Disconnect from stream

> To disconnect from the stream, use this code:

```shell
Not supported
```

```javascript
var wia = require('wia')('token');
wia.stream.disconnect();
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] disconnectFromStream];
```

To close an MQTT connection you must disconnect from the stream.

# Devices
## The device object

> Example of a Device object

```
{
	"id": "dev_sdf892mdfgj238kdfg",
	"name": "Device One",
	"events": {
	    "temperature": {
		    "data": 21.5,
	        "timestamp": 1444995825836
	    },
	    "gyroscope": {
		    "data": {
			    "x": 0.015,
			    "y": 0.67,
			    "z": 0.49
		    },
	        "timestamp": 1444995825836
	    }
	},
	"isOnline": true,
	"createdAt": "1445198559000",
	"updatedAt": "1445198559000"
}
```

All devices are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
id | String | Unique identifier of the device.
name | String | Name of the device.
events | Object | List of most recent events.
isOnline | Boolean | Online status of the device.
createdAt | Timestamp | Timestamp of when device was created.
updatedAt | Timestamp | Timestamp of when device was updated.

## Create a device

> Example Request

```shell
curl "https://api.wia.io/v1/devices"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"My first device"}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.create({
	name: "My first device"
}, function(err, device) {
	if (err) console.log(err);
	if (device) console.log(device);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] createDevice:@{
  name: @"My first device"
} success:^(WiaDevice *device) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
{
	"id": "dev_1msdf89k83jdf",
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

## Retrieve a device

> Example Request

```shell
curl "https://api.wia.io/v1/devices/:id"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.retrieve("dev_23idgk0smd", function(err, device) {
	if (err) console.log(err);
	if (device) console.log(device);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] retrieveDevice:@"dev_23idgk0smd"
success:^(WiaDevice *device) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
{
	"id": "dev_23idgk0smd",
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

`GET https://api.wia.io/v1/devices/:id`

## Retrieve current device

> Example Request

```shell
curl "https://api.wia.io/v1/devices/me"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.me(function(err, device) {
	if (err) console.log(err);
	if (device) console.log(device);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] retrieveCurrentDevice:^(WiaDevice *device) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
{
	"id": "dev_msdf89k83jdf",
	"name": "Device One",
	"isOnline": true,
	"createdAt": 1445199652859,
	"updatedAt": 1445199652234
}
```

This endpoint retrieves the current device. Requires a Device token.

### HTTP Request

`GET https://api.wia.io/v1/devices/me`

## Update a device

> Example Request

```shell
curl "https://api.wia.io/v1/devices/me"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.update("dev_23idgk0smd", {
		name: "newName"
	}, function(err, device) {
		if (err) console.log(err);
		if (device) console.log(device);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] updateDevice:@"dev_23idgk0smd"
  fields:@{
    @"name": @"New device name"
  }
  success:^(WiaDevice *device) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
{
	"id": "dev_23idgk0smd",
	"name": "Device One",
	"createdAt": 1445199652859,
	"updatedAt": 1445199652234
}
```

This endpoint updates a device's details. Requires a User token.

### HTTP Request

`PUT https://api.wia.io/v1/devices/:id`

## Delete a device

> Example Request

```shell
curl "https://api.wia.io/v1/devices/:id"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.delete(
	"dev_23idgk0smd",
	function(err, deleted) {
	  if (err) console.log(err);
	  if (deleted) console.log(deleted);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] deleteDevice:@"dev_23idgksdf0smd"
  success:^(BOOL deleted) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
{
	"id": "dev_23idgksdf0smd",
	"name": "Device One",
	"createdAt": 1445199652859,
	"updatedAt": 1445199652234
}
```

This endpoint deletes a device. Requires a User token.

### HTTP Request

`DELETE https://api.wia.io/v1/devices/:id`

## List devices
> Example Request

```shell
curl "https://api.wia.io/v1/devices?limit=20" \
	-H "Authorization: Bearer token" \
	-X GET
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.list({
	limit: 20,
	page: 0
}, function(err, data) {
 	if (err) console.log(err);
	if (data) console.log(data);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] listDevices:@{
  @"limit": @(20),
  @"page": @(0)
} success:^(NSArray *devices) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
[
  {
    "id": "dev_23idgksdf0smd",
    "name": "Device One",
    "isOnline": true,
    "createdAt": 1445199652859,
    "updatedAt": 1445199652234
  },
  {
    "id": "dev_823nmdfgoshISj",
    "name": "Device Two",
    "isOnline": false,
    "createdAt": 1445199652859,
    "updatedAt": 1445199652234
  }
]
```

This endpoint retrieves devices. Requires a User or Customer token.

### HTTP Request

`GET https://api.wia.io/v1/devices`

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | name | Field to sort by. Valid values include name and lastUpdated.
sort | String | asc | Either ascending (asc) or descending (desc).

# Events
## The event object

> Example of an Event object with single data value

```
{
	"name": "eventName",
	"data": "value",
	"timestamp": 1440597871365
}
```

> Example of an Event object with object in data

```
{
	"name": "eventName",
	"data": {
		"key": "value"
	},
	"timestamp": 1440597871654
}
```

All events are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
name | String | Name of the event. Alphanumeric characters only.
data | Any | Data associated with the event. A number, string or object can be passed into this.
timestamp | Timestamp | Timestamp of the event in milliseconds.

## Publish an event

> Example Request

```shell
curl https://api.wia.io/v1/events \
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"temperature","data":21.5}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.events.publish(
	{ name: "temperature",
	  data: 21.5 },
	function(err, published) {
	    if (err) console.log(err);
	    if (published) console.log("Event published.");
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] publishEvent:@"temperature"
  data:@(21.5)
  success:^(WiaDevice *device) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> The above command returns status 200 OK when event has been created.

This endpoint publishes an event. Requires a Device token.

### HTTP Request

`POST https://api.wia.io/v1/events`

### Attributes

Parameter | Type | Description
--------- | ----------- | -----------
name | String | Name of the event.
data | Any | Data associated with the event. A number, string or object can be passed into this.
file | Buffer | File to be associated with the event.
timestamp | Timestamp | Timestamp of the event in milliseconds.

## Subscribe to events

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')(
	'token'
);
// For all events use:
wia.events.subscribe(
	{ device: "dev_23idgksdf0smd" },
	function(err, event) {
	    if (err) console.log(err);
	    if (event) console.log(event);
	}
);

// For a specific event use:
wia.events.subscribe(
	{ device: "dev_23idgksdf0smd",
	  name: "temperature" },
	function(err, event) {
	    if (err) console.log(err);
	    if (event) console.log(event);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];

// For all events use:
[[WiaClient sharedInstance] subscribeToEvents:@"dev_23idgksdf0smd"
  success:^(WiaDeviceEvent *event) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];

// For a specific event use:
[[WiaClient sharedInstance] subscribeToEvents:@"dev_23idgksdf0smd"
  name:@"temperature"
  success:^(WiaDeviceEvent *event) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> The above command returns an Event object when one has been received.

This endpoint subscribes to events. Requires a User token.

### HTTP Request

`Not supported.`

## Unsubscribe from events

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')(
	'token'
);

// For all device events use:
wia.events.unsubscribe(
	{ device: "dev_23idgksdf0smd" },
	function(err) {
 	   if (err) console.log(err);
	}
);

// For a specific device event use:
wia.events.unsubscribe(
	{ device: "dev_23idgksdf0smd",
	  name: "temperature" },
	function(err) {
 	   if (err) console.log(err);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];

// For all events use:
[[WiaClient sharedInstance] unsubscribeFromEvents:@"dev_23idgksdf0smd"
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];

// For a specific event use:
[[WiaClient sharedInstance] unsubscribeFromEvents:@"dev_23idgksdf0smd"
  name:@"temperature"
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> The above command returns an Event object when one has been received.

This endpoint unsubscribes from device events. Requires a User token.

### HTTP Request

`Not supported.`

## List events

> Example Request

```shell
curl "https://api.wia.io/v1/events"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.events.list({
	device: "dev_23idgksdf0smd",
	limit: 20,
	page: 0
}, function(err, data) {
	if (err) console.log(err);
	if (data) console.log(data);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] listEvents:@"dev_23idgksdf0smd"
  params: @{
    @"limit": @(20)
  }
  success:^(NSArray *events) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
[
  {
	"id": "dev_ms8dfgknLA9k",
    "name": "temperature",
    "data": 25.4,
  	"timestamp": 1440683447764
  },
  {
	"id": "dev_bijgwe29nNlop",	 
    "name": "location",
    "data": {
  		"latitude": 54.60247,
  		"longitude": -5.92717
  	},
  	"timestamp": 1440683234546
  }
]
```

This endpoint retrieves a list of events for a device. Requires a User or Customer token.

### HTTP Request

`GET https://api.wia.io/v1/events`

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
id | String | - | Unique identifier of device to get events for. Required.
name | String | - | Name of the event to return.
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | name | Field to sort by. Valid values include name and lastUpdated.
sort | String | asc | Either ascending (asc) or descending (desc).
since | Timestamp | - | Timestamp to start from.
until | Timestamp | - | Timestamp to return up until.

# Logs
## The log object

> Example of an Event object with single data value

```
{
	"id": "dev_ksd892MN9k12l",
	"level": "warning",
	"message": "Memory usage too high.",
	"data": {
		"memory": 425
	},
	"timestamp": 1440597871365
}
```

All logs are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
id | String | Unique identifier for the device.
level | String | Level of the log. Only 'fatal','error','warn','info','debug' or 'trace' allowed.
message | String | Message of the log
data | Any | Data associated with the log. A number, string or object can be passed into this.
timestamp | Timestamp | Timestamp of the event in milliseconds.

## Publish a log

> Example Request

```shell
curl https://api.wia.io/v1/logs \
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"level":"info","message":"message"}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.logs.publish(
	{ level: "info",
	  message: "message" },
	function(err, published) {
	    if (err) console.log(err);
	    if (published) console.log("Log published.");
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] publishLog:@"level"
  message: @"My first log message"
  data: @{
    @"cpu": @"1.5"
  }
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> The above command returns status 200 OK when log has been created.

This endpoint publishes a log. Requires a Device token.

### HTTP Request

`POST https://api.wia.io/v1/logs`

### Attributes

Parameter | Type | Description
--------- | ----------- | -----------
level | String | Level of the log. Only 'fatal','error','warn','info','debug' or 'trace' allowed.
message | String | Message of the log
data | Any | Data associated with the log. A number, string or object can be passed into this.
timestamp | Timestamp | Timestamp of the log in milliseconds.

## Subscribe to logs

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')(
	'token'
);
// For all logs use:
wia.logs.subscribe(
	{ id: "id" },
	function(err, log) {
	    if (err) console.log(err);
	    if (log) console.log(log);
	}
);

// For a specific log level use:
wia.logs.subscribe(
	{ id: "id",
	  level: "level" },
	function(err, log) {
	    if (err) console.log(err);
	    if (log) console.log(log);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];

// For all logs use:
[[WiaClient sharedInstance] subscribeToLogs:@"id"
  success:^(WiaDeviceLog *log) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];

// For a specific log level use:
[[WiaClient sharedInstance] subscribeToLogs:@"id"
  level:@"level"
  success:^(WiaDeviceLog *log) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> The above command returns a log object when one has been received.

This endpoint subscribes to logs. Requires a User token.

### HTTP Request

`Not supported.`

## Unsubscribe from logs

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')(
	'token'
);

// For all device events use:
wia.logs.unsubscribe(
	{ id: "id" },
	function(err) {
 	   if (err) console.log(err);
	}
);

// For a specific device event use:
wia.logs.unsubscribe(
	{ id: "id",
	  level: "level" },
	function(err) {
 	   if (err) console.log(err);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];

// For all logs use:
[[WiaClient sharedInstance] unsubscribeFromLogs:@"id"
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];

// For a specific log level use:
[[WiaClient sharedInstance] unsubscribeFromLogs:@"id"
  level:@"level"
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

This endpoint unsubscribes from device logs. Requires a User token.

### HTTP Request

`Not supported.`

## List logs

> Example Request

```shell
curl "https://api.wia.io/v1/logs"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.logs.list({
	id: "id",
	limit: 20,
	page: 0
}, function(err, data) {
	if (err) console.log(err);
	if (data) console.log(data);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] listLogs:@"id"
  params: @{
    @"limit": @(20),
    @"page": @(0)
  }
  success:^(NSArray *devices) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
[
  {
	"id": "dev_ksd892MN9k12l",
	"level": "warning",
	"message": "Memory usage too high.",
	"data": {
		"memory": 425
	},
	"timestamp": 1440597871365
  },
  {
	"id": "dev_ksd892MN9k12l",
	"level": "warning",
	"message": "Memory usage too high.",
	"data": {
		"memory": 425
	},
	"timestamp": 1440597871365
  }
]
```

This endpoint retrieves a list of logs for a device. Requires a User or Customer token.

### HTTP Request

`GET https://api.wia.io/v1/logs`

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
id | String | - | Unique identifier of device to get events for. Required.
level | String | - | Level of the log to return.
limit | Number | 20 | Number of logs to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | timestamp | Field to sort by.
sort | String | desc | Either ascending (asc) or descending (desc).
since | Timestamp | - | Timestamp to start from.
until | Timestamp | - | Timestamp to return up until.

# Functions
## The function object

> Example of a Function object

```
{
	"id": "Jas8snj1msdf89k83jdf",
	"name": "helloFunction",
	"isEnabled": true,
	"enabledAt": 1445253805000,
	"createdAt": 1444995244000,
	"updatedAt": 1445253805000
}
```

All functions are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
id | String | Unique identifier for the device.
name | String | Name of the function.
isEnabled | Boolean | If the function is currently enabled or not.
enabledAt | Timestamp | Timestamp in milliseconds of when the function was last enabled.
createdAt | Timestamp | Timestamp in milliseconds of when the function was initially created.
updatedAt | Timestamp | Timestamp in milliseconds of when the function was last updated.

## Register a function
> Example Request

```shell
curl "https://api.wia.io/v1/functions/register"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"functionName"}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.functions.register(
	{ name: "functionName" },
	function(data) {
   		// Function to run
	}, function(err, registered) {
		if (err) // Error occurred
		if (registered) // Registered successfully
	}
);
```

```objective_c
Not available
```

> Example Response

```json
{
	"name": "helloFunction",
	"isEnabled": true,
	"enabledAt": 1444995277000,
	"createdAt": 1444995244000,
	"updatedAt": 1444995277000
}
```

This endpoint register a function for a device. Requires a Device token.

### HTTP Request

`POST https://api.wia.io/v1/functions/register`

### URL Parameters

Parameter | Description
--------- | -----------
name | Name of the function to be registered

## Deregister a function
> Example Request

```shell
curl "https://api.wia.io/v1/functions/deregister"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X PUT -d '{"name":"functionName"}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.functions.deregister(
	{ name: "functionName" },
	function(err, deregistered) {
		if (err) // Error occurred
		if (deregistered) // Deregistered successfully
	}
);
```

```objective_c
Not available
```

> Example Response

```json
200 OK
```

This endpoint deregisters a function for a device. Requires a Device token.

### HTTP Request

`PUT https://api.wia.io/v1/functions/deregister`

### URL Parameters

Parameter | Type | Description
--------- | ---- | -----------
name | String | Name of the function to be deregistered.
all | Boolean | If set to true, deregisters all functions for a device.

## Call a function
> Example Request

```shell
curl "https://api.wia.io/v1/functions/call"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"id":"dev_kandfg981lodfg9", "name": "myFirstFunc"}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.functions.call(
	{ id: "id",
	  name: "functionName" },
	function(err) {
    	if (err) console.log(err);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] callFunction:@{
		@"id": @"dev_id",
		@"name": @"functionName"
	}
	success:^() {
		// Success
	} failure:^(NSError *error) {
	  // An error occurred
}];
```

> Example Response

```
200 OK
```

This endpoint runs a function on a device. Requires a User or Customer token.

### HTTP Request

`POST https://api.wia.io/v1/functions/call`

### URL Parameters

Parameter | Type | Description
--------- | ---- | -----------
id | String | Unique identifier of the device.
name | String | Name of the function to be run.
data | Any | Data to send to the function.

## List functions
> Example Request

```shell
curl "https://api.wia.io/v1/functions"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.functions.list({
	device: "id",
	limit: 20
}, function(err, functions) {
	if (err) console.log(err);
	if (functions) console.log(functions);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] listFunctions:@"id"
  params: @{
    @"limit": @(20),
    @"page": @(0)
  }
  success:^(NSArray *functions) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
[
	{   
	    "id": "func_nj1msdf89k83jdf",
  		"name":"helloFunction1",
  		"isEnabled":true,
  		"enabledAt":1445253805000,
  		"createdAt":1444995244000,
  		"updatedAt":1445253805000
  	},
  	{
	    "id": "func_8912jdfg9MSADh9",
  		"name":"helloFunction2",
  		"isEnabled":true,
  		"enabledAt":1445253805000,
  		"createdAt":1445253575000,
  		"updatedAt":1445253805000
  	}
]
```

This endpoint retrieves a list of functions for a device. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/functions`

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
id | String | - | Unique identifier for the device. Required.
limit | Number | 20 | Number of functions to return. Max value 200.
page | Number | 0 | Page of results.

# Users
## The user object

> Example of a User object

```
{
	"id": "user_asdjfunmdf002imfg",
	"username": "elliot@alderson.com",
	"fullName": "Elliot Alderson",
	"email": "elliot@alderson.com",
	"createdAt": 1440597871546,
	"updatedAt": 1440597871154
}
```

Parameter | Type | Description
--------- | ----------- | -----------
id | String | Unique identifier for the user.
username | String | Username of the user.
fullName | String | Full name of the user.
email | String | Email address of the user.
createdAt | Timestamp | Timestamp of the when the user was created.
updatedAt | Timestamp | Timestamp of the when the user was updated.

## Retrieve current user

> Example Request

```shell
curl "https://api.wia.io/v1/users/me"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token',
	'org-slug'
);
wia.users.me(function(err, user) {
	if (err) console.log(err);
	if (user) console.log(user);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] retrieveCurrentUser:^(WiaUser *user) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
{
	"id": "user_jhdfg8ndfglk",
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

# Organisations
## The organisation object

> Example of a organisation object

```
{
	"id": "org_asdjfunmdf002imfg",
	"name": "ACME Inc",
	"slug": "acme-inc",
	"avatar": {
		"default": "https://cdn.wia.io/images/acme-org-avatar.png",
		"thumbnail": "https://cdn.wia.io/images/acme-org-avatar_thumb.png"
	},
	"createdAt": 1440597871546,
	"updatedAt": 1440597871154
}
```

Parameter | Type | Description
--------- | ----------- | -----------
id | String | Unique identifier for the organisation.
name | String | Name of the organisation.
slug | String | Slug of the organisation. Used as the organisations URL.
avatar | String | Avatar of the organisation
createdAt | Timestamp | Timestamp of the when the organisation was created.
updatedAt | Timestamp | Timestamp of the when the organisation was updated.

## Retrieve current organisation

> Example Request

```shell
curl "https://api.wia.io/v1/organisations/me"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token',
	'org-slug'
);
wia.organisations.me(
	function(err, org) {
   		if (err) console.log(err);
   		if (org) console.log(org);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] retrieveCurrentOrganisation:^(WiaOrganisation *organisation) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

> Example Response

```json
{
	"id": "org_asdjfunmdf002imfg",
	"name": "ACME Inc",
	"slug": "acme-inc",
	"avatar": {
		"default": "https://cdn.wia.io/images/acme-org-avatar.png",
		"thumbnail": "https://cdn.wia.io/images/acme-org-avatar_thumb.png"
	},
	"createdAt": 1440597871546,
	"updatedAt": 1440597871154
}
```

This endpoint retrieves the current organisation in use. Requires a User token.
