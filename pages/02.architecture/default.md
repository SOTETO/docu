---
title: Architecture
---
# Microservices as CSCW components

## Case Study: Pool<sup>2</sup> of Viva con Agua de St. Pauli e.V.
Jeder Supporter kommt irgendwann am Pool von VcA nicht mehr vorbei. Das Tool ist zu einem zentralen Bestandteil unseres Netzwerks geworden. Ob
nun das nächste Festival mit sinnstiftender Aktivität füllen, die gesammelten Spenden dokumentieren oder einfach informiert bleiben - all dies läuft
mittlerweile über den Pool und so erlaubt das System das dezentrale Wachstum von VcA. Während Funktionen, wie etwa Email-Newsletter der Crews,
unser Zusammenwirken vereinfachen und teilweise sogar erst ermöglicht haben, versuchen wir den Pool so zu gestalten, dass dieser unseren
Bedürfnissen entspricht. Unser soziales Miteinander prägt also den Pool und gleichzeitig hat der Pool Auswirkungen auf dieses Miteinander.

Viva con Agua ist erfolgreich, weil sehr viele Menschen mit Spaß und Kreativität aktiv werden können. Wir erhalten unsere Motivation, indem wir immer
wieder Neues probieren, aber uns dabei nicht selbst übernehmen. Denn der stetig zunehmende Erfolg von VcA basiert nicht auf dem Wachstum einzelner
Aktionen, sondern auf der größer werdenden Anzahl unterschiedlicher Aktionen. Die dezentrale Organisation, welche der Pool erlaubt, ist somit auch
Garant für den Erfolg von VcA.

Gleichzeitig ist die dezentrale Struktur auch eine große Herausforderung für die Weiterentwicklung des Pools (also die Anpassung des Pools an unser
soziales System). Denn wie sollen die vielen verschiedenen Entwicklungen in den Crews, die teilweise auch im Konflikt miteinander stehen, in einem
technischen Tool zusammenfließen? Eine Crew braucht dringend einen Chat, um schnell Informationen weiterzugeben, die nächste Crew hätte gerne
Abstimmungshilfen, um wirklich faire Entscheidungen zu treffen, verzichtet aber explizit auf Chats, damit das Persönliche zwischen den Supportern nicht
zu kurz kommt. Die Umsetzung solcher verschiedenen Anforderungen ist nicht alleine komplex, sondern erfordert auch ein gut koordiniertes, größeres
Team an Entwicklern. Für eine gemeinnützige Organisation ist der damit verbundene finanzielle Aufwand nicht zu bewältigen.

## IT project culture
Wir haben uns vorgenommen den Spirit von VcA ins IT-Umfeld zu übertragen. Das bedeutet, Beteiligte sollen intrinsisch motiviert sein sowie Spaß und
Freude an der Realisierung des Projekts haben. Außerdem ist der Pool<sup>2</sup> eine Komposition kleiner, dezentral organisierter und möglichst unabhängiger
Projekte.

Damit diese Kultur gelebt werden kann, haben wir diverse Herausforderungen bewältigt oder müssen dies noch schaffen: Zunächst müssen Menschen
gefunden werden, die an einer kontinuierlichen technischen Weiterentwicklung in Symbiose mit dem sozialen System interessiert sind. Dabei können
ähnlichen Strukturen wie in Open Source Communities entstehen, studentische Arbeiten integriert werden oder Organisationen im Sinne des All-Profit
Gedanken einen Beitrag leisten. Zudem muss die Unabhängigkeit der Beteiligten ermöglicht werden. Es gilt also die notwendige Einarbeitung gering zu
halten und die Unabhängigkeit von Technologien weitestgehend zu gewährleisten. Auch die Schnittstellen zur Koordination zwischen den Teams sollten
frei gestaltet werden können, so dass verschiedene Projektmanagement-Stile unterstützt werden.

## Microservice architecture
Die zuvor beschriebene Herausforderungen möchten wir ganzheitlich und in Zusammenhang betrachten. Daher sollte die Entscheidung über eine
Architektur nicht alleine die funktionalen Anforderungen an den Pool<sup>2</sup> beachten, sondern auch Trennung der Projektteams hinsichtlich Technologie und
Koordination gewährleisten. Neben klassischen monolithischen Architekturen, betrachten wir daher auch stark in Komponenten separierte Varianten und
die Auswirkungen der jeweiligen Architekturen auf die Projektkultur.

Eine moderne Form einer losen Architektur basiert auf Microservices (Newman, 2015). Ein Microservice ist dabei eine eigenständige Anwendung, welche
in einem eigenen Prozess läuft und eine starke Kohäsion bzgl. eines _Bounded Context_ aufweist. Zudem steht ein solcher Service dauerhaft in Verbindung
mit anderen Microservices und verwendet dabei eine leichtgewichtige Kommunikation, wie etwa RESTful webservices. Eine Menge solcher Microservices
wird Microservice Architektur genannt (Newman, 2015; Dragoni et al., 2017).

Da Microservices weitgehend unabhängig voneinander agieren, ist eine freie Wahl der Technologie für jeden einzelnen Service gegeben (mit den
Einschränkungen, die nachfolgend im Abschnitt [Konzepte](#concepts) aufgeführt werden. Auch koordinative Abhängigkeiten sind stark eingeschränkt und so bietet eine
derartige Architektur die Möglichkeit die zuvor beschriebene Projektkultur zu etablieren.

![The microservice architecture of the Pool2](image://Pool2-ms.png?resize=600,300&classes=float-left)

Die funktionalen Anforderungen an den Pool<sup>2</sup> werden im ersten Schritt durch den Funktionsumfang des ersten Pool definiert. Abbildung 1 zeigt die
Projektion der Funktionen des Pool auf Microservices des Pool<sup>2</sup>. Drops ist für die Nutzerverwaltung verantwortlich, während Stream die Finanzen und
Waves die Aktionen hält. Bloob dient der Kommunikation. Diese Services bilden alleine die offensichtlichen funktionalen Anforderungen ab. [Konzepte](#concepts) führt 
weitere, zumeist nicht-funktionale Anforderungen sowie die weiteren Microservices ein, die diese Anforderungen realisieren. Zudem sind andere nichtfunktionale 
Anforderungen abzubilden. So benötigt etwa jedes Kollaborationstool ein Awareness-System, welches die Nutzer übereinander informiert (etwa durch Notifications).

Eine Microservice Architektur impliziert diverse Herausforderungen, die in monolithischen Architekturen nicht auftreten würden. Das Pool<sup>2</sup> Projekt in
Zusammenarbeit mit der [Humboldt-Universität zu Berlin](https://soteto.net?target=_blank) dient der Vorbereitung einer Microservice Architektur und von Konzepten, mit deren Hilfe die
Herausforderungen angegangen werden können. Wie etwa kann eine _[Shared Session](shared-session)_ zwischen den Microservices hergestellt werden, so dass die
Supporter sich nicht in jedem Microservice einzeln authentifizieren müssen? [Wie genau sieht die Kommunikation der Microservices aus?](oes) [Wie lässt sich
diese absichern?](intraMicroservice) Wie kann sichergestellt werden, dass die verschiedenen Services, die auch von verschiedenen Kooperationspartnern erstellt werden
einem [einheitlichen Corporate Design (CD) folgen, ohne das dabei doppelter Code entsteht](dUIfc) und die Wartbarkeit des System erschwert wird? Diese und
weitere Fragen sollen im Abschnitt [Konzepte](#concepts) diskutiert werden.

## Concepts

Nachfolgend werden grundlegende Konzepte aufgeführt, die eine Implementierung der zuvor beschriebenen, angestrebten Lösung auf technischer Ebene
erlauben. Die Liste von Konzepten wird dabei kontinuierlich erweitert und dient einer ersten Orientierung, wenn ein neuer Microservice entwickelt werden
soll.

| Name | Beschreibung | Status |
| ---- | ------------ | ------ |
| [Dynamic UI Fragment Composition](dUIfc) | In einer dezentralen Microservice Architektur ist die Implementierung der Nutzeroberfläche eine Herausforderung. Da ein zentraler Service starke Abhängigkeiten aller anderen Services zu diesem einen Service erzeugt, ist dies für die angestrebte Projektkultur *keine* Lösung. Somit muss jeder Service seine eigene Nutzeroberfläche implementieren können, dabei ist aber ein Corporate Design (CD) zu beachten und Code Duplizierung zu vermeiden. Das vorliegende Konzept beschreibt, wie diese Ziele erreicht werden können. | VERWENDET |
| [Business Object Exchange](oes) | Ein Microservice übernimmt die Verantwortung für (mehrere) Business Objects (BO). Diese müssen gegebenenfalls an andere Microservices weitergegeben werden, da diese zusätzliche Informationen zur Durchführung der bereitgestellten Operationen benötigen. Das vorliegende Konzept beschreibt den Austausch der BOs basierend auf RESTful webservices. | VERWENDET |
| [Shared Session](shared-session) | Innerhalb vieler Microservices werden Nutzer identifiziert werden müssen. Damit nicht jeder Microservice eine eigenen Authentifizierung implementieren muss und damit die Wartbarkeit des Gesamtsystem erschwert und außerdem den Nutzern kein eigener Login für jeden Microservice zugemutet wird, soll eine Shared Session implementiert werden. Das vorliegende Konzept beschreibt die Shared Session basierend auf einem OAuth2 Handshake zwischen den Microservices, so dass nur ein Service eine tatsächliche Session mit dem Nutzer halten muss. | VERWENDET |
| [Action-based extension of user representation](action-based-user-rep) | Dem Prinzip der _Open Participation_ bei VcA gerecht werdend, sollen Nutzer im Rahmen der Registrierung so wenig Informationen wie nötig angeben müssen. Einige Aktivitäten im Pool² erfordert jedoch mehr Wissen über den Nutzer. So wird etwa bei der Anmeldung für ein Festival die Handynummer und das Geburtsdatum benötigt. Die angestrebte Microservice Architektur soll es ermöglichen dynamisch neue Funktionen dem Pool² hinzuzufügen. Somit kann der Pool<sup>2</sup> zur Laufzeit um Funktionen erweitert werden die mehr Informationen über den authentifizierten Nutzer erfordern, als dieser bisher angegeben hat. Diesem Problem begegnet das vorliegende Konzept mit einer dynamischen Erweiterung der Stammdaten des Nutzers immer zu genau dem Zeitpunkt, zu dem der Nutzer eine Funktion nutzen möchte. Dabei wird der Datensatz stets nur um die fehlenden Informationen erweitert. | DRAFT |

## References
|     |      |
| --- | ---- |
| (Newman, 2015) | S. Newman, Building Microservices, 1st ed. O’Reilly Media, 2015. |
| (Dragoni et al., 2017) | N. Dragoni et al., “Microservices: yesterday, today, and tomorrow.” Cornell University, 2017. |
