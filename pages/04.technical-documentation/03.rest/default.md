---
title: REST API
slug: rest
---
# REST API
This page describes the REST APIs of the microservices.

!!! Please, take a look at these interfaces to discover available data objects. We strongly suggest to not inspect the database structures, since there could be different technologies in use and it would break the concept of microservices to directly use the databases of other MS.

## Drops
The API is documented as an [Open API v3](https://www.openapis.org/?target=_blank) specification in the [swagger.json](https://github.com/SOTETO/drops/blob/develop/swagger.json?target=_blank) file that is part of the [_Drops_ repository](https://github.com/SOTETO/drops?target=_blank).

The documentation is accessible by processing the RAW document ([https://raw.githubusercontent.com/SOTETO/drops/develop/swagger.json](https://raw.githubusercontent.com/SOTETO/drops/develop/swagger.json?target=_blank)) by an [Open API documentation renderer](https://openapi.tools/#documentation?target=_blank), like [MrinDoc](https://mrin9.github.io/OpenAPI-Viewer/?target=_blank).

### Authentification
Using the REST-API requires to authenticate the caller. First, you have to create a client by using the Drops frontend `/arise/#/oauth`. There you can add a new OAuth client that will also be used for the purpose of authentication of a REST request. You will have to add an `ID`, a `Secret`, a `Redirect URL` and `Grant Types`. You can ignore the `Redirect URL` and the `Grant Types`. This can be filled later, if you are implementing an [OAuth-Handshake](/how-to/oauth2-handshake).

The values of `ID` and `Secret` have to be used for the REST authentication. Just add them as values for the query parameter `client_id` and `client_secret`. 
For the example of having created an `ID` `test` with a secret `test`, the following cURL call will return all users, if it is running on `localhost`:
```
curl -X POST -H "Content-Type: application/json" -d '{}' "http://localhost/drops/rest/user?client_id=test&client_secret=test"
```
