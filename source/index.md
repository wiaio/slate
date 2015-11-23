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
var Wia = require('wia');
var accessToken = Wia.generateAccessToken({
		username: "username",
		password: "password",
		grantType: "password",
		scope: "user"
	}, function(err, accessToken) {
		if (err) // Handle error
		else if (accessToken) // Got token!
	}); 
```

> To generate a customer access token, use this code:

```shell
curl https://<organisation-slug>.wia.io/v1/auth/token \
	-d username=<username> \
	-d password=<password> \
	-d grantType=password \
	-d scope=customer
```

```javascript
var Wia = require('wia');
var accessToken = Wia.generateAccessToken({
		username: "username",
		password: "password",
		grantType: "password",
		scope: "customer"
	}, function(err, accessToken) {
		if (err) // Handle error
		else if (accessToken) // Got token!
	}); 
```

> Example of an access token

```
{
	"accessToken": "u_kna8MAsd92ksdf00ksla0k2mhndf",
	"tokenType": "bearer",
	"expiresIn": "0",
	"refreshToken": "u_mdfg8MMAS8msd912kasdf89mj",
	"scope": "user"
}
```

An access token may be generated for a user, customer or device.

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

# Devices
## The device object

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

## Create a device

> Example Request

```shell
shell "https://api.wia.io/v1/devices"
	-H "Authorization: Bearer u_userToken" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"My first device"}'
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('userToken');
client.createDevice({
	name: "My first device"
}, function(err, device) {
  if (err) console.log(err);
	if (device) console.log(device);
});
```

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient createDevice:@"My first device" success:^(WiaDevice *device) {
    NSLog(@"%@", device);
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
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


## Retrieve a device

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

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient getDevice:@"deviceKey" success:^(WiaDevice *device) {
    NSLog(@"%@", device);
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
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

## Retrieve current device

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

```objective_c
Currently not supported
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

## Update a device

> Example Request

```shell
shell "https://api.wia.io/v1/devices/me"
	-H "Authorization: Bearer d_8jdflsdf912kasdf2dffg"
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('accessToken');

client.devices.update("deviceKey", {
		name: "newName"
	}, function(err, device) {
		if (err) console.log(err);
		if (device) console.log(device);
	}
);
```

> Example Response

```json
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "Device One",
	"createdAt": 1445199652859,
	"updatedAt": 1445199652234
}
```

This endpoint updates a device's details. Requires a User token.

### HTTP Request

`PUT https://api.wia.io/v1/devices/:deviceKey`

## Delete a device

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

This endpoint deletes a device. Requires a User token.

### HTTP Request

`DELETE https://api.wia.io/v1/devices/:deviceKey`

## List devices
> Example Request

```shell
shell "https://api.wia.io/v1/devices"
	-H "Authorization: Bearer userToken"
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('userToken');
client.listDevices({
	limit: 20
}, function(err, data) {
 	if (err) console.log(err);
	if (data) console.log(data);
});
```

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient listDevices:20 page:0 success:^(NSArray *devices) {
    NSLog(@"%@", devices);
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
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

## List device events

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

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient listDeviceEvents:@"deviceToken" limit:20 page:0 eventName:@"sensor" success:^(NSArray *events) {
    NSLog(@"%@", events);
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
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

## Send a ping

> Example Request

```shell
curl http://api.wia.io/v1/ping \
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

```objective_c
Currently not supported.
```

> The above command returns status 200 OK when a ping has been received.


This endpoint allows a device to let the service know it is online. It is not required to use this method when using a stream enabled client. Requires a Device token.

### HTTP Request

`GET https://api.wia.io/v1/ping`

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
	-H "Authorization: Bearer d_bzw35dvId2x4Esf23sdgf3fgOUdp16ysUqoig" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"temperature","data":21.5}'
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

```objective_c
Currently not supported.
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
timestamp | Timestamp | Timestamp of the event in milliseconds.

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

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient listEvents:20 page:0 eventName:@"sensor" success:^(NSArray *events) {
    NSLog(@"%@", events);
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
```

> The above command returns status 200 OK when event has been created.

This endpoint publishes an event. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/events`

### URL Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
limit | Number | 20 | Number of events to return.
page | Number | 0 | Page of events to return.
fromTimestamp | Number | - | From timestamp in milliseconds.
toTimestamp | Number | - | To timestamp in milliseconds.
order | String | timestamp | Field to sort the records by.
sort | String | desc | Ascending (asc) or descending (desc) of the order.
name | String | - | Name of event.

## Subscribe to events

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

```objective_c
Currently not supported.
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

```objective_c
Currently not supported.
```

> The above command returns an Event object when one has been received.

This endpoint unsubscribes from device events. Requires a User token.

### HTTP Request

`Not supported.`

# Commands
## List device commands
> Example Request

```shell
shell "https://api.wia.io/v1/devices/Jas8snj1msdf89k83jdf/commands"
	-H "Authorization: Bearer u_kasd9ldsjsdf823fgdfgwdfdfs"
```

```javascript
var Wia = require('wia');
var client = new Wia.UserClient('u_jsdf812jkdf01kdf');
client.listDeviceCommands("Jas8snj1msdf89k83jdf", {
	limit: 20
}, function(err, commands) {
	if (err) console.log(err);
	if (commands) console.log(commands);
});
```

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient listDeviceCommands:@"deviceKey" limit:20 page:0 success:^(NSArray *commands) {
    NSLog(@"%@", commands);
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
```

> Example Response

```json
[
	{   
	    "deviceKey": "Jas8snj1msdf89k83jdf",
  		"name":"helloCommand",
  		"isEnabled":true,
  		"enabledAt":1445253805000,
  		"createdAt":1444995244000,
  		"updatedAt":1445253805000
  	},
  	{
	    "deviceKey": "Jas8snj1msdf89k83jdf",
  		"name":"testCommand16",
  		"isEnabled":true,
  		"enabledAt":1445253805000,
  		"createdAt":1445253575000,
  		"updatedAt":1445253805000
  	}
]
```

This endpoint retrieves devices. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/devices/:deviceKey/commands`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
limit | 20 | Number of commands to return. Max value 200.
page | 0 | First page is 0.

## Run a command
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

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient runCommand:@"deviceKey" commandName:@"commandName" commandData:nil success:^(NSObject *obj) {
    NSLog(@"Command sent!");
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
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


## Register a command
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

```objective_c
Currently not supported.
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


## Deregister a command
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

```objective_c
Currently not supported.
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


## Deregisters all commands
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

```objective_c
Currently not supported.
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

# Customers
## The customer object

> Example of a Customer object

```
{
	"customerKey": "cus_asdjfunmdf002imfg",
	"username": "elliot@alderson.com",
	"fullName": "Elliot Alderson",
	"email": "elliot@alderson.com",
	"createdAt": 1440597871546,
	"updatedAt": 1440597871154
}
```

Parameter | Type | Description
--------- | ----------- | -----------
customerKey | String | Unique identifier for the customer.
username | String | Username of the customer.
fullName | String | Full name of the customer.
email | String | Email address of the customer.
createdAt | Timestamp | Timestamp of the when the customer was created.
updatedAt | Timestamp | Timestamp of the when the customer was updated.

## Create a customer

> Example Request

```shell
curl https://<organisation-slug>.wia.io/v1/customers \
	-H "Authorization: Bearer u_i2rBorJ6Zyyu4c3pXt2mhDijwSoFC51T" \
	-d fullName="fullName" \
	-d email="email"
```

```javascript
var Wia = require('wia');
var userClient = new Wia.UserClient('u_gh9dfmsdflkasdf2dffg', 'organisation-slug');

userClient.customers.create({
		fullName: "fullName",
		email: "email"
	}, function(err, customer) {
	    if (err) // Handle error
		else if (customer) // Do something with customer.
	}
);
```

> Example Response

```json
{
  "customerKey": "jhdfg8ndfglk",
  "username": "elliot@fsociety.com",
  "email": "elliot@fsociety.com",
  "fullName": "Elliot Alderson",
  "createdAt": 1444063382000,
  "updatedAt": 1444063382000
}
```

This endpoint creates a new customer. Requires a User token.

### HTTP Request

`POST https://api.wia.io/v1/customers`

# Users
## The user object

> Example of a User object

```
{
	"userKey": "user_asdjfunmdf002imfg",
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

## Retrieve current user

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

```objective_c
WiaUserClient *userClient = [[WiaUserClient alloc] initWithToken:@"userToken"];
[userClient getUserMe:^(WiaUser *user) {
    NSLog(@"%@", user);
} failure:^(NSError *error) {
    NSLog(@"%@", [error localizedDescription]);
}];
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

# Organisations
## The organisation object

> Example of a organisation object

```
{
	"organisationKey": "org_asdjfunmdf002imfg",
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
organisationKey | String | Unique identifier for the organisation.
name | String | Name of the organisation.
slug | String | Slug of the organisation. Used as the organisations URL.
avatar | String | Avatar of the organisation
createdAt | Timestamp | Timestamp of the when the organisation was created.
updatedAt | Timestamp | Timestamp of the when the organisation was updated.
