---
title: Dynamic UI Fragment Composition
slug: dUIfc
---

# Dynamic UI Fragment Composition

Die angestrebte Microservice-Architektur dient vorrangig der Verteilung von Verantwortung innerhalb des Projekts. Im Sinne der Prinzipien _Loosely
coupling_ und _High Cohesion_ ist also auch die Einführung eines zentralen User Interface (UI) Services nicht zielführend. Dies impliziert, dass die diversen
kleinen Projektteams für ihre Microservices einschließlich der UI verantwortlich sind.

Neben der angesprochenen Verteilung der Verantwortung sind aber auch grundlegende Prinzipien der _Human-Computer-Interaction (HCI)_ und ein _Corpora
te Design (CD)_ zu realisieren. Wie ein derartiges, einheitliches Look & Feel erreicht werden kann, beschreibt das vorliegende Konzept. Neben der
Umsetzung nicht-funktionaler Anforderungen, welche im Folgenden dokumentiert werden, gilt es ebenso informelle Beschreibungen des Verhaltens der
Microservices zu beachten. Letzteres muss wiederum durch neue Mechanismen im sozialen System sichergestellt und implementiert werden.

## Solution
Der Pool<sup>2</sup> wird als _Rich-Internet-Application (RIA)_ entworfen. Neben der Verwendung im Browser soll auch eine mobile Nutzung möglich sein. Im ersten
Schritt wird dabei angenommen, dass Web-Apps als mobile Clients implementiert werden. Die Menge an möglichen Client-Technologien lässt sich somit
auf HTML, CSS und JavaScript einschränken.

Die hier skizzierte Lösung basiert daher auf zwei Säulen:
* Geteiltes CSS
* Widgets

Die erste Säule (Geteiltes CSS) erlaubt die gemeinsame Nutzung von Design-Elementen und Layout-Beschreibungen. Somit wird ein gemeinsames CD
ermöglicht. Nachdem die vorherige Säule die Nutzung eines globalen User Interfaces erlaubt, dienen Widgets der gemeinsamen Nutzung von UI-Elementen 
durch verschiedene Services. Beispielsweise werden mehrere Services die Auswahl von Nutzern mittels des UI ermöglichen. Neben
Herausforderungen hinsichtlich der Wartbarkeit, könnte auch ein CD und die Konsistenz der Nutzerführung nach den gängigen Prinzipien der HCI nicht
sichergestellt werden, wenn jeder Service eigene UI-Elemente für diesen Zweck bereitstellen würde. Da Nutzer vom Drops Microservice verwaltet werden,
soll dieser auch UI-Elemente zur Verwendung der Nutzer bereitstellen. Nach diesem Prinzip entwickelt, folgt die Architektur auch bezüglich der UI-Elemente 
den Prinzipien _Loosely coupling_ und _High Cohesion_, welche für Microservice-Architekturen grundlegend sind. Allgemeine, globale Elemente,
wie etwa Navigation oder, zentraler statischer Inhalt (wie bspw. ein Impressum oder ein Header inkl. Logo) können ebenfalls als Widgets für alle Inhalte
der RIA bereitgestellt werden.

### Geteiltes CSS
Globales CSS ist im Sinne einer Trennung der Domain nach Bounded Contexts allen Kontexten übergeordnet. Daher ist die Verantwortung für das CSS
nicht einem der Microservices zuzuordnen, die funktionale Anforderungen abbilden, sondern separat zu betrachten. CSS wird somit durch den speziellen
Microservice Dispenser implementiert, welcher diese nicht-funktionale Anforderung abbildet.

Das CSS selbst folgt gängigen Richtlinien zur Erstellung modularen CSS ([https://css-tricks.com/css-style-guides/](https://css-tricks.com/css-style-guides/?target=_blank) 
und [http://cssguidelin.es/](http://cssguidelin.es/?target=_blank)), fungiert als Pattern Library und wird mithilfe von LESS ([http://lesscss.org/](http://lesscss.org/?target=_blank)) entwickelt, 
um Wartbarkeit und Weiterentwicklung zukunftssicher zu gestalten.

### Widgets
Die Microservices sind mittels Widgets in der Lage einzelne Funktionen inklusive eines UI bereitzustellen, so dass diese durc h andere Microservices
ausgegeben werden können. Ein Widget soll mittels eines URI integriert werden können. Dabei orientiert sich das Konzept der Widgets grundlegend an
der Idee von Transclusions. Die bereitstellenden Microservices dienen dabei als Quelle auf welche die URI zeigt. Auf eine GET Anfrage des Widgets
mittels des URI liefert der Microservice ein UI-Element zurück, welches dabei jedoch nicht allein statisch ausgegeben werden, sondern auch über
dynamisches Verhalten verfügen kann. Neben dem Standardverhalten von HTML-Elementen können auch JavaScript basierte Erweiterungen
vorgenommen werden oder vollkommen eigenständige Alternativelemente via JavaScript definiert werden. Hinsichtlich der optischen Präsentation werden
Widgets über das geteilte CSS, Style Tags (scoped) innerhalb des Widget Markups oder das Style-Attribut an den jeweiligen Markup Elementen gestaltet.

Der Aufruf eines Widgets mittels HTTP GET Request liefert immer HTML Code innerhalb eines Root-Tags zurück. Dabei kann der HTML Code eigene(n)
CSS oder JavaScript Code enthalten und der Root-Tag frei gewählt werden, muss aber mittels einer ID ausgezeichnet werden, welche das Widget
eindeutig identifiziert. Da Widgets gegebenfalls parametrisiert werden müssen (etwa um kontextsensitive Platzhalter für Formular-Elemente zu erlauben),
soll die URI die Übergabe von Variablenwerten nach den Kriterien eines RESTful Webservices erlauben.

Des Weiteren können Widgets Seiteneffekte haben. Diese beschreiben Reaktionen des Widgets auf konkrete Ereignisse. So kann etwa die Selektion einer
Crew in einem Widget A die Auswahl von Nutzern in einem anderen Widget B einschränken. Ereignisse werden über die einbindende Seite mittels
JavaScript Events ausgetauscht. Dabei kann ein solcher Event sowohl von der Seite selbst, als auch von einem eingebundenen Widget ausgelöst werden.

Zu jedem Widget muss zusätzlich eine Dokumentation geliefert werden, die mögliche Parametrisierungen, das Verhalten und Seiteneffekte (also die
Events auf die reagiert wird) beschreibt. Im Sinne des dezentralen und loosely coupled Organisationsprinzips des Projekts ist definiert, dass Widgets mit
Hilfe semantischer Versionierung ([http://semver.org/](http://semver.org/?target=_blank)) stets abwärtskompatibel deployed werden. Dies bedeutet, dass die Versionsnummer innerhalb der
URI des Widgets spezifiziert werden können muss.
