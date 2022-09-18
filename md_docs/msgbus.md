# Message bus 

The principle of request-reply is at the core of web-applications since they exist.  
It is great but comes with some limitaions:

1. Data displayed is not realtime: After your request for a data, if it changes server-side, your app., your user won't know until the next request.
2. No server events: You server can only send something to your user when your user takes the initiative.
3. No peer-to-peer events: a user app. cannot communicate with another user app. (at least not realtime, and not without using your back-end server wich has probably other things to do than route user-to-user messages).

Attempts to work-around those inherent limitations exist, but usually involve polling (or long-polling) systems, which put a serious additional strain on your backend server. These are therefore always of a limited use, and not really scallable.

Without questionning the request-reply model, the use of a realtime messagebus alongside can prove very complementary.

This can be done with technologies which are now part of all major browsers, and mature enough to be deployed in secure, large, production environments. 
- Secure Websockets on top of HTTPS (same domain, same port 443), 
- WebWorkers : Running in a different browser thread than your js app, their data is inaccessible from it or from the console.
- Server-side messaging brokers like Redis, RabbitMQ, MQTT, Active MQ and many other, offer stable, fast, scalable message routing and queuing.

![Message bus...](img/MessageBus.png "Message bus")

## The messageBus plugin of Sparc :
The message bus plugin of Sparc, ogether with it's server Node.js counterpart and a Redis-broker do exactly that : 
It basically allows your browser app. to connect to Redis Pub-Sub channels, server-side.

It then becomes possible for your browser app. to send realtime messages to another user, or group of other users, or to any server-side back-end application or micro-service. But more interestingly, any server-side back-end application or micro-service can send realtime data or events to any browser app., user (maybe using your app in several browsers or tabs), or groups of users.

The messageBus plugin for Sparc takes care of all communication gory details (Ronnections, keep-alive, auto-reconnection, subscriptions management,...).
It offers a simplified yet powerfull API to tour app.
