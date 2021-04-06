---
title: Technical documentation
slug: technical-documentation
---
# Technical documentation
The microservice architecture of the Pool<sup>2</sup> requires to consider the concepts described in section [architecture](../architecture). For this purpose, we will describe needed technical documentation and best practices (as far as known) in this section.

## One microservice, one application?
Even though it is quite common to implement one service as one monolithic application, this is not a required limitation. Since we are implementing different technical parts of a socio-technical collaboration system, we should consider up-to-date guidelines for RIAs (Rich-Internet-Applications). Thus, the core microservices developed by VcA itself consist of at least two applications: (1) a backend server system and (2) a frontend client application. While the first handles the connection to the database, [OAuth2 integration](../how-to/oauth2-handshake), [OES](../architecture/oes) and implements some RESTful interfaces for [data exchange](../architecture/oes), implements the client application a modern user interface considering requirements like [Widgets](../architecture/dUIfc#widgets). In the end all microservices consist of multiple (at least two) different software projects.

Additionally to the two base projects, there are several more possibly needed applications to implement a complete microservice. At first, all [widgets](../architecture/dUIfc#widgets) are better isolated as distinguished software projects and repositories, since they have there own deployment flow and versioning using [NPM](https://www.npmjs.com/?target=_blank). Furthermore, we have implemented some plugins to simplify the setup of backend applications. Those plugins are also separated from the core backend systems for the purpose of deployment and generalization of requirements.

The following subsections list all software projects for each microservice implement for Heureka! itself:

### Drops

| Category | Name | URL | Purpose |
| -------- | ---- | --- | ------- |
| Backend | drops | [https://github.com/SOTETO/drops](https://github.com/SOTETO/drops?target=_blank) | The server-side backend system written using [Scala / Play 2.4.x](https://www.playframework.com/documentation/2.4.x/Home?target=_blank). |
| Frontend | arise | [https://github.com/SOTETO/arise](https://github.com/SOTETO/arise?target=_blank) | The client-side frontend system written using [Vue.js](https://vuejs.org/?target=_blank). |
| Widget | vca-widget-user | [https://github.com/SOTETO/vca-widget-user](https://github.com/SOTETO/vca-widget-user?target=_blank) | A widget that implements user interface elements to search, select and show users. It is also written using [Vue.js](https://vuejs.org/?target=_blank). |
| Plugin | play2-oauth-client | [https://github.com/SOTETO/play2-oauth-client](https://github.com/SOTETO/play2-oauth-client?target=_blank) | A server-side backend plugin for [Play 2.5](https://www.playframework.com/documentation/2.5.x/Home?target=_blank) and [Play 2.7](https://www.playframework.com/documentation/2.7.x/Home) that serves as a [OAuth2 client for the OAuth2 Provider](../how-to/oauth2-handshake) that has been implemented in the backend of _Drops_. |

### Stream

| Category | Name | URL | Purpose |
| -------- | ---- | --- | ------- |
| Backend | stream-backend | [https://github.com/SOTETO/stream-backend](https://github.com/SOTETO/stream-backend?target=_blank) | The server-side backend system written using [Scala / Play 2.7.x](https://www.playframework.com/documentation/2.7.x/Home?target=_blank). |
| Frontend | stream-frontend | [https://github.com/SOTETO/stream-frontend](https://github.com/SOTETO/stream-frontend?target=_blank) | The client-side frontend system written using [Vue.js](https://vuejs.org/?target=_blank). |

## Microservice-architecture consists of microservices and nothing else?
Getting this motor running requires more than a set of loosely coupled microservices. According to the concept of [Dynamic UI Fragment Composition](../architecture/dUIfc) every microservice needs to use some centralized UI elements, like a shared CSS library or a corporate navigation. Thus, we have introduced the following shared services and software projects:

| Category | Name | URL | Purpose |
| -------- | ---- | --- | ------- |
| CLI | heureka | [https://github.com/SOTETO/heureka](https://github.com/SOTETO/heureka?target=_blank) | |
| Dockerfile | grav-dockerfile | [https://github.com/SOTETO/grav-dockerfile](https://github.com/SOTETO/grav-dockerfile?target=_blank) | |
| Content | docu | [https://github.com/SOTETO/docu](https://github.com/SOTETO/docu?target=_blank) | |
| Backend | dispenser | [https://github.com/SOTETO/dispenser](https://github.com/SOTETO/dispenser?target=_blank) | Handles a database to instantiate a navigation and hosts a shared CSS library that can be used by all microservices. |
| Widget | vca-widget-navigation | [https://github.com/SOTETO/vca-widget-navigation](https://github.com/SOTETO/vca-widget-navigation?target=_blank) | A widget that implements UI elements for a navigation. It is written using [Vue.js](https://vuejs.org/?target=_blank). |
| Widget | vca-widget-base | [https://github.com/SOTETO/vca-widget-base](https://github.com/SOTETO/vca-widget-base?target=_blank) | A widget that implements basic UI elements that can be used by all frontend applications. It is written using [Vue.js](https://vuejs.org/?target=_blank). |
| Backend | webapps-drops-oauth | [https://github.com/Viva-con-Agua/webapps-drops-oauth](https://github.com/Viva-con-Agua/webapps-drops-oauth?target=_blank) | A server-side backend system that uses the play2-oauth-client to handle the [OAuth2 based authentication](../how-to/oauth2-handshake) of the Pool<sup>2</sup> shared session concept. It can be used for WebApps with no or external backend implementation to handle the authentication. |

## Communication between Microservices?
According to the concepts of [Business Object Exchange](../architecture/oes), you can find here the technical documentation of all RESTful APIs and of the NATS service that is used to implement the OES.
