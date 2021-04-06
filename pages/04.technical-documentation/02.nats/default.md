---
title: NATS Messages
slug: nats
---
# NATS Messages
Regarding to the [Business Object Exchange](../../architecture/oes) concept, microservices have to be informed about data updates. The Pool<sup>2</sup> uses a [NATS message broker](https://nats.io/?target=_blank) instance to implement a simple provider-subscriber model. Every microservice can publish updates of its business objects and subscribe to updates of data that is handled by other microservices. The following sections show for every microservice its published messages and all subscriptions of this microservice.

## Drops - Published Messages
[Drops](https://github.com/SOTETO/drops?target=_blank) publishes the following messages:

| Subject | Message body | Description |
| ------- | ------------ | ----------- |
| LOGOUT | UUID | Will be published, if a user cancels their session. |
| CREATE | UUID | Will be published, if a user has been created. |
| UPDATE | UUID | Will be published, if a user has been updated. |
| DELETE | UUID | Will be published, if a user has been deleted. |

## play2-oauth-client - Subscribed Messages
The [play2-oauth-client](https://github.com/SOTETO/play2-oauth-client?target=_blank) subscribes the following messages:

| Subject | Message body | Description |
| ------- | ------------ | ----------- |
| LOGOUT | UUID | If a message will be received, the contained UUID will be added to a list of user IDs whose sessions will be killed on their next incoming request. |

