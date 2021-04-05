---
title: Action-based extension of user representation
slug: action-based-user-rep
---

# Action-based extension of user representation
Dem Prinzip der _Open Participation_ bei VcA gerecht werdend, sollen Nutzer im Rahmen der Registrierung so wenig Informationen wie nötig angeben
müssen. Einige Aktivitäten im Pool<sup>2</sup> erfordert jedoch mehr Wissen über den Nutzer. So wird etwa bei der Anmeldung für ein Festival die Handynummer
und das Geburtsdatum benötigt. Die angestrebte Microservice Architektur soll es ermöglichen dynamisch neue Funktionen dem Pool<sup>2</sup> hinzuzufügen. Somit
kann der Pool<sup>2</sup> zur Laufzeit um Funktionen erweitert werden die mehr Informationen über den authentifizierten Nutzer erfordern, als dieser bisher
angegeben hat. Diesem Problem begegnet das vorliegende Konzept mit einer dynamischen Erweiterung der Stammdaten des Nutzers immer zu genau
dem Zeitpunkt, zu dem der Nutzer eine Funktion nutzen möchte. Dabei wird der Datensatz stets nur um die fehlenden Informationen erweitert.

Jede verfügbare Funktion ist in einer Webanwendung entweder über Javascript auf dem Client oder eine URL erreichbar. In beiden Fällen gelten für den
betrachteten Fall die gleichen Bedingungen (authentifizierter Nutzer ist bekannt) und das selbe Vorgehen kann gewählt werden. Die Anwendung kann
prüfen, ob ein zu einem Nutzer alle notwendigen Informationen bekannt sind (d.h. im Nutzerobjekt als Attributwerte beschrieben). Ist dies der Fall, wird die
Funktion ausgeführt. Ist dies nicht der Fall, kann der Drops Microservice über die Informationen (identifiziert über Attributbezeichnungen) informiert
werden, die zu dem aktuellen Nutzer fehlen. Drops kann anschließend ein Formular zur Eingabe der fehlenden Informationen zurückgeben, welches durch
die Anwendung ausgegeben wird. Um Drops die volle Kontrolle über das Formular geben zu können, wird hier auf das Konzept der [Widgets](../dUIfc#widgets) zurückgegriffen.

Das empfangene Widget kann dann auf unterschiedliche Art und Weise genutzt werden. Ein JavaScript im Browser kann das Widget zusätzlich rendern
(beispielsweise in einem Overlay), während ein anderer MS das Widget direkt in seinem Antwort-HTML ausgeben kann.

Auf diese Weise kann sichergestellt werden, dass ein Update von Drops (beispielsweise aufgrund der Umsetzung neuer Anforderungen) keine Migration
der Nutzerdaten erfordert, der Nutzer Informationen über sich selbst erst dann preisgeben muss, wenn diese auch benötigt werden und MS-Entwickler
kaum Aufwände bei der Berücksichtigung dieser globalen Anforderung haben.
