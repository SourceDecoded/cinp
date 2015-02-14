# CIP
Concise Interaction Protocol


* Lightweight: few rules, much freedom
* Interaction: do more than push and pull object state - interact with Types and instances
* Protocol: client/server communication


CIP Protocol Summary
=====================

This protocol is very much like REST with the ability to call methods, and while we are at it, use HTTP verbs that a little more descriptive and make things over all more explicitly defined.  All Requests/Responses are encoded acording to the 'Accepts' HTTP Header and the resulting encodeing will be in the 'content-type' header.  General "header" information (ie, list offsets, describe control info, etc) will be HTTP headers.

HTTP Paths
----------

<root path>::

  root to all parts of the protocol name space, ie: /api/v1

<root path>/<model path>::

  Path to a particular model, should be singular and '/' delimited for trees/namespaces of models, ie: /api/v1/job or /api/v1/provisioner/job

<root path>/<model path>:<object id>: ::

  Path to an instance of an object/model, ie: /api/v1/job:5:

<root path>/<model path>[:<object id>:](<method name>)::

  Call method
  ie: /api/v1/job:5:(delete) /api/v1/device(add)
  If Primary key is not specified, then it is a call to a static method.
  Method parameters are in the request body.

HTTP Verbs/Methods
------------------

LIST::

  List objects
  Optional Query is sent in the request body
  Returns list of object paths
  Number of results and result start offset controlled by http headers, record count and returned position also in headers

GET::

  Get Single object
  Returns Object detail

DELETE::

  Delete Single object
  No Return body, if success will return 200

NEW::

  Create new object
  returns object detail of newly created object
  The uri of the newly created object will be a HTTP header

COPY::

  Copy Existing object to a new one
  returns object detail of newly created object
  The uri of the newly created object will be a HTTP header

UPDATE::

  Update Values in Existing object
  returns object detail of newly created object

DESCRIBE::

  Describe namespace, model and it's actions
  See Describe section

CALL::

  Call an object method
  return return value of method

HTTP Return codes
-----------------

200::

  OK/Deleted/Results returned

201::

  Created/Saved

400::

  Bad Request - some type of problem with the request, in this format:
  {
    ["error": "<general request error>"],
    ["fields": < list of key( field name) and value( message) for each field with errors> ]
  }

401::

  Not Authorized (special exception for authentication)

404::

  Not Found

500::

  Exception, with the exception detail in the response body.  Only for when something really breaks, hopfully all expected errors are handeled above.
  {
    "name": "<exception name>",
    "description": "<exception description>"
  }

Describe
--------
If called on a path, returns a list of available sub-namespaces and models available at that level.  If called on a model, returns the model field definition and a list of the actions.  If called on an action, returns the actions details.  The type of the uri described should be HTTP header 'type', and be one of 'namespace', 'model' or 'action'.  The URI of the target will be an HTTP Header.

namespace::

  { 
    "type": "namespace",
    "api-version": <application's api version at this node>,
    "protocol-version": <CIP protocol version "0.1" for now>,
    "doc": <namespace documentation>,
    "children": [ <list of sub dir/namespaces (relative names)> ],
    "models": [ <list of the models at this level (relative names)> ]
  }

model::

  {
    "type": "model",
    "doc": <model documentation>,
    "list-fields": [<paramater spec list>],
    "fields": [<field list>],
    "actions": [<method list>],
  }

  NOTE: there is not action overriding, the name is unique. per model

action::

  {
    "type": "action",
    "doc": <action documentation>,
    "paramater-list": [<paramater spec list>],
    "static": <boolean flag>,
    "return-type": <paramater spec>
  }

Field Spec::

  { "name": "<field name>", "mode": "[R|W|RW|RC]<defaults to RW (CW is writeable during create)>", "type": "<field type">, "required": <true|false>, ["length": <char length>] }
  
Parameter Spec::

  { "name": "<name>", "type": "<field type>" }

Field Types
-----------
For the most part JSON encoding is enough:

String::

 "< valid string charaters>"

DateTime::

 "yyyy-mm-ddThh:mm:ss.hhhZ"  ie: "2012-04-23T18:25:43.511Z"  in UTC

Boolean::

 true|false

Integer::

 [0-9]*  ie: 42

Float::

 [0-9]*.[0-9]* or [0-9]*e[-]?[0-9]*


File::

  TBD

Model::

  "<root path>/<model path>:<object id>:" (ie the url of object)
  - or -
  { "uri": "<root path>/<model path>", "values": { <key>:<value>.... } }
  This second way is how you would handle structs, embeded objects, it could also conceivably be used to create a related object from another, but this method could get less transparent.

Authentication
--------------
Use of the HTTP Header "Auth-Token" will be used to send back a token representing an authenticated session.  The means by which the Token is given to the client is not specified.  It might be pre-shard via another system, or via a set of designated actions.

Cache-Control
-------------
Cache control will be handled by normal HTTP cache control HTTP header.  NOTE: only GET and DESCRIBE can be cached.  All other HTTP Methods shall cary a no-cache header.  LIST Requests will not have any parameter information in the URI to enable reasonable caching.
