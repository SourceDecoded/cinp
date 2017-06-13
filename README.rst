Note:
  Development of this protocol has moved to http://github.com/cinp
  

CInP
===
Concise Interaction Protocol

Why CInP
=======

The relm of web protocols is full of many solutions to this problem, the two most popular are probably SOAP (Simple Object Access Protocol) and ReST (Representational State Transfer).

SOAP is by no means _simple_, consisting of getting and sending requests primarly over POST requests, and requring sometimes very large WSDLs to define the interface, this protocol is more a XML API tunnel over HTTP, very verbose, uncache able, tricky to load ballance, and almost completly ignorning the features of HTTP.

REST brought the us the ability to use HTTP as part of the protocol, making things cacheable, easy for Layer 7 appliances to assist in load balancing, etc.  However it is _state_ transfer, completly leaving out the ability to _call_ a method/procedure.  It also isn't formally fully defined, leaving room form diffrent takes on it's use.  Frequently it is implemented in not truely a State mode.

CInP's goal is to extend beyond REST in the spirit of REST to enable application developers to have some of the more _enterprise_ features of SOAP (like mainting business logic soly on the server), while using HTTP as a Protocal and not as a tunnel between endpoints.

Protcol Overview
================

Components of CInP
-----------------

Namespace:

  the HTTP path where models reside, this can be used to group models in applicable groups, and to assist the server in spliting parts of an application accross multiple servers with the help of a L7 Switch.

Model:

  A Collection of records using the same fields and callable methods.

Method:

  A Callable function/method of on a model.

Field:

  Data Column of a Model, or paramater of a Method

Data Flow
---------

CInP is designed in such a way that a CInP library can be wrtten on both the client and server side to handel the protocoal exposing the endpoints in an applicable method to the langage of the endpoints (simmaler to SOAP).  To help with this a HTTP Header is 'CInP-Version: X.X' This way the client/server libraries will know the CInP protocol level of each other.  There is also a seperate API version in a DESCRIBE Response so the application on the client end knows the API version of that paticalure service.  This api-version is specified at in each namespace enabling a cluster of servers to handle sperate parts of the name space and the client will be aware if diffrent parts of the service are running diffrent API versions.

Data Encoding
-------------

valid charaters for namespace, actions and object ids are: [a-zA-Z0-9\-_.!~*'], other charaters can be % hh encoded per usual URI convention. (This should match the javascript function encodeURI, with the exceptoin of '(' and ')' )

Curent implementations support only JSON at this time, however the Content-Type and Accept Headers are to be used to specify what encoding the endpoints would like to use.  JSON, XML, YAML are expented to be implemented.

Dates are encoded as yyyy-mm-dd hh:mm:ss in UTC
