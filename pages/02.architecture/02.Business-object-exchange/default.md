---
title: Business Object Exchange
slug: oes
---
# Business Object Exchange
Ein Microservice übernimmt die Verantwortung für (mehrere) verwaltete Objekte (VO). Diese müssen gegebenenfalls an andere Microservices
weitergegeben werden, da die Services zusätzliche Informationen zur Durchführung der bereitgestellten Operationen benötigen. Das vorliegende Konzept
beschreibt den Austausch der VOs basierend auf RESTful Webservices, welche häufig in Microservice Architekturen verwendet werden (Dragoni et al.,
2017). REST bildet die HTTP Verben auf Basisoperationen des CRUD-Prinzips (Create, Read, Update and Delete) ab. Somit wird für die Erstellung von
Instanzen einer Entität POST verwendet, für das Abrufen GET, die Aktualisierung ist durch PUT gekennzeichnet (ebenso die gleichzeitige Erstellung
mehrerer Instanzen) und der Löschvorgang wird mittels DELETE eingeleitet (Rodriguez, 2008).

RESTful Webservices werden durch wenige Haupteigenschaften charakterisiert. Erstens sind sie zustandslos: Requests enthalten alle nötigen
Informationen, um die Anfrage zu beantworten und Responses können Links auf andere Ressourcen beschreiben. Zudem können die Responses auf eine
Anfrage gecached werden. Zweitens nutzen Webservices URIs mit dem Aufbau einer Verzeichnisstruktur: Eine Hierarchie von (Sub)Pfaden erweitert
einen primären Wurzelknoten und Query Strings sollten vermieden werden. Außerdem werden die Daten in einem vom Menschen lesbaren Format
beschrieben, wie etwa XML, JSON oder beides parallel (Rodriguez, 2008).

Um die lose Struktur innerhalb von VcA zu berücksichtigen, sollte auf dieser Kommunikationsebene Choreography anstelle von Orchestration als Form der
Organisation eingesetzt werden (Nikaj and Weske, 2016; Nikaj et al., 2016). Auf diese Weise benötigt die Kommunikation zwischen zwei Services auch
nur die beiden Services und einen Kommunikationskanal zwischen diesen. Somit kann ein Netzwerk von Microservices entstehen, in dem die einzelnen
Services nur abhängig von den Services sind, mit denen sie auch kommunizieren müssen.

Beispielsweise: Der Microservice Waves erlaubt die Verwaltung von Aktionen. Um eine Aktion anzulegen muss ein Ansprechpartner (ASP) hinterlegt
werden können. Ansprechpartner ist in diesem Kontext eine Rolle, die ein Nutzer im System einnehmen kann. Nutzer werden jedoch im Drops-System
verwaltet und persistiert. Waves kann die durch die Nutzer repräsentierten Supporter für diesen Zweck von Drops abfragen und nutzt dafür RESTful
Webservices.

## Selektion der Daten
Eine Herausforderung für den Entwickler eines Microservices besteht darin zu entscheiden welche Daten für die Übertragung bereitgestellt werden sollen.
Dafür lassen sich die VOs anhand der Grafik aus Abbildung 2 klassifizieren. Grundsätzlich sind die VOs in zwei Kategorien zu trennen: Erstens gibt es die
Eigenen VO, also VO die durch den Microservice selbst gehalten und verwaltet werden und zweitens die Pulled VO, also VOs die von anderen
Microservices bezogen werden. Hier interessiert uns nur die erste Kategorie. Diese lässt sich ebenfalls in die Kategorien Pushed VOund Interne VO aufteil
en. Während die Pushed VO eben genau die VOs umfasst, die an andere Microservices weitergegeben werden sollen, dienen Interne VO alleine der
Verarbeitung durch den vorliegenden Microservice und werden nicht weitergegeben.

Als Beispiel soll an dieser Stelle der Nutzer des Systems dienen. Die Verwaltung der Nutzer obliegt dem Drops Microservice. Der Nutzer selbst wird keinen
anderen Services bereitgestellt und ist daher kein Pushed VO, sondern ein Eigenes VO von Drops. Der durch den Nutzer repräsentierte Supporter ist
hingegen ein Pushed VO. Inwiefern unterscheiden sich Supporter und Nutzer des Systems? Ein Nutzer umfasst stets einen Supporter, bildet aber
zusätzlich die Möglichkeit zur Authentifizierung mittels eines zusätzlichen Passworts. Zur Identifikation des Nutzers wird die Email-Adresse verwendet, die
auch den Supporter eindeutig identifiziert. Somit ist der Supporter ein Pushed VO, während der Nutzer gar ein Eigener VO ist (Authentifizierung der Nutzer
innerhalb des Pool<sup>2</sup> wird mittels des [Shared Session](../shared-session) Konzepts realisiert). Ob der Supporter als Pushed VO oder Interne VO vom Drops-Entwickler
betrachtet werden sollte hängt davon ab, ob der Supporter auch für andere Microservices relevant sein könnte. Da dies im Fall des Supporters gegeben
ist, handelt es sich bei einem Supporter um ein Pushed VO.

Zu beachten ist der Sonderfall, dass zusätzliche Informationen zu einem Pulled VO in einem Service erzeugt und verarbeitet werden. Es stellt sich
natürlich die Frage, ob derartige Erweiterungen der VOs auch nach außen transparent zur Verfügung gestellt werden sollen. Hier greift die später
beschriebene "Eskalationsrichtlinie", nach der ein VO im Netzwerk nur vollständig aus einer Quelle bezogen werden kann. Somit werden solche
Erweiterungen im einfachen Fall nicht nach außen bereitgestellt und werden somit als Interne VO betrachtet. Diese Überlegung wird in Abbildung 3 visualisiert.

## Eskalationsrichtlinie
Wie bereits im Abschnitt "Selektion der Daten" beschrieben, müssen Regeln für die Veränderung von Pushed VOs (und Pulled VOs) festgesetzt werden.
Die folgende Tabelle soll für die verschiedenen Fälle das jeweilige Vorgehen und jeweils eine Begründung dazu liefern:

| Fall | Regel | Begründung |
| ---- | ----- | ---------- |
| Ein Pushed VO wird erweitert | Erweiterung kann vorgenommen werden und steht unter einer neuen Versionsnummer zur Verfügung. Vorherige Beschreibung der Daten bleibt unter einer vorherigen Versionsnummer verfügbar. | Konflikte im Umgang mit den empfangenen Daten sollen möglichst vermieden werden. Hierfür ist es sinnvoll den Entwicklern der Microservices selbst die Verantwortung für die Robustheit ihres Systems zu übergeben, was durch die Verfügbarkeit der verschiedenen Versionen der Datenformate ermöglicht wird.|
| Eigenschaften eines Pushed VO werden verändert (beispielsweise der Typ) | Veränderung kann vorgenommen werden und steht unter einer neuen Versionsnummer zur Verfügung. Vorherige Beschreibung der Daten bleibt unter einer vorherigen Versionsnummer verfügbar. | Konflikte im Umgang mit den empfangenen Daten sollen möglichst vermieden werden. Hierfür ist es sinnvoll den Entwicklern der Microservices selbst die Verantwortung für die Robustheit ihres Systems zu übergeben, was durch die Verfügbarkeit der verschiedenen Versionen der Datenformate ermöglicht wird. |
| Eigenschaften eines Pushed VO werden entfernt | Entfernung kann vorgenommen werden und steht unter einer neuen Versionsnummer zur Verfügung. Vorherige Beschreibung der Daten bleibt unter einer vorherigen Versionsnummer verfügbar. | Konflikte im Umgang mit den empfangenen Daten sollen möglichst vermieden werden. Hierfür ist es sinnvoll den Entwicklern der Microservices selbst die Verantwortung für die Robustheit ihres Systems zu übergeben, was durch die Verfügbarkeit der verschiedenen Versionen der Datenformate ermöglicht wird. |
| Ein Pulled VO wird erweitert | Initiierung eines Abstimmungsvorgangs mit anderen Microservice Entwicklern. Wird diese Erweiterung auch durch andere Services benötigt oder abgebildet? Falls ja: Als Anforderung an den haltenden MS. Falls nein: Abbildung als Interne VO im beziehend en MS. | Kommunikation auf technischer Ebene soll möglichst einfach und überschaubar bleiben. Daher sollen VO nicht auf verschiedene Microservices verteilt werden. |
| Eigenschaften eines Pulled VO müssen verändert werden (beispielsweise der Typ) | Initiierung eines Abstimmungsvorgangs mit anderen Microservice Entwicklern. Wird die Veränderung durch alle Entwickler-Teams befürwortet: Anforderung an den halten den MS. Falls die Veränderung nur durch den beziehenden MS benötigt wird, sollte dort eine Lösung gefunden werden (bspw. Typkonvertierung). Wenn nur ein Teil der Entwickler-Teams die Veränderung befürwortet, muss eine Lösung auf Seiten des halt enden MS gefunden werden, welche alle Varianten über die verschiedenen Versionen hinweg bereitstellt. | Auf diese Weise kann sichergestellt werden, dass jede benötigte Form der VOs bezogen werden kann, ohne dadurch einige Microservices auf den Zugriff auf alte Versionen zu beschränken (und damit von zukünftigen Entwicklungen auszuschließen). |
| Eigenschaften eines Pulled VO werden nicht benötigt | Siehe Lösung zuvor (Eigenschaften eines Pulled VO müssen verändert werden) | Siehe Begründung zuvor (Eigenschaften eines Pulled VO müssen verändert werden) |

Legende_: _Haltender MS_ beschreibt den Microservice, der das VO für andere Microservices bereitstellt. _Beziehender MS_ meint den Microservice, der das
VO über die RESTful Schnittstelle abfragt.

## Lebenszyklen - Object Event System
Verändern sich die gehaltenen Daten in Microservices, müssen andere Microservices gegebenenfalls darauf reagieren können. Beispielsweise werden in
Waves zu jeder Aktion verantwortliche Supporter assoziiert. Löscht oder deaktiviert ein solcher Supporter nun aber den eigenen Account, ist es sinnvoll,
wenn Waves dies registrieren kann, den Nutzer aus der Assoziation entfernt und eventuell andere Nutzer über diesen Vorgang informiert.

Damit ein Microservice die Anderen über Veränderungen seines Datenbestands informieren kann, führen wir eine zusätzliche Kommunikationsebene ein.
Dieses Kommunikationssystem zwischen den Microservice wird *Object Event System (OES)* genannt und basiert auf dem bestehenden
Kommunikationsnetzwerk zwischen den Microservices. Das OES implementiert einen Nachrichtenaustausch nach dem PUSH-Prinzip. Das bedeutet, dass
ein Microservice nach einer Änderung an einem VO allen anderen Microservices, die mit ihm bzgl. *Object Exchange* verbunden sind, eine Nachricht
schickt. Diese Nachricht umfasst dabei die Information wann welches Objekt verändert wurde und welche Art der Veränderung vorgenommen wurde
(löschen oder ändern). Beziehende Microservices können anschließend das Objekt neu beziehen (bei Veränderung) oder alle Referenzierungen auf das
Objekt entfernen. Auch hier wird die Verantwortung den Entwicklern des jeweiligen Microservices überlassen, um eine möglichst freie Gestaltung des
Umgangs mit derartigen Ereignissen zu erlauben.

Die Nachrichten haben daher folgendes Format:
```
{
  "sender": "microservice-uuid",
  "action": "action-id",
  "object": "object-uuid",
  "type": "object-type",
  "timestamp": 123456789
}
```

Während das Attribut `sender` den Microservice identifiziert, der das jeweilige Objekt hält, beschreibt `action` die verändernde Operation. `action` kann
dabei drei verschiedene Werte annehmen: `delete`, `update`, `deactivated` oder `activated`. Im Unterschied zu `deactivated` beschreibt `delete` die
vollständige Entfernung des Objekts aus dem Datenbestand. Es kann somit nie wieder abgerufen werden. `deactivated` hingegen ist ein Objekt dann,
wenn es noch in der Datenbank gehalten wird, aber nicht mehr aktiv verwendet werden soll. Beispielsweise könnte ein Supporter `deactivated` sein,
nachdem der Nutzer seinen Account gelöscht hat. Dies hat den Vorteil, dass der Supporter im Kontext vergangener Interaktion noch repräsentiert werden
kann (etwa als verantwortlicher Ansprechpartner zu einer Aktion). Die `action` `activated` kann verwendet werden, um deaktivierte Objekte zu
reaktivieren. Das Attribut object identifiziert das betroffene Objekt eindeutig mittels einer `UUID`. `type` unterstützt die Kontextualisierung des Objekts und
beschreibt den Typ (die Klasse) des Objekts. Der mitgelieferte `timestamp` gibt Auskunft darüber, wann die Operation ausgeführt wurde und wird als Unix
Timestamp (fortlaufender ganzzahliger Wert) abgebildet.

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
