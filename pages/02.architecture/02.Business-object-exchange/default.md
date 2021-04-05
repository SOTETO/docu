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


_Legende_: _Haltender MS_ beschreibt den Microservice, der das VO für andere Microservices bereitstellt. _Beziehender MS_ meint den Microservice, der das
VO über die RESTful Schnittstelle abfragt.
