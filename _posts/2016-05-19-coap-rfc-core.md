---
layout: post
title: "Constrained Application Protocol (CoAP)"
date: 2016-05-19
---
### [RFC 7252](https://tools.ietf.org/html/rfc7252)

* CoAP is designed for Machine to Machine based applications. e.g Smart energy, building automation
* CoAP provides 
    * request/response interaction model between application endpoints
    * Built in discovery of services and resources
    * Contains concpets of Web URI's and Internet media types

### Goals and features of CoAP
* CoAP CoRE(Constrained RESTful Environments) aims to realize rest architectecture for constrained nodes where constrained nodes are typically low in memory (8 bit micro controllers) and network (e.g 6LoWPAN - low Power Wireless personal area Networks)
* Design a generic web protocol fulfilling  M2M requirements(dealing with message delivery to devices, scalability, failure handling etc Look at M2M requirments [here](http://www.etsi.org/deliver/etsi_ts/102600_102699/102689/01.01.01_60/ts_102689v010101p.pdf) ) in this contrained environment. 
* UDP binding with optional reliability supporting unicast and multicast
* asynchronous message exchange
* Low header overhead and parsing complexity
* URI and content type support
* Proxy caching capability
* stateless HTTP mapping ,with proxies providing access to coap resources over HTTP. 
* security binding to DTLS (Datagram transport Layer Security)

### Terms to remember [RFC7228](https://tools.ietf.org/html/rfc7228):
* EndPoint : Refers to the node/ constrained node
* Sender / Source Endpont / Client : the originating node/endpoint for the message or the destination endpoint of a response 
* Recepient / Destination Endpoint : the destination node/endpoint for the message or the originating endpoint of a request.
* Origin Server: the node where the resource resides. 
* Intermediry : CoAP endpoint acting as both a server and a client. e.g. Proxy
* Cross Proxy : proxy that translates to different protocols .eg. http to coap / coap to http
* Confirmable message : messages requiring acknowledgement (can be of type Acknowledgement or Reset)
* Reset Message : indicates that a message was received but with some context missing. 
* PiggyBacked Response : response is included in the ACK itself
* Separate Response: When a confirmable message is ACK'ed with a empty response (as the server does not have the response right away), a separate message is sent using another message exchange. 

### About Coap
