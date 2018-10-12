# Samespace Call Flow API

Some ways on how to start exploring Samespace APIs ....

NOTE: This document assumes you've successfully created a samespace cloud and created users and teams in it.

# Setup

Generate a auth token for a user in Samespace.  Feel free to contact Administrator for it.

Using this REST API, you can make outgoing call flows,and query metadata about calls you've created. 

### Base Url

All URLs referenced in the documentation have the following base:

```https://developers.samespace.com/api/v2```


### Call Flow API

```/call/flow```

Post Payload

```
{
	"cloud_id":"CLOUD",
	"cloud_sid":"CLOUDSID",
	"auth_token":"your auth token",
	“record”: true, #defaults to false
	"flow":[{...},{...}]
}
```

"publish": this key contains configuration to publish real-time events to a url 

 Example: 
 ```
 publish: {
		  url:”https://status_url”,
		  method: “PUT” # defaults to POST 
}
```

"flow": This key contains array of call flow objects used to define a call flow. The list of building blocks that may be used to build call flows is as follows.

### Call Flow Objects

#### Call

Using this Call Flow Object one can place a call to a number with a  specified caller id number.

##### Example
```
{
			"id":<<some id>>,
			"type":"call",
			"number":"XXXXXX",
			"caller_id":"123456",
			"next_step":<<some other id>>
	}
```
id : id can be any string unique for that particular call flow object.
type: type defines the type of call flow object.("call" here)
number: number can be any PSTN number or internal number that will be called
next_step: next_step defines the id of next call flow object to be executed in the flow.

#### Play

Using this Call Flow Object one can play a text or a media url.

##### Example
```
{
			"id":<<some id>>,
			"type":"play",
			"text":"Hi, My name is Sumeet Tiwari. Press 1 for apples or press 2 for oranges.",
            "url":"http://link_to_media_file",
			"next_step":<<some other id>>
	}
```
id : id can be any string unique for that particular call flow object.
type: type defines the type of call flow object.("play" here)
url: link of the mp3 file to be played
text: text that needs to be said
next_step: next_step defines the id of next call flow object to be executed in the flow.

NOTE: Either of text or url field is mandatory

#### Input

Using this Call Flow Object to gather digits from the user.

##### Example
```
{
            		"id":<<some id>>,
			"type":"keypad",
			"count":4,
			"flow":{
				"*":<<some other id>>
			 },
			"variable":"ID"
	}
```
id : id can be any string unique for that particular call flow object.
type: type defines the type of call flow object.("digits" here)
count: count of digits to gather before we proceed.
flow: flow based on digits input.
variable: variable in which we should store input

#### HTTP

Using this Call Flow Object one can make http call during a call flow.

##### Example
```
{
			"id":<<some id>>,
			"type":"http",
			"url":"https://subdomain.domain.com/api/{{ID}}",
            "method":"GET",
			"flow":{
				"success":<<some id 1>>,
				"fail":<<some id 2>>
			 }
	}
```
id : id can be any string unique for that particular call flow object.
type: type defines the type of call flow object.("http" here)
url: url 
next_step: next_step defines the id of next call flow object to be executed in the flow.

#### Callback

Using this Call Flow Object one can set a callback for a time.

##### Example
```
{
			"id":<<some id>>,
			"type":"callback",
			"date":"2018-10-11",
			"time":"20:48:00",
			"timezone":"UTC",
			"entity":5263,
			"next_step":<<some other id>>
	}
```
id : id can be any string unique for that particular call flow object.
type: type defines the type of call flow object.("callback" here)
date: valid date in YYYY-MM-DD format.
time: time as per 24 hr clock
timezone: timezone (eg; UTC).
entity: voice id of either team or user

#### Connect

Using this Call Flow Object one can connect to PSTN or internal entity.

##### Example
```
{
		    "id":<<some id>>,
			"type":"connect",
			"number":"XXXXX",
			"caller_id":"YYYY",
			“next_step”:<<some id2>
	}
```
id : id can be any string unique for that particular call flow object.
type: type defines the type of call flow object.
number: this signifies the number to be connected to, could be PSTN number or an internal number
caller_id: Caller id for the call connect
next_step: next call flow object id.

#### Sample POST payload
```
{
	"cloud_id":"dev",
	"cloud_sid":"XXXXXXXX",
	"auth_token":"your_auth_token",
	"record":false,
	"flow":[
		{
			"id":"0",
			"type":"call",
			"number":"917507161643",
			"caller_id":"917507161643",
			"next_step":1
		},
		{
			"id":1,
			"type":"play",
			"text":"Hi, Welcome to Samespace. Press 1 for apples or press 2 for oranges.",
			"next_step":2
		},
		{
			"id":2,
			"type":"digits",
			"count":4,
			"flow":{
				"*":5
			 },
			 "variable":"account_id"
		},
		{
			"id":10,
			"type":"callback",
			"date":"2018-10-11",
			"time":"20:48:00",
			"timezone":"UTC",
			"entity":5263,
			"next_step":4
		},
		{
			"id":5,
			"type":"http",
			"url":"https://in01c005d.samespace.com/api/test/{{account_id}}",
			"flow":{
				"success":10,
				"fail":4
			 }
		},
		{
			"id":3,
			"type":"connect",
			"number":"16468107000",
			"caller_id":"12345678"
		},	
		{
			"id":4,
			"type":"connect",
			"number":"5263",
			"caller_id":"917507161643"
		}
	]
}
```
