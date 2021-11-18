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

You can setup the environment by using the [Heureka! console](https://github.com/SOTETO/heureka). Pull it from github and follow the [steps described by the readme](https://github.com/SOTETO/heureka#how-to-add-a-new-microservice) (How to add a new microservice). 
Afterwards, you can start your local applications as part of the microservice configuration. You will have access to the internal docker network.

## Available APIs
You are running a current microservice configuration, including [drops](https://github.com/SOTETO/drops) and [arise](https://github.com/SOTETO/arise). Most important the development of a new microservice is the usage of the [drops API](https://github.com/SOTETO/drops#webservice) to read users and their social structure and the [OAuth-Handshake to initiate a shared session](https://github.com/SOTETO/drops#oauth2-based-session-handshake).
Using the default configuration of a newly created microservice, the Drops API will be available at `172.3.0.2:9000` from the internal docker network or at `localhost:9000/drops` sending the request through the nginx proxy.

Just as well, the [NATS](https://nats.io/) message broker is used by the Heureka! microservice configuration to exchange messages between the services. It is available at `172.3.150.1:4222` from the internal network or at `localhost:4222` sending the request through the nginx proxy.

## Integration in the architecture
The two main challenges to integrate a new service are 
* Having a [shared session](../../architecture/shared-session)
* Develop [UI elements that are reusable by other services](../../architecture/dUIfc#widgets)

Currently, the [shared session](../../architecture/shared-session) challenge is addressed by the Heureka!-OAuth2 handshake. The [OAuth2 handshake HowTo](../oauth2-handshake) explains the integration in the shared session. The [widgets HowTo](../widgets) explains the implementation of reusable UI elements and also there usage.

You can develop the UI by the technologies you know! Thus, you will not be limited and can use the framework of your choice. Just understand and use the [existing widgets](../../architecture/dUIfc#widgets) to extend your UI by elements provided by the other microservices. Additionally, you can use a basic CSS to style your microservice interface the same way as the rest of the application and you can [implement your own reusable user interface elements](../widgets) as widgets that can be used by other microservice developers.

### Docker integration
The setup of a new microservice as part of the Heureka! architecture ist described in the [README of the Heureka! CLI](https://github.com/SOTETO/heureka#how-to-add-a-new-microservice).

If any additional service is required (e.g. database), just set it up as a docker container and initiate a connection by its IP address.

### Basic styling
Additionally, you have to use the shared CSS by adding
```
<link rel="stylesheet" type="text/css" media="screen" href="/dispenser/css/vca.css">
```
to your HTML files.

!!! You can use basic page elements to replicate the corporate design that is already implemented. These are delivered as widgets by [vca-widget-base](https://github.com/SOTETO/vca-widget-base?target=_blank).

### Showing the navigation
The navigation in the default layout is shown as part of a header and a footer. These elements are implemented as widgets ([heureka-widget-navigation](https://github.com/SOTETO/heureka-widget-navigation-2021?target=_blank)) and can be integrated into the page that is rendered by your application. See the [HowTo widgets](../widgets) article for a detailed explanation. Subsequently is shown an example for a [vue.js](https://vuejs.org/?target=_blank) application:
```
<template>
    <div id="ms-frontend-app">
        <WidgetTopNavigation />
        <div id="content">
            <router-view/>
        </div>
        <WidgetBottomNavigation />
    </div>
</template>

<script>
    import { WidgetTopNavigation, WidgetBottomNavigation } from 'heureka-widget-navigation-2021';
    import 'heureka-widget-navigation-2021/dist/heureka-widget-navigation-2021.css'
    
    export default {
        name: 'ms-frontend-app',
        components: { WidgetTopNavigation, WidgetBottomNavigation }
    }
</script>

<style lang="less">
    #arise {
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: #2c3e50;
        flex: 1;
        display: flex;
        flex-direction: column;
        min-height: min-content;
    }
    #content {
        flex-grow: 1;
        flex-shrink: 0;	
        display: flex;
        overflow: auto;
    }
</style>
```

### Integration in the navigation
Cloning the [Heureka! console](https://github.com/SOTETO/heureka) will create the `<path-to-heureka-console>/microservices/ms-<name>/.docker-conf/navigation/GlobalNav.json` and 
`<path-to-heureka-console>/microservices/ms-<name>/.docker-conf/navigation/noSignIn.json`. Change the `GlobalNav.json` (menue after a successful login) to add a menue entry for pages of your new microservice and to the `noSignIn.json`, if the entry should be available without an established session.

Subsequently, you have to reload your local microservice configuration by execute `rm` and `up` in your microservices  environment of the Heureka! console (considering the default implementation of your `env.sh`).

## Integration in documentation
*First of all: *Use your repository and the readme of your microservice' project to document specific functions of your service. If you want to write an How-To or 
any other more general documentation, you can simply add a [Markdown](https://www.markdownguide.org/basic-syntax/) file to the `pages` directory of the [documentation 
repository](https://github.com/SOTETO/docu) of the Heureka! project.

The documentation uses [GRAV CMS](https://getgrav.org/) to generate this documentation. Thus, also the `images` and the `languages` directories in the repository are used and could be filled with content.