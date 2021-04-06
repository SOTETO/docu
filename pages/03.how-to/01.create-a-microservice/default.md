---
title: Create a microservice
taxonomy:
    category: how-to
---
# Create a microservice
Creating a microservice should by possible by using the technologies you know. At the very beginning you have to setup a microservice environment that is very similar to the production environment.   
In contrast to a production system, the development of a new microservice requires to have access to 
* the APIs of the already existing microservices
* the NATS message broker
* the microservice routing proxy to add your new service to the architecture

You can setup the environment by using the [Heureka! console](https://github.com/SOTETO/heureka). Pull it from github and follow the [steps described by the readme](https://github.com/SOTETO/heureka#development-environment---create-a-new-microservice).
Afterwards, you can start your local applications as part of the microservice configuration. Including having access to the internal docker network.

## Available APIs
You are running a current microservice configuration, including [drops](https://github.com/SOTETO/drops) and [arise](https://github.com/SOTETO/arise). Most important the development of a new microservice is the usage of the [drops API](https://github.com/SOTETO/drops#webservice) to read users and their social structure and the [OAuth-Handshake to initiate a shared session](https://github.com/SOTETO/drops#oauth2-based-session-handshake).
In the development environment of Heureka!, the Drops API will be available at `localhost:9000` or `172.3.0.2:9000` (from the internal docker network; if you are using the default configuration).

Just as well, the [NATS](https://nats.io/) message broker is used by the Heureka! microservice configuration to exchange messages between the services. It is available at `localhost:4222` or `172.3.150.1:4222`.

## Integration in the architecture
The two main challenges to integrate a new service are 
* Having a [shared session](../../architecture/shared-session)
* Develop [UI elements that are reusable by other services](../../architecture/dUIfc#widgets)

Currently, the [shared session](../../architecture/shared-session) challenge is addressed by the Heureka!-OAuth2 handshake. The [OAuth2 handshake HowTo](../oauth2-handshake) explains the integration in the shared session. The [widgets HowTo](../widgets) explains the implementation of reusable UI elements and also there usage.

Additionally, you have to use the shared CSS by adding
```
<link rel="stylesheet" type="text/css" media="screen" href="/dispenser/css/vca.css">
```
to your HTML files.

!!! You can use basic page elements to replicate the corporate design that is already implemented. These are delivered as widgets by [vca-widget-base](https://github.com/SOTETO/vca-widget-base?target=_blank).

## Integration in the navigation
Cloning the [Heureka! console](https://github.com/SOTETO/heureka) will create the `<path-to-heureka-console>/.docker-conf/mode_dev/navigation/GlobalNav.json` and 
`<path-to-heureka-console>/.docker-conf/mode_dev/navigation/noSignIn.json`. Change the `GlobalNav.json` to add a menue entry for pages of your new microservice 
to add it to the menue after a successful login and to the `noSignIn.json`, if the entry should be available without an established session.

Subsequently, you have to reload your local microservice configuration by execute `rm` and `up` in the development environment in the Heureka! console.

## Integration in documentation
First of all: Use your repository and the readme of your microservice' project to document specific functions of your service. If you want to write an How-To or 
any other more general documentation, you can simply add a [Markdown](https://www.markdownguide.org/basic-syntax/) file to the `pages` directory of the [documentation 
repository](https://github.com/SOTETO/docu) of the Heureka! project.

The documentation uses [GRAV CMS](https://getgrav.org/) to generate this documentation. Thus, also the `images` and the `languages` in the repository are used and 
could be filled with content.
