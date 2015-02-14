CIP
===
Concise Interaction Protocol

Why CIP
=======

The relm of web protocols is full of many solutions to this problem, the two most popular are probably SOAP (Simple Object Access Protocol) and ReST (Representational State Transfer).

SOAP is by no means _simple_, consisting of getting and sending requests primarly over POST requests, and requring sometimes very large WSDLs to define the interface, this protocol is more a XML API tunnel over HTTP, very verbose, uncache able, tricky to load ballance, and almost completly ignorning the features of HTTP.

REST brought the us the ability to use HTTP as part of the protocol, making things cacheable, easy for Layer 7 appliances to assist in load balancing, etc.  However it is _state_ transfer, completly leaving out the ability to _call_ a method/procedure.  It also isn't formally fully defined, leaving room form diffrent takes on it's use.  Frequently it is implemented in not truely a State mode.

CIP's goal is to extend beyond REST in the spirit of REST to enable application developers to have some of the more _enterprise_ features of SOAP (like mainting business logic soly on the server), while using HTTP as a Protocal and not as a tunnel between endpoints.
