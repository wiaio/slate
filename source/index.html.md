---
title: Wia Documentation

language_tabs:
  - shell: curl
  - javascript: Node.js
  - objective_c: iOS
  - java: Java
  - python: Python  

toc_footers:
  - <a href='https://www.wia.io/signup' target="_blank">Create a free Wia account</a>
  - <a href='http://twitter.com/wiaio' target="_blank">Follow us on Twitter @wiaio</a>

includes:
  - errors

search: false
---

# Introduction
> REST Endpoint

```
https://api.wia.io (port 443) - Secure
```

> MQTT Endpoint

```
mqtt://api.wia.io (port 1883) - Non-secure
mqtts://api.wia.io (port 8883) - Secure
```

> Websockets Endpoint

```
ws://api.wia.io (port 3000) - Non-secure
wss://api.wia.io (port 3001) - Secure
```

The Wia API is designed to be as intuitive as possible. We offer both a REST API and streaming over MQTT. If you have any questions, would like to see a new feature or find something is broken then <a href="mailto:support@wia.io">let us know</a>.

We have official client libraries that can be found on our <a href="https://github.com/wiaio" target="_blank">GitHub page</a>.

You can view code examples in the dark area to the right, and switch the programming language of the examples with the tabs in the top right.

<aside class="warning">The Wia API is currently under development and breaking changes may be introduced.</aside>

# Authentication
## Authenticating requests

The Wia API expects the secret key or access token be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer token`

It can also be added to a request as a query parameter, for example:

`GET https://api.wia.io/v1/devices?access_token=u_asd81nksdf9191jKASU2k`

<aside class="warning">If you are not using the correct type of token, you will get a 401 Unauthorized response.</aside>

```shell
curl https://api.wia.io/v1 \
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')('secret key or token');

// With secret key in constructor object
var wia = require('wia')({
	secretKey: 'secretKey'
});

// With application key in constructor object
var wia = require('wia')({
	appKey: 'appKey'
});
```

```objective_c
#import "Wia.h"

// Set the secret key
[[WiaClient sharedInstance] setSecretKey:"secret_key"];

// Set the app key
[[WiaClient sharedInstance] setAppKey:"app_key"];
```

```java
import io.wia.Wia;

// Set the secret key
Wia.setSecretKey("secret_key");

// Set the app key
Wia.setAppKey("app_key");
```

```python
import wia

# Set the secret key
wia.secret_key = "secret_key"

# Set the app key
wia.app_key = "app_key"
```

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

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");
Wia.connectToStream();
```

```python
import wia

wia.secret_key = "secret_key"
wia.Stream.connect()
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

```java
import io.wia.Wia;

Wia.disconnectFromStream();
```

```python
import wia

wia.secret_key = "secret_key"
wia.Stream.disconnect()
```

To close an MQTT connection you must disconnect from the stream.

## Stream handlers

> Connect

```shell
Not supported
```

```javascript
wia.stream.on('connect', function() {
	console.log('Stream connected!');
});
```

```objective_c
[[NSNotificationCenter defaultCenter] addObserverForName:@"WiaStreamConnect" object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
	NSLog(@"Stream connected");
});
```

```java
Not supported
```

```python
Not supported
```

> Reconnect

```shell
Not supported
```

```javascript
wia.stream.on('reconnect', function() {
	console.log('Stream reconnecting!');
});
```

```objective_c
[[NSNotificationCenter defaultCenter] addObserverForName:@"WiaStreamReconnect" object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
	NSLog(@"Stream connected");
});
```

```java
Not supported
```

```python
Not supported
```

> Disconnect

```shell
Not supported
```

```javascript
wia.stream.on('disconnect', function() {
	console.log('Stream disconnected!');
});
```

```objective_c
[[NSNotificationCenter defaultCenter] addObserverForName:@"WiaStreamDisconnect" object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
	NSLog(@"Stream connected");
});
```

```java
Not supported
```

```python
Not supported
```

> Offline

```shell
Not supported
```

```javascript
wia.stream.on('offline', function() {
	console.log('Stream offline!');
});
```

```objective_c
[[NSNotificationCenter defaultCenter] addObserverForName:@"WiaStreamOffline" object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
	NSLog(@"Stream connected");
});
```

```java
Not supported
```

```python
Not supported
```

> Error

```shell
Not supported
```

```javascript
wia.stream.on('error', function(error) {
	console.log('Stream error!');
	console.log(error);
});
```

```objective_c
[[NSNotificationCenter defaultCenter] addObserverForName:@"WiaStreamError" object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
	NSLog(@"Stream connected");
});
```

```java
Not supported
```

```python
Not supported
```

When communicating with the stream, a number of notifications are emitted.

Name | Description
--------- | -----------
Connect | Emitted on successful connection.
Reconnect | Emitted when a reconnect starts.
Disconnect | Emitted after a disconnection.
Offline | Emitted when the client goes offline.
Error | Emitted when a client cannot connect or a parsing error occurs.

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
var wia = require('wia')('secret key or token');

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

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Map<String, Object> deviceParams = new HashMap<String, Object>();
deviceParams.put("name", "myFirstDevice");

Device device = Device.create(deviceParams);
```

```python
import wia

wia.secret_key = "secret_key"

device = wia.Device.create(
    name="My first device"
)
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

This endpoint creates a device.

### HTTP Request

`POST https://api.wia.io/v1/devices`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | x

### Parameters

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
var wia = require('wia')('secret key or token');

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

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Device device = Device.retrieve("dev_kj28hfgno19");
```

```python
import wia

wia.secret_key = "secret_key"

device = wia.Device.retrieve("dev_kj28hfgno19")
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

This endpoint retrieves a device.

### HTTP Request

`GET https://api.wia.io/v1/devices/:id`

### Authorization
Access Type | Permitted | Notes
-------------- | -------------- | --------------
Device | ✓ | Can only retrieve information about itself.
User | ✓ | Can retrieve all devices.
Organisation | ✓ | Can retrieve all devices.
Customer | ✓ | Can only retrieve devices linked to their account.

To retrieve the current authenticated device, pass the "me" as the ID.

## Update a device

> Example Request

```shell
curl "https://api.wia.io/v1/devices/dev_23idgk0smd" \
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X PUT -d '{"name":"New Device Name"}'
```

```javascript
var wia = require('wia')('secret key or token');

wia.devices.update("dev_23idgk0smd", {
		name: "New Device Name"
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

```java
import io.wia.Wia;
import io.wia.models.Device;

Wia.setSecretKey("secret_key");

Device retrievedDevice = Device.retrieve("dev_kn18herg9");

Map<String, Object> updateParams = new HashMap<String, Object>();
updateParams.put("name", "New device name");

retrievedDevice.update(updateParams);
```

```python
import wia

wia.secret_key = "sk_asdgf23uNsdfu23jnfdgJKHG"

device = wia.Device.retrieve("dev_kw8bnfimsngubdfg23")
device.name = "New device name"
device.save()
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

This endpoint updates a device's details.

### HTTP Request

`PUT https://api.wia.io/v1/devices/:id`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | x

## Delete a device

> Example Request

```shell
curl "https://api.wia.io/v1/devices/:id" \
	-H "Authorization: Bearer token" \
	-X DELETE
```

```javascript
var wia = require('wia')('secret key or token');

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

```java
import io.wia.Wia;
import io.wia.models.Device;

Device device = Device.retrieve("dev_1j12k2erhgjng89l");

WiaDeletedObject deletedDevice = device.delete();
```

```python
import wia

wia.secret_key = "sk_asdgf23uNsdfu23jnfdgJKHG"

device = wia.Device.retrieve("dev_kw8bnfimsngubdfg23")
device.delete()
```

> The above command returns status 200 OK when a device has been deleted.

This endpoint deletes a device.

### HTTP Request

`DELETE https://api.wia.io/v1/devices/:id`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | x

## List devices
> Example Request

```shell
curl "https://api.wia.io/v1/devices?limit=20" \
	-H "Authorization: Bearer token" \
	-X GET
```

```javascript
var wia = require('wia')('secret key or token');

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
} success:^(NSArray *devices, NSNumber *count) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;
import io.wia.models.DeviceCollection;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("limit", 10);

DeviceCollection devicesCollection = Device.list(params);
```

```python
import wia

wia.secret_key = "secret_key"

result = wia.Device.list(limit=40)
```

> Example Response

```json
[
  "devices": [{
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
  }],
  "count": 2
]
```

This endpoint retrieves devices and a total count.

### HTTP Request

`GET https://api.wia.io/v1/devices`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | 'createdAt' | Field to sort by. Valid values include name and lastUpdated.
sort | String | 'desc' | Either ascending (asc) or descending (desc).
customer | String | - | ID of customer to get devices for.

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
var wia = require('wia')('secret key or token');

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
[[WiaClient sharedInstance] publishEvent:@{
	@"name": @"testEvent",
	@"data": @(12.5)
} success:^(WiaEvent *event) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key";

Map<String, Object> params = new HashMap<String, Object>();
params.put("name", "testEvent");

Event event = Event.publish(params);
```

```python
import wia

wia.secret_key = "secret_key"

result = wia.Event.publish(
            name="event_name",
            data=100
            )
```

> The above command returns status 200 OK when event has been created.

This endpoint publishes an event.

### HTTP Request

`POST https://api.wia.io/v1/events`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | ✓
User | x
Organisation | x
Customer | x

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
var wia = require('wia')('secret key or token');

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
[[WiaClient sharedInstance] subscribeToEvents:@{
  @"device": @"dev_23idgksdf0smd"
}];

// For a specific event use:
[[WiaClient sharedInstance] subscribeToEvents:@{
  @"device": @"dev_23idgksdf0smd",
  @"name": @"temperature"
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Wia.connectToStream();

//for specific events:
final String eventName = "myFirstEvent";
Event.subscribe("device_id", eventName, new WiaEventSubscribeCallback(){
    @Override
    public void received(Event event){
    logger.debug("Got event, Timestamp: " + event.getTimestamp());
    }
  });

//for all events:
Event.subscribe("device_id", new WiaEventSubscribeCallback(){
    @Override
    public void received(Event event){
      logger.debug("Got event, Timestamp: " + event.getTimestamp());
    }
  });
```

```python

import wia

wia.Stream.connect()

def example_func(payload):
  #payload contains event messages
  pass

#for all events use:
wia.Event.subscribe(
    device="device_id",
    func=example_func
  )

#for specific events use:
wia.Event.subscribe(
    device="device_id",
    name="event_name",
    func=example_func
  )

```

> The above command returns an Event object when one has been received.

This endpoint subscribes to events.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## Unsubscribe from events

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')('secret key or token');

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
[[WiaClient sharedInstance] unsubscribeFromEvents:@{
  @"device": @"dev_23idgksdf0smd"
}];

// For a specific event use:
[[WiaClient sharedInstance] unsubscribeFromEvents:@{
  @"device": @"dev_23idgksdf0smd",
  @"name": @"temperature"
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

//for specific events:
final String eventName = "someEvent";
Event.unsubscribe("device_id", eventName);

//for all events:
Event.unsubscribe("device_id");

```

```python
import wia

#for all events use
wia.Event.unsubscribe(
    device="device_id",
  )

#for specific events use:
wia.Event.unsubscribe(
    device="device_id",
    name="event_name"
  )

```

> The above command returns an Event object when one has been received.

This endpoint unsubscribes from device events.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## List events

> Example Request

```shell
curl "https://api.wia.io/v1/events"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')('secret key or token');

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
[[WiaClient sharedInstance] listEvents:@{
	@"device": @"dev_23idgksdf0smd",
	@"limit": @(20),
	@"page": @(0)
} success:^(NSArray *events, NSNumber *count) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;
import io.wia.models.EventCollection;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("limit", 10);

EventCollection EventCollection = Event.list(params);
```

```python
import wia

result = wia.Event.list(
        device="device_id",
        limit=20,
        page=0
        )

```

> Example Response

```json
[
  "events": [{
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
  }],
  "count": 45
]
```

This endpoint retrieves a list of events for a device.

### HTTP Request

`GET https://api.wia.io/v1/events`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
device | String | - | Unique identifier of device to get events for. Required.
name | String | - | Name of the event to return.
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | 'timestamp' | Field to sort by. Valid values include name and lastUpdated.
sort | String | 'desc' | Either ascending (asc) or descending (desc).
since | Timestamp | - | Timestamp to start from.
until | Timestamp | - | Timestamp to return up until.

# Sensors
## The sensor object

> Example of an Sensor object with single data value

```
{
	"name": "sensorName",
	"data": "value",
	"timestamp": 1440597871365
}
```

> Example of an Sensor object with object in data

```
{
	"name": "sensorName",
	"data": {
		"key": "value"
	},
	"timestamp": 1440597871654
}
```

All sensors are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
name | String | Name of the sensor. Alphanumeric characters only.
data | Any | Data associated with the sensor. A number, string or object can be passed into this.
timestamp | Timestamp | Timestamp of the sensor in milliseconds.

## Publish an sensor

> Example Request

```shell
curl https://api.wia.io/v1/sensors \
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"temperature","data":21.5}'
```

```javascript
var wia = require('wia')('secret key');

wia.sensors.publish(
	{ name: "temperature",
	  data: 21.5 },
	function(err, published) {
	    if (err) console.log(err);
	    if (published) console.log("Sensor published.");
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] publishSensor::@{
	@"name": @"temperature",
	@"data": @(21.5)
} success:^(WiaSensor *sensor) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("name", "temperature");
params.put("data", 21.453);

Sensor sensor = Sensor.publish(params);
```

```python
import wia

result = wia.Sensor.publish(
    name="sensor_name",
    data=100
  )

```

> The above command returns status 200 OK when sensor has been created.

This endpoint publishes a sensor event.

### HTTP Request

`POST https://api.wia.io/v1/sensors`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | ✓
User | x
Organisation | x
Customer | x

### Attributes

Parameter | Type | Description
--------- | ----------- | -----------
name | String | Name of the sensor.
data | Any | Data associated with the sensor. A number, string or object can be passed into this.
timestamp | Timestamp | Timestamp of the sensor in milliseconds.

## Subscribe to sensors

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')('secret key or token');

// For all sensors use:
wia.sensors.subscribe(
	{ device: "dev_23idgksdf0smd" },
	function(err, sensor) {
	    if (err) console.log(err);
	    if (sensor) console.log(sensor);
	}
);

// For a specific sensor use:
wia.sensors.subscribe(
	{ device: "dev_23idgksdf0smd",
	  name: "temperature" },
	function(err, sensor) {
	    if (err) console.log(err);
	    if (sensor) console.log(sensor);
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];

// For all sensors use:
[[WiaClient sharedInstance] subscribeToSensors:@{
	@"device": @"dev_23idgksdf0smd"
}];

// For a specific sensor use:
[[WiaClient sharedInstance] subscribeToSensors:@{
	@"device": @"dev_23idgksdf0smd",
	@"name": @"temperature"
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Wia.connectToStream();

//for specific sensors:
final String sensorName = "temperature";
Sensor.subscribe("device_id", sensorName, new WiaSensorSubscribeCallback(){
    @Override
    public void received(Sensor sensor){
    logger.debug("Got sensor, Timestamp: " + sensor.getTimestamp());
    }
  });

//for all sensors:
Sensor.subscribe("device_id", new WiaSensorSubscribeCallback(){
    @Override
    public void received(Sensor sensor){
      logger.debug("Got sensor, Timestamp: " + sensor.getTimestamp());
    }
  });
```

```python
import wia

def example_func(payload):
  #payload will have sensor messages
  pass

#for all sensors use:
wia.Sensor.subscribe(
    device="device_id",
    func=example_func
  )

#for specific sensors use:
wia.Sensor.subscribe(
    device="device_id",
    name="sensor_name",
    func=example_func
  )
```

> The above command returns an Sensor object when one has been received.

This endpoint subscribes to sensor events.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## Unsubscribe from sensors

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')('secret key');

// For all device sensors use:
wia.sensors.unsubscribe(
	{ device: "dev_23idgksdf0smd" },
	function(err) {
 	   if (err) console.log(err);
	}
);

// For a specific device sensor use:
wia.sensors.unsubscribe(
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

// For all sensors use:
[[WiaClient sharedInstance] unsubscribeFromSensors:@"dev_23idgksdf0smd"
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];

// For a specific sensor use:
[[WiaClient sharedInstance] unsubscribeFromSensors:@"dev_23idgksdf0smd"
  name:@"temperature"
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

//for specific sensors:
final String sensorName = "temperature";
Sensor.unsubscribe("device_id", sensorName);

//for all sensors:
Sensor.unsubscribe("device_id");
```

```python
import wia

#for all sensors use:
wia.Sensor.unsubscribe(
    device="device_id"
  )

#for a specific sensor use:
wia.Sensor.unsubscribe(
    device="device_id",
    name="sensor_name"
  )
```

> The above command returns an Sensor object when one has been received.

This endpoint unsubscribes from device sensors.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## List sensors

> Example Request

```shell
curl "https://api.wia.io/v1/sensors"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')('secret key');

wia.sensors.list({
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
[[WiaClient sharedInstance] listSensors:@"dev_23idgksdf0smd"
  params: @{
    @"limit": @(20)
  }
  success:^(NSArray *sensors) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;
import io.wia.models.SensorCollection;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("device", "device_id");
params.put("limit", 10);

SensorCollection SensorCollection = Sensor.list(params);
```

```python
import wia

result = wia.Sensor.list(
    device="device_id",
    limit=20,
    page=0
  )
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
    "name": "humidity",
    "data": 12.5,
  	"timestamp": 1440683234546
  }
]
```

This endpoint retrieves a list of sensor events for a device.

### HTTP Request

`GET https://api.wia.io/v1/sensors`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
device | String | - | Unique identifier of device to get sensors for. Required.
name | String | - | Name of the sensor to return events for.
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | 'timestamp' | Field to sort by. Valid values include name and lastUpdated.
sort | String | 'desc' | Either ascending (asc) or descending (desc).
since | Timestamp | - | Timestamp to start from.
until | Timestamp | - | Timestamp to return up until.

# Locations
## The location object

> Example of an Event object

```
{
	"id": "B7EB5EAA-9166-422E-8482-DC41EFD076B2",
	"latitude": 53.349805,
	"longitude": -6.260310,
	"altitude": 0,
	"timestamp": 1440597871365,
	"receivedTimestamp": 1440597871365
}
```

All events are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
id | String | Unique identifier for the location object.
latitude | Number | Latitude of the location.
longitude | Number | Longitude of the location.
altitude | Number | Altitude of the location.
timestamp | Timestamp | Timestamp of the location in milliseconds.
receivedTimestamp | Timestamp | Received timestamp of the location in milliseconds.

## Publish a location

> Example Request

```shell
curl https://api.wia.io/v1/locations \
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"latitude":53.349805,"longitude":-6.260310}'
```

```javascript
var wia = require('wia')('secret key');

wia.locations.publish(
	{ "latitude": 53.349805,
	  "longitude": -6.260310 },
	function(err, published) {
	    if (err) console.log(err);
	    if (published) console.log("Location published.");
	}
);
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] publishLocation: @{
    @"latitude": @(53.349805),
    @"longitude": @(-6.260310)
  }
  success:^(WiaLocation *location) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("longitude", -6.260310);
params.put("latitude", 53.349805);

Location location = Location.publish(params);
```

```python
import wia

result = wia.Location.publish(
    longitude=-6.260310,
    latitude=53.349805
  )
```

> The above command returns status 200 OK when location has been published.

This endpoint publishes a location.

### HTTP Request

`POST https://api.wia.io/v1/locations`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | ✓
User | x
Organisation | x
Customer | x

### Attributes

Parameter | Type | Description
--------- | ----------- | -----------
latitude | Number | Latitude of the location.
longitude | Number | Longitude of the location.
altitude | Number | Altitude of the location.

## Subscribe to locations

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')('secret key or token');

wia.locations.subscribe(
	{ device: "dev_23idgksdf0smd" },
	function(err, location) {
	    if (err) console.log(err);
	    if (location) console.log(location);
	}
);

```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];

// For all events use:
[[WiaClient sharedInstance] subscribeToLocations:@"dev_23idgksdf0smd"
  success:^(WiaLocation *location) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret key");

Wia.connectToStream();

Location.subscribe("device_id", new WiaLocationSubscribeCallback(){
    @Override
    public void received(Location location){
      logger.debug("Got location, Timestamp: " + location.getTimestamp());
    }
  });
```

```python
import wia

def example_func(payload):
  #payload will contain location messages
  pass

wia.Location.subscribe(
    device="device_id",
    func=example_func
  )
```

> The above command returns a Location object when one has been received.

This endpoint subscribes to locations.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## Unsubscribe from locations

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')('secret key or token');

wia.locations.unsubscribe(
	{ device: "dev_23idgksdf0smd" },
	function(err) {
 	   if (err) console.log(err);
	}
);

```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];

[[WiaClient sharedInstance] unsubscribeFromLocations:@"dev_23idgksdf0smd"
  success:^() {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Location.unsubscribe("device_id");
```

```python
import wia

wia.Location.unsubscribe(
    device="device_id"
  )
```

> The above command unsubscribe from devices locations.

This endpoint unsubscribes from device locations.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## List locations

> Example Request

```shell
curl "https://api.wia.io/v1/locations"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')('secret key or token');

wia.locations.list({
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
[[WiaClient sharedInstance] listLocations:@"dev_23idgksdf0smd"
  params: @{
    @"limit": @(20)
  }
  success:^(NSArray *locations) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
import io.wia.Wia;
import io.wia.models.LocationCollection;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("device", "device_id");
params.put("limit", 10);

LocationCollection LocationsCollection = Location.list(params);
```

```python
import wia

wia.Location.list(
    device="device_name",
    limit=20,
    page=0
  )
```

> Example Response

```json
[
  {
	"id": "B7EB5EAA-9166-422E-8482-DC41EFD076B2",
	"latitude": 53.349805,
	"longitude": -6.260310,
	"altitude": 0,
	"timestamp": 1440597871365,
	"receivedTimestamp": 1440597871365
  },
  {
	"id": "F8F4184D-4374-41D3-B6A1-7291B522642D",
	"latitude": 53.349805,
	"longitude": -6.260310,
	"altitude": 0,
	"timestamp": 1440597871365,
	"receivedTimestamp": 1440597871365
  },
  {
	"id": "F6F9E75B-209B-4633-9FD2-71C006E37F7D",
	"latitude": 53.349805,
	"longitude": -6.260310,
	"altitude": 0,
	"timestamp": 1440597871365,
	"receivedTimestamp": 1440597871365
  }
]
```

This endpoint retrieves a list of locations for a device.

### HTTP Request

`GET https://api.wia.io/v1/locations`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
device | String | - | Unique identifier of device to get locations for. Required.
limit | Number | 20 | Number of devices to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | 'timestamp' | Field to sort by. Valid values include name and lastUpdated.
sort | String | 'desc' | Either ascending (asc) or descending (desc).
since | Timestamp | - | Timestamp to start from.
until | Timestamp | - | Timestamp to return up until.

# Logs
## The log object

> Example of an Log object

```
{
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
var wia = require('wia')('secret key or token');

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

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("level", "info");
params.put("message", "test");

Log log = Log.publish(params);
```

```python
import wia

result = wia.Log.publish(
          level="info",
          message="example message"
          )
```

> The above command returns status 200 OK when log has been created.

This endpoint publishes a log. Requires a Device token.

### HTTP Request

`POST https://api.wia.io/v1/logs`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | ✓
User | x
Organisation | x
Customer | x

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
var wia = require('wia')('secret key or token');

// For all logs use:
wia.logs.subscribe(
	{ device: "dev_kndjg832hjdfg" },
	function(err, log) {
	    if (err) console.log(err);
	    if (log) console.log(log);
	}
);

// For a specific log level use:
wia.logs.subscribe(
	{ device: "dev_kndjg832hjdfg",
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

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

Wia.connectToStream();

//for specific log level:
final String logLevel = "info";
Log.subscribe("device_id", logLevel, new WiaLogSubscribeCallback(){
    @Override
    public void received(Log log){
    logger.debug("Got log, Timestamp: " + log.getTimestamp());
    }
  });

//for all levels:
Log.subscribe("device_id", new WiaLogSubscribeCallback(){
    @Override
    public void received(Log log){
      logger.debug("Got log, Timestamp: " + log.getTimestamp());
    }
  });
```

```python
import wia

def example_func(payload):
  #payload will have logs
  pass

#for all logs use:
wia.Log.subscribe(
    device="device_id",
    func=example_func
  )

#for a specific log level use:
wia.Log.subscribe(
    device="device_id",
    level="level",
    func=example_func
  )
```

> The above command returns a log object when one has been received.

This endpoint subscribes to logs.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## Unsubscribe from logs

> Example

```shell
Not supported
```

```javascript
var wia = require('wia')('secret key or token');

// For all device events use:
wia.logs.unsubscribe(
	{ device: "id" },
	function(err) {
 	   if (err) console.log(err);
	}
);

// For a specific device event use:
wia.logs.unsubscribe(
	{ device: "id",
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

```java
import io.wia.Wia;

Wia.setSecretKey("secret_key");

//for specific levels:
final String logLevel = "info";
Log.unsubscribe("device_id", logLevel);

//for all levels:
Log.unsubscribe("device_id");
```

```python
import wia

#for all logs use:
wia.Log.unsubscribe(
    device="device_id"
  )
#for specific log level use:
wia.Log.unsubscribe(
    device="device_id",
    level="level"
  )
```

This endpoint unsubscribes from device logs.

### HTTP Request

`Not supported.`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

## List logs

> Example Request

```shell
curl "https://api.wia.io/v1/logs"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')('secret key or token');

wia.logs.list({
	device: "dev_ksdnf28bndfg",
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

```java
import io.wia.Wia;
import io.wia.models.SensorCollection;

Wia.setSecretKey("secret_key");

Map<String, Object> params = new HashMap<String, Object>();
params.put("device", "device_id");
params.put("limit", 10);

LogCollection LogCollection = Log.list(params);
```

```python
import wia

result = wia.Log.list(
          device="device_id",
          limit=20,
          page=0
        )
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

This endpoint retrieves a list of logs for a device.

### HTTP Request

`GET https://api.wia.io/v1/logs`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | x

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
device | String | - | Unique identifier of device to get events for. Required.
level | String | - | Level of the log to return.
limit | Number | 20 | Number of logs to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | 'timestamp' | Field to sort by.
sort | String | 'desc' | Either ascending (asc) or descending (desc).
since | Timestamp | - | Timestamp to start from.
until | Timestamp | - | Timestamp to return up until.

# Functions
## The function object

> Example of a Function object

```
{
	"id": "func_aba7812nsdfk",
	"name": "helloFunction",
	"isEnabled": true,
	"device": {
		"id": "dev_gh8jfg9MASDu"
	},
	"enabledAt": 1445253805000,
	"createdAt": 1444995244000,
	"updatedAt": 1445253805000
}
```

All functions are made up of the same object structure.

Parameter | Type | Description
--------- | ----------- | -----------
device | String | Unique identifier for the function.
name | String | Name of the function.
isEnabled | Boolean | If the function is currently enabled or not.
device | Device | Device function is assigned to.
enabledAt | Timestamp | Timestamp in milliseconds of when the function was last enabled.
createdAt | Timestamp | Timestamp in milliseconds of when the function was initially created.
updatedAt | Timestamp | Timestamp in milliseconds of when the function was last updated.

## Create a function
> Example Request

```shell
curl "https://api.wia.io/v1/functions"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"name":"functionName"}'
```

```javascript
var wia = require('wia')('secret key or token');

wia.functions.create(
	{ name: "helloWorld" },
	function(data) {
   		// Function to run
   		console.log("Hello world!");
	}, function(err, registered) {
    if (err) // Error occurred
      console.log(err);
    if (registered) // Registered successfully
      console.log(registered);
	}
);
```

```objective_c
Not available
```

```java
Not available
```

```python
import wia

def test_function(argument):
  #function implementation

result = wia.Function.create(
    name='function_name',
    function=test_function
  )
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

This endpoint register a function for a device.

### HTTP Request

`POST https://api.wia.io/v1/functions`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | ✓
User | x
Organisation | x
Customer | x

### URL Parameters

Parameter | Description
--------- | -----------
name | Name of the function to be registered

## Delete a function
> Example Request

```shell
curl "https://api.wia.io/v1/functions/func_aba7812nsdfk"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X DELETE -d '{"name":"functionName"}'
```

```javascript
var wia = require('wia')('secret key or token');

wia.functions.delete(
	"func_aba7812nsdfk",
	function(err, deregistered) {
		if (err) // Error occurred
		if (deregistered) // Deregistered successfully
	}
);
```

```objective_c
Not available
```

```java
Not available
```

```python
import wia

wia.Function.delete(
    "function_id"
  )
```

> Example Response

```json
200 OK
```

This endpoint deregisters a function for a device.

### HTTP Request

`DELETE https://api.wia.io/v1/functions/:id`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | ✓
User | x
Organisation | x
Customer | x

### URL Parameters

Parameter | Type | Description
--------- | ---- | -----------
id | String | Unique ID of function.
name | String | Name of the function to be deleted.

## Call a function
> Example Request

```shell
curl "https://api.wia.io/v1/functions/:id/call"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X DELETE
```

```javascript
var wia = require('wia')('secret key or token');

wia.functions.call(
	{ id: "func_9thkLKKf9jsdfm",
	  device: "dev_knsd9k1n2dfg" },
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

```java
Not available
```

```python
import wia

wia.Function.call(
    device="device_id",
    func="function_id",
    data={'arg': 100}
  )
```

> Example Response

```
200 OK
```

This endpoint runs a function on a device.

### HTTP Request

`POST https://api.wia.io/v1/functions/:id/call`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

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
var wia = require('wia')('secret key or token');

wia.functions.list({
	device: "dev_jndg81bdfngl",
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

```java
Not available
```

```python
import wia

result = wia.Function.list(
    device="device_id",
    limit=20,
    page=0
  )
```

> Example Response

```json
{
    "functions": [
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
    ],
    "count": 2
}
```

This endpoint retrieves a list of functions for a device.

### HTTP Request

`GET https://api.wia.io/v1/functions`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | ✓
Organisation | ✓
Customer | ✓

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
device | String | - | Unique identifier for the device. Required.
limit | Number | 20 | Number of functions to return. Max value 200.
page | Number | 0 | Page of results.

# Customers
## The customer object

> Example of a User object

```
{
	"id": "cus_BDjfkdsd8Jasd1fg",
	"username": "elliot@alderson.com",
	"email": "elliot@alderson.com",
	"fullName": "Elliot Alderson",
	"avatar": "http://images.wia.io/avatars/A97F0394-D03A-479A-87B8-FDC85079F4A5",
	"createdAt": 1440597871546,
	"updatedAt": 1440597871154
}
```

Parameter | Type | Description
--------- | ----------- | -----------
id | String | Unique identifier for the customer.
username | String | Username of the customer.
email | String | Email address of the customer.
fullName | String | Full name of the customer.
avatar | String | URL to the customers avatar.
createdAt | Timestamp | Timestamp of the when the customer was created.
updatedAt | Timestamp | Timestamp of the when the customer was updated.

## Create a customer

> Example Request

```shell
curl "https://api.wia.io/v1/customers"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X POST -d '{"fullName":"Elliot Andereson", "email":"elliot@fsociety.com"}'
```

```javascript
var wia = require('wia')('secret key');

wia.customers.create({
	fullName: "Elliot Anderson",
	email: "elliot@fsociety.com"
}, function(err, user) {
	if (err) console.log(err);
	if (user) console.log(user);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"secret key"];
[[WiaClient sharedInstance] createCustomer:@{
	fullName: @"Elliot Anderson",
	email: @"elliot@fsociety.com"
} success:^(WiaUser *user) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
Not available
```

```python
Not available
```

> Example Response

```json
{
	"id": "cus_BDjfkdsd8Jasd1fg",
	"username": "elliot@alderson.com",
	"email": "elliot@alderson.com",
	"fullName": "Elliot Alderson",
	"avatar": "http://images.wia.io/avatars/A97F0394-D03A-479A-87B8-FDC85079F4A5",
	"createdAt": 1440597871546,
	"updatedAt": 1440597871154
}
```

This endpoint creates a customer.

### HTTP Request

`POST https://api.wia.io/v1/customers`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | x
Organisation | ✓
Customer | x

### Parameters

Parameter | Type | Description
--------- | ---- | -----------
fullName | String | Full name of the customer to be created. Required.
email | String | Email address of customer to be created. Required.

## Retrieve a customer

> Example Request

```shell
curl "https://api.wia.io/v1/customers/cus_kndfg82mM90fdgm1"
	-H "Authorization: Bearer token"
```

```javascript
var wia = require('wia')('secret key or token');

wia.users.retrieve("cus_kndfg82mM90fdgm1", function(err, user) {
	if (err) console.log(err);
	if (user) console.log(user);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] retrieveCustomer:@"cus_kndfg82mM90fdgm1"
  success:^(WiaCustomer *customer) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
Not available
```

```python
Not available
```

> Example Response

```json
{
	"id": "cus_jhdfg8ndfglk",
	"username": "elliot@fsociety.com",
	"firstName": "Elliot",
	"lastName": "Alderson",
	"fullName": "Elliot Alderson",
	"createdAt": 1444063382000,
	"updatedAt": 1444063382000
}
```

This endpoint retrieves a customer.

### HTTP Request

`GET https://api.wia.io/v1/customers/:id`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | x
Organisation | ✓
Customer | x

## Update a customer

> Example Request

```shell
curl "https://api.wia.io/v1/customers/cus_kndfg82mM90fdgm1"
	-H "Authorization: Bearer token" \
	-H "Content-Type: application/json" \
	-X PUT -d '{"fullName":"Tyrell Wellick"}'
```

```javascript
var wia = require('wia')('secret key or token');

wia.customers.update("cus_kndfg82mM90fdgm1",
	{"fullName":"Tyrell Wellick"}, function(err, customer) {
	if (err) console.log(err);
	if (customer) console.log(customer);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] updateCustomer:@"cus_kndfg82mM90fdgm1"
  fields:@{
    @"fullName": @"Tyrell Wellick"
  }
  success:^(WiaCustomer *customer) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
Not available
```

```python
Not available
```

> Example Response

```json
{
	"id": "user_jhdfg8ndfglk",
	"username": "elliot@fsociety.com",
	"firstName": "Tyrell",
	"lastName": "Wellick",
	"fullName": "Tyrell Wellick",
	"createdAt": 1444063382000,
	"updatedAt": 1444063382000
}
```

This endpoint updates a customer.

### HTTP Request

`PUT https://api.wia.io/v1/customers/:id`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | x
Organisation | ✓
Customer | x

### Parameters

Parameter | Type | Description
--------- | ---- | -----------
fullName | String | Full name of the customer to be created.
email | String | Email address of customer to be created.

## Delete a customer

> Example Request

```shell
curl "https://api.wia.io/v1/customers/cus_kndfg82mM90fdgm1"
	-X DELETE
```

```javascript
var wia = require('wia')('secret key or token');

wia.customers.delete("cus_kndfg82mM90fdgm1", function(err, customer) {
	if (err) console.log(err);
	if (customer) console.log(customer);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] deleteCustomer:@"cus_kndfg82mM90fdgm1"
  success:^(BOOL deleted) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
Not available
```

```python
Not available
```

> Example Response

```json
200 OK
```

This endpoint deletes a customer.

### HTTP Request

`DELETE https://api.wia.io/v1/customers/:id`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | x
Organisation | ✓
Customer | x

## List customers
> Example Request

```shell
curl "https://api.wia.io/v1/customers?limit=20" \
	-H "Authorization: Bearer token" \
	-X GET
```

```javascript
var wia = require('wia')('secret key or token');

wia.customers.list({
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
[[WiaClient sharedInstance] listCustomers:@{
  @"limit": @(20),
  @"page": @(0)
} success:^(NSDictionary *result) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
Not available
```

```python
Not available
```

> Example Response

```json
[
  "users": [{
	"id": "cus_jhdfg8ndfglk",
	"username": "elliot@fsociety.com",
	"firstName": "Elliot",
	"lastName": "Anderson",
	"fullName": "Elliot Anderson",
	"createdAt": 1444063382000,
	"updatedAt": 1444063382000
  },
  {
	"id": "cus_mkn324875gBasdu",
	"username": "tyrell@ecorp.com",
	"firstName": "Tyrell",
	"lastName": "Wellick",
	"fullName": "Tyrell Wellick",
	"createdAt": 1444063382000,
	"updatedAt": 1444063382000
  }],
  "count": 2
]
```

This endpoint retrieves users and a total count.

### HTTP Request

`GET https://api.wia.io/v1/customers`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | x
Organisation | ✓
Customer | x

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
limit | Number | 20 | Number of customers to return. Max value 200.
page | Number | 0 | First page is 0.
order | String | 'createdAt' | Field to sort by. Valid values include 'createdAt', 'updatedAt' and 'name'.
sort | String | 'desc' | Either ascending 'asc' or descending 'desc'.

## Add device to customer
> Example Request

```shell
curl "https://api.wia.io/v1/customers/cus_jhdfg8ndfglk/devices" \
	-H "Authorization: Bearer token" \
	-X POST -d '{"device":"dev_ms8dfgknLA9k"}'
```

```javascript
var wia = require('wia')('secret key or token');

wia.customers.addDevice("cus_jhdfg8ndfglk", "dev_ms8dfgknLA9k", function(err, added) {
 	if (err) console.log(err);
	if (added) console.log(added);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] addDevice:@"" toUser@""
	success:^(BOOL added) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
Not available
```

```python
Not available
```

> Example Response

```json
200 OK
```

This endpoint adds a device to a customer.

### HTTP Request

`POST https://api.wia.io/v1/customers/:id/devices`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | x
Organisation | ✓
Customer | x

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
device | String | - | Unique identifier of device to be added to customer.

## Remove device from customer
> Example Request

```shell
curl "https://api.wia.io/v1/customers/cus_jhdfg8ndfglk/devices/dev_ms8dfgknLA9k" \
	-H "Authorization: Bearer token" \
	-X DELETE
```

```javascript
var wia = require('wia')('secret key or token');

wia.users.removeDevice("cus_jhdfg8ndfglk", "dev_ms8dfgknLA9k", function(err, removed) {
 	if (err) console.log(err);
	if (removed) console.log(removed);
});
```

```objective_c
#import "Wia.h"

[[WiaClient sharedInstance] initWithToken:@"token"];
[[WiaClient sharedInstance] removeDevice:@"" fromCustomer@""
	success:^(BOOL removed) {
  // Success
} failure:^(NSError *error) {
  // An error occurred
}];
```

```java
Not available
```

```python
Not available
```

> Example Response

```json
200 OK
```

This endpoint removes a device from a customer.

### HTTP Request

`DELETE https://api.wia.io/v1/customers/:customerId/devices/:deviceId`

### Authorization
Access Type | Permitted
-------------- | --------------
Device | x
User | x
Organisation | ✓
Customer | x
