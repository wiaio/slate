---
title: Wia Documentation

language_tabs:
  - shell: curl
  - javascript: Node.js
#   - objective_c: iOS

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
var wia = require('wia');
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

> To generate a customer access token, use this code:

```shell
curl https://<organisation-slug>.wia.io/v1/auth/token \
	-d username=<username> \
	-d password=<password> \
	-d grantType=password \
	-d scope=customer
```

```javascript
var wia = require('wia');
wia.setOrganisationSlug('org-slug');
var accessToken = wia.generateAccessToken({
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
deviceKey | String | Unique key of the device.
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
curl "https://api.wia.io/v1/devices/:deviceKey"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.retrieve("deviceKey", function(err, device) {
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
curl "https://api.wia.io/v1/devices/me"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.update("deviceKey", {
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
curl "https://api.wia.io/v1/devices/me"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.devices.delete(
	"deviceKey",
	function(err, confirmed) {
	  if (err) console.log(err);
	  if (confirmed) console.log(confirmed);
	}
);
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
curl "https://api.wia.io/v1/devices"
	-H "Authorization: Bearer userToken"
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

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | name | Field to sort by. Valid values include name and lastUpdated.
sort | String | asc | Either ascending (asc) or descending (desc).

## Send a ping

> Example Request

```shell
curl http://api.wia.io/v1/ping \
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'
);
wia.sendPing(
	function(err) {
   		if (err) console.log(err);
	}
);
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
	{ deviceKey: "deviceKey" },
	function(err, event) {
	    if (err) console.log(err);
	    if (event) console.log(event);
	}
);

// For a specific event use:
wia.events.subscribe(
	{ deviceKey: "deviceKey",
	  name: "eventName" },
	function(err, event) {
	    if (err) console.log(err);
	    if (event) console.log(event);
	}
);
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
	{ deviceKey: "deviceKey" },
	function(err) {
 	   if (err) console.log(err);
	}
);

// For a specific device event use:
wia.events.unsubscribe(
	{ deviceKey: "deviceKey",
	  name: "eventName" },
	function(err) {
 	   if (err) console.log(err);
	}
);
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
	deviceKey: "deviceKey",
	limit: 20,
	page: 0
}, function(err, data) {
	if (err) console.log(err);
	if (data) console.log(data);
});
```

> Example Response

```json
[
  {
	"deviceKey": "dev_ms8dfgknLA9k",
    "name": "temperature",
    "data": 25.4,
  	"timestamp": 1440683447
  },
  {
	"deviceKey": "dev_bijgwe29nNlop",	 
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

`GET https://api.wia.io/v1/events`

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
deviceKey | String | - | Key of device to get events for. Required.
name | String | - | Name of the event to return.
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | name | Field to sort by. Valid values include name and lastUpdated.
sort | String | asc | Either ascending (asc) or descending (desc).
since | Timestamp | - | Timestamp to start from.
until | Timestamp | - | Timestamp to return up until.

# Commands
## The command object

> Example of a Command object

```
{
	"deviceKey": "Jas8snj1msdf89k83jdf",
	"name": "helloCommand",
	"isEnabled": true,
	"enabledAt": 1445253805000,
	"createdAt": 1444995244000,
	"updatedAt": 1445253805000
}
```

All commands are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
deviceKey | String | Unique key for the device.
name | String | Name of the command.
isEnabled | Boolean | If the command is currently enabled or not.
enabledAt | Timestamp | Timestamp in milliseconds of when the command was last enabled.
createdAt | Timestamp | Timestamp in milliseconds of when the command was initially created.
updatedAt | Timestamp | Timestamp in milliseconds of when the command was last updated.

## Register a command
> Example Request

```shell
curl "https://api.wia.io/v1/commands/register"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"commandName"}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.commands.register("commandName",
	function(data) {
   		// Function to run
	}, function(err, registered) {
		if (err) // Error occurred
		if (registered) // Registered successfully	
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


## Deregister a command
> Example Request

```shell
curl "https://api.wia.io/v1/commands/deregister"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X PUT -d '{"name":"commandName"}'
```

```javascript
var wia = require('wia')(
	'token'
);
wia.commands.deregister("commandName",
	function(err, registered) {
		if (err) // Error occurred
		if (deregistered) // Deregistered successfully	
	}
);
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
curl "https://api.wia.io/v1/commands/deregisterAll"
	-H "Authorization: Bearer token" \
	-X PUT
```

```javascript
var wia = require('wia')(
	'token'
);
wia.commands.deregisterAll(
	function(err, deregistered) {
		if (err) // Error occurred
		if (deregistered) // Deregistered successfully	
	}
);
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

## Run a command
> Example Request

```shell
curl "https://api.wia.io/v1/devices/:deviceKey/commands/:commandName/run"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST
```

```javascript
var wia = require('wia')(
	'token'
);
wia.commands.run(
	{ deviceKey: "deviceKey",
	  commandName: "commandName" },
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
deviceKey | Unique key of the device.
commandName | Name of the command to be run.

## List commands
> Example Request

```shell
curl "https://api.wia.io/v1/commands"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token'	
);
wia.commands.list({
	deviceKey: "deviceKey",
	limit: 20
}, function(err, commands) {
	if (err) console.log(err);
	if (commands) console.log(commands);
});
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

`GET https://api.wia.io/v1/commands`

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
deviceKey | String | - | Unique key of the device. Required.
limit | Number | 20 | Number of commands to return. Max value 200.
page | Number | 0 | Page of results.

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
customerKey | String | Unique key for the customer.
username | String | Username of the customer.
fullName | String | Full name of the customer.
email | String | Email address of the customer.
createdAt | Timestamp | Timestamp of the when the customer was created.
updatedAt | Timestamp | Timestamp of the when the customer was updated.

## Create a customer

> Example Request

```shell
curl https://<organisation-slug>.wia.io/v1/customers \
	-H "Authorization: Bearer token" \
	-d fullName="fullName" \
	-d email="email"
```

```javascript
var wia = require('wia')(
	'token', 
	'org-slug'
);
wia.customers.create({
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
	"customerKey": "cus_jhdfg8ndfglk",
	"username": "elliot@fsociety.com",
	"email": "elliot@fsociety.com",
	"fullName": "Elliot Alderson",
	"createdAt": 1435104000000,
	"updatedAt": 1441152000000
}
```

This endpoint creates a new customer. Requires a User token.

### HTTP Request

`POST https://api.wia.io/v1/customers`

## Retrieve a customer

> Example Request

```shell
curl "https://api.wia.io/v1/customers/:customerKey"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token',
	'org-slug'
);
wia.customers.retrieve("customerKey", 
	function(err, customer) {
		if (err) console.log(err);
		if (customer) console.log(customer);
	}
);
```

> Example Response

```json
{
	"customerKey": "cus_jhdfg8ndfglk",
	"username": "elliot@fsociety.com",
	"email": "elliot@fsociety.com",
	"fullName": "Elliot Alderson",
	"createdAt": 1435104000000,
	"updatedAt": 1441152000000
}
```

This endpoint retrieves a customer. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/customers/:customerKey`

## Update a customer

> Example Request

```shell
curl "https://api.wia.io/v1/customers/:customerKey"
	-H "Authorization: Bearer token"
	-X PUT -d '{"name":"commandName"}'
```

```javascript
var wia = require('wia')(
	'token',
	'org-slug'
);
wia.customers.update("customerKey", {
		fullName: "New Name"
	}, function(err, customer) {
	if (err) console.log(err);
	if (customer) console.log(customer);
});
```

> Example Response

```json
{
	"customerKey": "cus_jhdfg8ndfglk",
	"username": "elliot@fsociety.com",
	"email": "elliot@fsociety.com",
	"fullName": "Elliot Alderson",
	"createdAt": 1435104000000,
	"updatedAt": 1441152000000
}
```

This endpoint retrieves a customer. Requires a User token.

### HTTP Request

`PUT https://api.wia.io/v1/customers/:customerKey`

## Delete a customer

> Example Request

```shell
curl "https://api.wia.io/v1/customers/:customerKey"
	-H "Authorization: Bearer token"
	-X DELETE'
```

```javascript
var wia = require('wia')(
	'token',
	'org-slug'
);
wia.customers.delete("customerKey", function(err, deleted) {
	if (err) console.log(err);
	if (deleted) console.log(deleted);
});
```

> Example Response

```
200 OK
```

This endpoint deletes a customer. Requires a User token.

### HTTP Request

`DELETE https://api.wia.io/v1/customers/:customerKey`

## List customers
> Example Request

```shell
curl "https://api.wia.io/v1/customers"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token',
	'org-slug'
);
wia.customers.list({
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
	"customerKey": "cus_jhdfg8ndfglk",
	"username": "elliot@fsociety.com",
	"email": "elliot@fsociety.com",
	"fullName": "Elliot Alderson",
	"createdAt": 1435104000000,
	"updatedAt": 1441152000000
  },
  {
	"customerKey": "cus_20kdfgj9kfo",
	"username": "a.moss@allsafe.com",
	"email": "a.moss@allsafe.com",
	"fullName":  "Angela Moss",
	"createdAt": 1435104000000,
	"updatedAt": 1441152000000
  }
]
```

This endpoint retrieves devices. Requires a User token.

### HTTP Request

`GET https://api.wia.io/v1/customers`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
limit | 20 | Number of devices to return. Max value 200.
page | 0 | First page is 0.
order | name | Field to sort by. Valid values include name and lastUpdated.
sort | asc | Either ascending (asc) or descending (desc).

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
curl "https://api.wia.io/v1/users/me"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')(
	'token',
	'org-slug'
);
wia.users.me(
	function(err, user) {
    if (err) console.log(err);
		// asynchronously called
	}
);
```

> Example Response

```json
{
	"userKey": "user_jhdfg8ndfglk",
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

> Example Response

```json
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

This endpoint retrieves the current organisation in use. Requires a User token.
