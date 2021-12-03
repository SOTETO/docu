---
title: Business Object Exchange
slug: oes
---
# Business Object Exchange
One microservice is responsible for (multiple) managed data objects (MO) that have to be transmitted to other microservices.
This concept describes the exchange of MOs that bases on RESTful webservices as mainly implemented by microservice architectures (Dragoni et al. 2017). 

REST uses the HTTP verbs for the basis operations of the CRUD principle (`Create`, `Read`, `Update` and `Delete`). Thus, the creation of instances uses `POST`, while `GET` is used to call for instances and an update uses `PUT` (as well as the parallel creation of multiple instances). Delete uses the descriptive verb `DELETE` (Rodriguez, 2008).

RESTful Webservices are stateless: Requests contain all required information to answer the request and responses can describe links to other resources. Furthermore, responses can be cached.
Additionally, webservices are using URIs following a directory structure: A hierarchy of (sub) pathes is extending a root node and query strings should be avoided. Moreover, the data should by human readable, using formats like XML, JSON or both in parallel (Rodriguez, 2008).

Considering the expected loosely coupling of the Heureka! architecture, the communication between microservices should follow the concept of choreography, instead of orchestration (Nikaj and Weske, 2016; Nikaj et al., 2016). Thus, the communication between two services requires only a direct communication channel between both. Therefore, a network of microservices results and there are only dependencies between the directly communicating nodes.

## Selection of data
It is a challenge for software developers of microservices to decide which data should be provided in RESTful webservices.
Thus, MOs are categorized: (1) Own MOs that are saved and managed by the microservice itself and (2) pulled MOs that have been received from other microservices.
Only the first category is relevant, that can be splitted in (1.1) pushed MOs and (1.2) internal MOs.
Only the type (1.1) of the pushed MOs are relevant for other microservices. 

In some special cases, additional information for pulled MOs are created and managed. If these information should become pushed by a RESTful webservice can be decided by the escalation guideline: A MO has to be always complete in the network. Thus, data extensions are handled only as internal MOs.

## Escalation guideline
The guideline defines rules for the change of pushed and pulled MOs. The following table describes a rule and a reasoning for each case:

| Case | Rule | Reasoning |
| ---- | ----- | ---------- |
| Extend a pushed MO | Pushed MOs can be extended by increasing the version number. Furthermore, the old versions have to be still accessible by the old version numbers. | Conflicts with pulled MOs have to be avoided. Thus, the software developers should be responsible by themselves regarding the robustness of their system. This becomes possible by the versioning of data formats. |
| Attributes of a pushed MO are altered | Changes can become implemened and have to be provided by a new version number. The previous data description has to be accessible by the previous version number. | Conflicts with pulled MOs have to be avoided. Thus, the software developers should be responsible by themselves regarding the robustness of their system. This becomes possible by the versioning of data formats. |
| Attributes of a pushed MO will be removed | Attributes can be deleted and have to be provided by a new version number. The previous data description has to be accessible by the previous version number. | Conflicts with pulled MOs have to be avoided. Thus, the software developers should be responsible by themselves regarding the robustness of their system. This becomes possible by the versioning of data formats. |
| A pulled MO will be extended | A collaboration process with the developers of the providing microservices has to be initiated. If the extension is also required by other services, a new requirement for the providing microservices has to be created. If this is not the case, the new requirement has to be implemented as an internal MO in the receiving microservice. | The technical communication should be as simple and managable as possible. Thus, MOs should not be dispersed on different services. |
| Attributes of a pulled MO must be changed | A collaboration process with the developers of all receiving microservices has to be initiated. If the change is accepted by the developers of multiple receiving microservices, a new requirement for the providing microservice has to be formulated and a new version should be released. If the change is only required by the originally initiating microservice, a solution has to be implemented in this microservice. | Conflicts with pulled MOs have to be avoided. Thus, the software developers should be responsible by themselves regarding the robustness of their system. This becomes possible by the versioning of data formats. |
| Attributes of a pulled MO are not required anymore | See previous solution (Attributes of a pulled MO have to be changed) | See reasoning before (Attributes of a pulled MO have to be changed) |

## Life cycles - Object event system
If the provided data of a microservice is changed, other services may have to react. For example, users are often associated to other data objects. If a user deletes or deactivates his / her account, it is required for other microservices to detect this change and to delete the corresponding association.

Thus the *Object Event System (OES)* ist introduced as ab additional communication layer. A modern message broker ist used to push updates of data to other services that have subscribed for these data. 
Messages describe the data of change as well as the operation (`delete` or `update`). Afterwards, receiving microservices can request the data object again, to update all references or delete these references.

The messages have the following format:
```
{
  "sender": "microservice-uuid",
  "action": "action-id",
  "object": "object-uuid",
  "type": "object-type",
  "timestamp": 123456789
}
```

While the attribut `sender` identifies the providing microservice, `action`  describes the altering operation. `action` can have four different values: `delete`, `update`, `deactivated` or `activated`. In difference to `deactivated`, `delete` describes the complete deletion of the object. Thus, it will not be possible to request the data object again. `deactivated` means that the object is still saved in the database, but not actively used anymore. The `action` `activated` can be used to reactivate deactivated objects. 
The attribute `object` identifies the addressed object using an `UUID`. `type` supports the contextualization of the object and describes the type. The unix `timestamp` marks the time the operation has been executed.

Neben Herausforderungen hinsichtlich der [Authentifizierung unter den Microservices](../action-based-user-rep), ist für die Realisierung von RESTful Webservices keine
Registrierung des Beziehenden MS beim Haltenden MS notwendig. Daher ist es dem Haltenden MS nicht möglich den Beziehenden MS zu bestimmen und
diesem so Nachrichten zu senden. Diese technische Herausforderung ließe sich mittels eines Bus-Systems angehen, welcher allerdings eine zentrale
Komponente darstellt und somit das Ziel der Architektur und des Systems verfehlt. Somit müssen andere Lösungen gefunden werden. Wir verwenden
daher einen Publish-Subscriber Mechanismus, mittels dem Nachrichten versendet werden. Jeder Beziehenden MS registriert sich bei einen Haltenden MS
und so kann dieser anschließend die Nachricht an die betreffenden MS versenden. Es wird zudem angenommen, dass ein Beziehender MS bzgl. eines Haltenden MS 
diese Rolle für die gesamte Dauer des Betriebs einnimmt und nicht alleine für einen beschränkten Zeitraum. Daher ist eine Abmeldung nicht
notwendig. Um jedoch auch Entwicklungszyklen beachten zu können und bei Updates eines Beziehenden MS ggf. die Notwendigkeit und die Schnittstelle
zu einem Haltenden MS entfällt, sollen Haltende MS andere MS dann aus ihrer Liste der Beziehenden MS entfernen, wenn auf eine an diese versendete
Nachricht der Statuscode `404` geantwortet wird.

## Kommunikation
RESTful Webservices basieren auf Endpunkten die über eine URI erreicht werden können. Damit also verschiedene Microservices Daten austauschen
können, müssen diese die Endpunkte der jeweils anderen Services kennen. Die Endpunkte, die ein Service bereitstellt werden in der Dokumentation des
Services erfasst und können dort von anderen Entwickler-Teams eingesehen werden. Ein Microservice kann also einfach Daten von einem anderen
Service abfragen, in dem er eine Verbindung zu einem Endpunkt herstellt. Auf dieser Kommunikationsebene kennt also jeder Microservice genau 
die anderen Services, von denen er Daten abfragt.

Allerdings kann es notwendig sein, dass bereitstellende Services mit konsumierenden Services Kontakt aufnehmen (siehe Lebenszyklen - Object Event
System). Für diesen Zweck müssen die bereitstellenden Services entsprechende Endpunkte der konsumierenden Services kennen. Somit müssen sich
die konsumierenden Services bei den bereitstellenden Services registrieren. Dafür stellt jeder bereitstellende Service den Endpunkt

```
/services/register
```

zur Verfügung, zu dem folgende Informationen via POST übermittelt werden können:

```
{
"name": "microservice-id",
"oes": "/pfad/zum/oes/endpunkt"
}
```

Ein Microservice ist damit in der Lage PUSH-Nachrichten im Sinne des OES zu versenden. Sollte es notwendig sein den Host zum Pfad zu ermitteln, kann
auf die entsprechenden [Header Felder](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) zurückgegriffen werden.

## Sicherheit
Im ersten Schritt der Umsetzung erfolgt die Kommunikation einzig zwischen Docker-Containern und ist daher von einem externen Netzwerk abgeschottet. 
Allerdings muss dies bei der Erstellung und beim Deployment der Docker-Container beachtet werden, so dass die Messages nicht von einem Services über 
Port 80 des hostenden Servers ins Internet und von dort wieder zurück zum Port 80 des selben Servers gesendet werden, nur um dort einem anderen 
Docker-Container und dem dort laufenden Services zugesandt zu werden.

## Konsistenz der Schnittstellen
Die Arbeit der Software Entwickler wird sehr vereinfacht, wenn die Schnittstellen konsistent entworfen sind. Um dies zu erreichen, sollen die Schnittstellen
folgende Guidelines beachten und bereits bestehenden Schnittstellen nachfolgend hinsichtlich ihrer jeweiligen Syntax beschrieben werden.

Den generellen Ansprüchen an RESTful Interfaces (Rodriguez, 2008) folgend, gilt es zu klären, wie vorhandene Daten beim Abrufen gefiltert werden
können. RESTful Interfaces sollen für die Abfrage von Daten via HTTP GET ermöglichen. Es stellt sich also die Frage, wie Queries angemessen
spezifiziert werden können, ohne den Body eines HTTP Requests oder einen komplexen Query-String zu verwenden. Microservices sollen hierfür an
einem GET Request zwei Parameter bereitstellen:

Einen Filter, welcher ein stringified JSON mit Beschreibungen sogenannter partiell definierter Entitäten enthält. Dabei handelt es sich um Datencontainer,
welche die selbe Struktur aufweisen wie die vom MS verwalteten Entitäten. Im Unterschied zu diesen sind in partiell definierten Entitäten allerdings alle
Werte optional. Dies bedeutet beispielsweise, dass für das Objekt der Nutzer in Drops eine partiell definierte Entität nur eine Email oder nur einen
Vornamen oder nur diese beiden Attribute enthalten kann. Zusätzlich können die Werte dieser Attribute ebenfalls partiell definiert sein. So kann etwa der
Vorname eines Nutzers als String "Pet*" angegeben werden, wobei das "*" als Wildcard Operator fungiert und sowohl die Werte "Pete", als auch "Peter"
oder ähnliches zu dem partiellen Wert äquivalent sind. Auf diese Weise lassen sich Parameter zum filtern beschreiben.

Zusätzlich müssen diese partiellen Werte zueinander in Relation gebracht werden können. Es macht beispielsweise einen Unterschied ob ich nach
Nutzern mit dem Namen "Pet*" UND der Email "test@test.com" suche, oder nach Nutzern mit dem Namen "Pet*" ODER der Email "test@test.com". Diese
Relationen werden über den Wert der übergegebenen Query beschrieben, welcher als String angegeben wird. Er besteht aus einer Menge syntaktischer
Elemente (Strukturelemente wie Klammern, und Operatoren) sowie die Bezeichner der Attribute der partiell definierten Entität enthalten dürfen. Die Menge
erlaubter Operatoren wird nachfolgend definiert:

Binäre Operatoren (beziehen sich auf Relationen zwischen den Attributen der partiell definierten Entität):

* `_OR_` - logisches ODER
* `_AND_` - logisches UND

Unäre Operatoren (beziehen sich auf Relationen zwischen den Attributen und ihren jeweiligen Werten in der partiell definierten Entität):

* `!` - logisches NOT
* `=` - Gleichheit
* `<` - Kleiner
* `>` - Größer
* `<=` - Kleiner-Gleich
* `>=` - Größer-Gleich
* `!=` - Ungleich
* `IN` - Gesuchte Entität hat als Wert an dem Attribut einen Wert, welcher in einer Liste an dem Attribut in der partiell definierten Entität angegeben wurde.
* `BETWEEN` - Gesuchte Entität hat als Wert an dem Attribut einen Wert, welcher zwischen zwei Werten liegt, die an dem Attribut in der partiell definierten Entität angegeben wurden.
* `LIKE` - Gesuchte Entität hat als Wert an dem Attribut einen Wert, äquivalent zu dem partiell definierten Wert an dem Attribut der partiell definierten Entität.

Beispiel: `first_name.LIKE_AND_email.=_AND_(crew.name.LIKE_OR_placeOfResidence.LIKE)`

!!! Zu beachten ist, dass in der Query Attributpfade mittels `.` beschrieben werden können und die unären Operatoren stets an einen Attribut(-pfad) mittels `.` konkateniert werden.

!!! Keys welche aus mehreren Wörtern zusammen gesetzt sind, werden in der cammel case Notation dargestellt, dabei wird sich an den JSON Style Guid von Google orientiert (Google, 2007).

!!! Die konkrete Implementierung aller bisher vorhandenen Microservices kann im Bereich REST APIs dieser Dokumentation eingesehen werden.

## References
|   |   |
| - | - |
| (Dragoni et al., 2017) | N. Dragoni et al., “Microservices: yesterday, today, and tomorrow.” Cornell University, 2017. |
| (Rodriguez, 2008) | A. Rodriguez, “RESTful Web services: The basics,” 2008. [Online]. Available: https://www.ibm.com/developerworks/library/ws-restful/. [Accessed: 17-Mar-2017] |
| (Nikaj and Weske, 2016) | A. Nikaj and M. Weske, “Formal Specification of RESTful Choreography Properties,” in Web Engineering. ICWE 2016. Lecture Notes in Computer Science, 2016, pp. 365–372. |
| (Nikaj et al., 2016) | A. Nikaj, S. Mandal, C. Pautasso, and M. Weske, “From Choreography Diagrams to RESTful Interactions,” in Service-Oriented Computing – ICSOC 2015 Workshops. ICSOC 2015. Lecture Notes in Computer Science, vol 9586, 2016, pp. 3–14. |
| (Google, 2007) | “Google JSON Style Guid - Property Name Format” 2007. [Online]. Available: https://google.github.io/styleguide/jsoncstyleguide.xml#Property_Name_Format/. [Accessed: 21-Oct-2017] |
