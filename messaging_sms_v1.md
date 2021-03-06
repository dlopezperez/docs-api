# SmartMessaging API

## Table of Contents   
1. [Overview](#overview)
2. [API - Resources](#API)
	4. [/messaging/v1/sms/](#/messaging/v1/sms/)
	5. [/messaging/v1/sms/{messageId}](#/messaging/v1/sms/{messageId})
	6. [/messaging/v1/tokenvalidation/](#/messaging/v1/tokenvalidation/)
	7. [/messaging/v1/tokenvalidation/{msisdn}/{token}](#/messaging/v1/tokenvalidation/{msisdn}/{token})
3. [API - Error Handling](#ERROR)
4. [ClientId Authentication](#AUTHENTICATION)
5. [Examples](#EXAMPLES)


<div id='overview'/>
## Overview

The SmartMessaging API provides different resources around the topic mobile messaging. 

**Sms**
Resource to send a SMS. It is also possible to receive a status notification.

**Token Validation**
Simple  resource to validate a phone number by token.  To validate a number a well specified SMS message with included textual token will be sent to your customers mobile. Now the customer is able forward this token to your service (HTML from, Mail, Callcenter). After a quick validation by this resource you can be sure that phone number depends to your customer.

<div id='API'/>
## API - Resources

<div id='/messaging/v1/sms/'/>
### /messaging/v1/sms/

#### **post**: Send an SMS

##### **Request** 
###### **Headers**

 - client_id (Id to authenticate client)
 - Content-Type (application/json)
 - Accept (application/json)

###### **Body** (application/json)
<pre><code>{
	"from" : "Swisscom",
	"to" : "+4171234567",
	"text" : "Greetings from Swisscom!"
	"callbackUrl" : "http://notification.com/service"
}</code></pre>
<pre><code>{
    "$schema": "http://json-schema.org/draft-04/schema",
    "type": "object",
    "required": [
      "to", "text"
    ],
    "properties": {
        "from": {
            "description": "Sender name or number. Valid MSISDN number or alpha numeric text with max. 11 Characters",
            "type": "string"
        },
        "to": {
            "description": "Recipient number. A valid number starts with 0, 00 or + and must contain 7 to 15 digits. Local numbers (prefix 0) are handled with international code +41 by default.", 
            "type": "string"
        },
        "text": {
            "description": "Appears as text in SMS.",
            "type": "string"
        },
        "callbackUrl": {
            "description": "A valid callback URL (HTTP) for delivery notification. The notification call will be send (PUT method with the header.location which contains messageID) for each SMS status change",
            "type": "string"
        }
    }
}</code></pre>

##### **Success Responses**
###### **201 Response** 
<pre><code>no content</code></pre>

##### **Error Responses**

[400](#400), [401](#401), [403](#403), [404](#404), [405](#405), [406](#406), [429](#429), [500](#500), [503](#503), 

<div id='/messaging/v1/sms/{messageId}'/>
### /messaging/v1/sms/{messageId}

#### **get**: Get Status information for a SMS.

##### **Request** 
###### **Headers**

 - client_id (Id to authenticate client)
 - Accept (application/json)

###### **URI Parameters**
 - messageId (messageId of SMS. You will receive this is on notification request.)

##### **Success Responses**
###### **200 Response (application/json)** 
<pre><code>{
  "messageId":"0001",
  "status":"201",
  "description":"Delivered with delivery confirmation"
}</code></pre>

<pre><code>{
  "$schema": "http://json-schema.org/draft-04/schema",
  "type": "object",
  "required": [
    "messageId",
    "status"
  ],
  "properties": {
    "messageId": {
      "type": "string"
    },
    "status": {
      "type": "string"
    },
    "description": {
      "type": "string"
    }
  }
}</code></pre>

##### **Error Responses**

[401](#401), [403](#403), [404](#404), [405](#405), [500](#500), [503](#503), 

<div id='/messaging/v1/tokenvalidation/'/>
### /messaging/v1/tokenvalidation/

#### **post**: This call will start the SMS Token validation process by sending a validation token to a MSISDN. The validation token is a random string of characters that is sent to the user's mobile phone. After making this call, your application can then verify the user recieved the validation token by calling Validate SMS Token.

##### **Request** 
###### **Headers**

 - client_id (Id to authenticate client)
 - Content-Type (application/json)
 - Accept (application/json)

###### **Body** (application/json)
<pre><code>{
   "to":"+41791544781",
   "text":"Verification code: %TOKEN% \r\n Expires in 60 seconds.",
   "tokenType":"SHORT_ALPHANUMERIC",
   "expireTime":60,
   "tokenLength":6
}</code></pre>
<pre><code>{
    "$schema": "http://json-schema.org/draft-04/schema",
    "type": "object" ,
    "required": [
      "to"
    ],
    "properties": {
        "to": {
            "description": "Mobile number which should be verified. A valid number starts with 0, 00 or + and must contain 7 to 15 digits. Local numbers (prefix 0) are handled with international code +41 by default. ",
            "type": "string"
        },
        "text": {
            "description": "Appears as text in SMS. %TOKEN% is the placeholder for the generated token.",
            "type": "string"
        },
        "tokenType": {
            "description": "Describes what kind of token should be generated.",
            "enum": [
                "SHORT_NUMERIC",
                "SHORT_ALPHANUMERIC",
                "SHORT_SMALL_AND_CAPITAL",
                "LONG_CRYPTIC"
            ]
        },
        "expireTime": {
            "description": "Expire time of the token in seconds.",
            "type": "integer",
            "minimum": 0,
            "maximum": 86400
        },
        "tokenLength": {
            "description": "The size of the token. It is mandatory for all SHORT_* tokenTypes.",
            "type": "integer",
            "minimum": 1,
            "maximum": 12
        }
    }
}</code></pre>

##### **Success Responses**
###### **201 Response** 
<pre><code>no content</code></pre>

##### **Error Responses**

[400](#400), [401](#401), [403](#403), [404](#404), [405](#405), [406](#406), [429](#429), [500](#500), [503](#503), 

<div id='/messaging/v1/tokenvalidation/{msisdn}/{token}'/>
### /messaging/v1/tokenvalidation/{msisdn}/{token}

#### **get**: Validate the token sent to the MSISDN number by the Send SMS Token call. This call is made after the Send SMS Token call to validate the token sent to the end user via SMS.

##### **Request** 
###### **Headers**

 - client_id (Id to authenticate client)
 - Accept (application/json)

###### **URI Parameters**
 - msisdn (The token depending phone number. A valid number starts with 0, 00 or + and must contain 7 to 15 digits. Local numbers (prefix 0) are handled with international code +41 by default.)
 - token (Token to verify.)

##### **Success Responses**
###### **200 Response** 
<pre><code>{
    "validation": "SUCCESS"
}</code></pre>

<pre><code>{
    "$schema": "http://json-schema.org/draft-04/schema",
    "type": "object" ,
    "required": [
        "validation"
    ],                        
    "properties": {
        "validation": {
            "description": "Whether token validation was successful",
            "enum": [
              "SUCCESS",
              "FAILED"
            ]
        }
    }
}</code></pre>

##### **Error Responses**

[400](#400), [401](#401), [403](#403), [404](#404), [405](#405), [406](#406),  [500](#500), [503](#503), 

<div id='ERROR'/>
## API - Error Handling

#### Error Response Schema
<pre><code>{
  "$schema": "http://json-schema.org/draft-03/schema",
  "type": "object" ,
  "properties": {
    "uuid": {
      "description": "The UUID of the client software",
      "type": "string",
      "required": false
    },
    "status": {
      "description": "The http status code, e.g. 400",
      "type": "string",
      "required": true
    },
    "code": {
      "description": "The error code, e.g. INVALID_PARAMETER",
      "type": "string",
      "required": true
    },
    "message": {
      "description": "The error message",
      "type": "string",
      "required": true
    },
    "detail": {
      "description": "The error detail",
      "type": "string",
      "required": false
    }
  }
}  </code></pre>

#### Error Response Examples
<div id='400'/>
##### **400** (Bad Request. Invalid request.)
<pre><code>{
  "status": "400",
  "code": "INVALID_REQUEST",
  "message": "The request could not be understood due to malformed syntax. Correct the request syntax and try again.",
  "detail": ""
}</code></pre>
<div id='401'/>
##### **401** (Unauthorized. No permission to resource or invalid api key.)
<pre><code>{
  "status": "401",
  "code": "INVALID_AUTHENTICATION_CREDENTIALS",
  "message": "Authentication credentials missing or incorrect. Check that you are using the correct authentication method and that you have permission to the given resource and your app key is approved.",
  "detail": ""
}</code></pre>
<div id='403'/>
##### **403** (Forbidden. No permission for requested resource.)
<pre><code>{
  "status": "403",
  "code": "FORBIDDEN_RESOURCE",
  "message": "The server understood the request, but is refusing to fulfill it. Check that you have permission to the given resource.",
  "detail": ""
}</code></pre>
<div id='404'/>
##### **404** (Not Found. The server has not found anything matching the Request-URI.)
<pre><code>{
  "status": "404",
  "code": "INVALID_REQUEST_RESOURCE",
  "message": "The requested resource does not exist. Verify that the resource URI is correctly spelled.",
  "detail": ""
}</code></pre>
<div id='405'/>
##### **405** (Method Not Allowed. The resource is not accessible via the given method. For example, the client sent a POST request to a resource that only implements GET.)
<pre><code>{
  "status": "405",
  "code": "INVALID_REQUEST_METHOD",
  "message": "The resource is not accessible via the given method. Check the documentation or the Allow header for allowed methods.",
  "detail": ""
}</code></pre>
<div id='406'/>
##### **406** (Invalid Header Accept. The provided Accept-type is not equal the accepted. E.g. application/zip instead of text/json.)
<pre><code>{
  "status": "406",
  "code": "INVALID_HEADER_ACCEPT",
  "message": "The requested resource is only capable of generating content not acceptable according to the Accept headers sent in the request. Make sure the request has an Accept header with a media type supported by the API. Check the developer portal for supported media types.",
  "detail": ""
}</code></pre>
<div id='429'/>
##### **429** (Too Many Requests. Quota for this service has been exhausted. Message must contain further details.)
<pre><code>{
  "status": "429",
  "code": "EXPIRED_QUOTA",
  "message": "Your quota for this service has been exhausted. Try again later or contact support at developer-support@swisscom.com.",
  "detail": ""
}</code></pre>
<div id='500'/>
##### **500** (Internal Server Error. Server is misconfigured, or generic error message.)
<pre><code>{
  "status": "500",
  "code": "INTERNAL_SERVER_ERROR",
  "message": "The server encountered an unexpected condition which prevented it from fulfilling the request. Try again later or contact support at developer-support@swisscom.com.",
  "detail": ""
}</code></pre>
<div id='503'/>
##### **503** (Service Unavailable. The server is currently unable to handle the request due to temporary overloading or maintenance. Typically back-end is unavailable.)
<pre><code>{
  "status": "503",
  "code": "UNAVAILABLE_SERVICE",
  "message": "The server is currently unable to handle the request due to temporary overloading or maintenance. Try again later or contact support at developer-support@swisscom.com.",
  "detail": ""
}</code></pre>

<div id='AUTHENTICATION'/>
## ClientId Authentication
The SmartSms API requires ClientId authentication. The ClientId is part of the DevPortal ServiceKey, which are available after you booked the SmartSms service. For a valid authentication you have to set the ClientId as HTTP header "client_id". See also examples for details.

<div id='EXAMPLES'/>
## Examples
You can quickly make calls to the Swisscom APIs using [cURL](http://curl.haxx.se) in the command line. If you have not already installed cURL, use the [cURL Download Wizard](http://curl.haxx.se/dlwiz/?type=bin) to get the correct binary.

### Sending SMS
This first call shows how to send a SMS without optional parameter:
<code>
curl -ik -H "Content-Type: application/json" -H "client_id:%YOUR_CLIENT_ID%" -X POST "https://api.swisscom.com/messaging/v1/sms" -d "{\"to\": \"+417xxxxxxxx\", \"text\": \"hello world\"}"  
</code>

By default your registered mobile number is used as sender number. To display a different sender number you can set the "from" parameter. Notice: you need special permissions to use this parameter. Please contact our support team, to book this feature.   

If you need updates about the SMS state (e.g. SMS was received). You can set the "callBackUrl" parameter.  You will receive a HTTP PUT request on this URL, on any SMS state change.
<code>
curl -ik -H "Content-Type: application/json" -H "client_id:%YOUR_CLIENT_ID%" -X POST "https://api.swisscom.com/messaging/v1/sms" -d "{\"to\": \"+417xxxxxxxx\", \"from\": \"Swisscom\", \"text\": \"hello world\", \"callbackUrl\": \"http://mydomain.net/notification\"}"  
</code>

### Token Validation

If you want to verify the mobile number of a customer, you can simple send a SMS token with this request:
<code>
curl -ik -H "Content-Type: application/json" -H "client_id:%YOUR_CLIENT_ID%" -X POST " https://api.swisscom.com/messaging/v1/sms/tokenvalidation" -d "{\"to\":\"+417xxxxxxxx\",\"text\":\"Take this token: %TOKEN%\", \"tokenType\":\"SHORT_NUMERIC\",\"expireTime\":120,\"tokenLength\":6}"
 </code>

The customer receives the token on his mobile. After he types the token into your web form, you can do a verification with the following request:

<code>
curl -ik -H "Accept: application/json" -H "client_id:%YOUR_CLIENT_ID%" -X GET " https://api.swisscom.com/messaging/v1/sms/tokenvalidation/00417xxxxxxxx/%RECEIVED_TOKEN_GOES_HERE%"
</code>
