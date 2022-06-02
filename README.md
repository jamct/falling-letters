# ADtranz-Fallblattanzeigen der Deutschen Bahn
## Dokumentation und Wissenswertes für Bastler

Fallblattanzeigen an Bahnhöfen und Flughäfen faszinieren nicht nur Kinder und Eisenbahnfans – an die klackernden Anzeigen erinnern sich viele gern zurück, die vor den Zweitausendernjahren mit dem Zug gefahren sind. Heute gibt es sie nur noch selten, aus Bahnhöfen sind sie weitestgehend verschwunden, nur in einigen Flughäfen zeigen sie noch die FLugziele an. Die gebrauchten Module sind in großen Stückzahlen bei Sammlern und in Kleinanzeigen gelandet.

![](images/main.png)

In c’t 13/2022 beschreiben wir, wie wir 10 alphanumerische Fallblattmoodule des Herstellers ADtranz gekauft und analyisert haben. Sie stammen von der Deutschen Bahn und zeigen Buchstaben, Zahlen und Sonderzeichen. Dabei sind wir auf das eher seltene Binärcode-Verfahren namens Gray-Code gestoßen. Wer sich für [Informatik-Grundlagen interessiert, wird im ausführlichen Artikel fündig](https://www.heise.de/select/ct/2022/13/2207522004009614846). In diesem Repository finden alljene Informationen, die selbst solche Module ergattern konnten (oder noch im Keller liegen haben) und sie zum Leben erwecken wollen. Wir teilen unsere Erkenntnisse, Bilder und Code mit Ihnen – alles, was zum Nachbasteln hilfreich ist. Diese Dokumentation ist dabei nicht abgeschlossen, sondern dokumentiert ein laufendes Bastelprojekt. Wir freuen uns über Ihre Erkenntnisse, Anwendungsfälle und Verbesserungsvorschläge hier bei GitHub oder per Mail an jam@ct.de

## Beschaffung

Als wir im Februar/März 2022 nach "Fallblattanzeige" bei eBay und Kleinanzeigenportalen gesucht haben, war der Gebrauchtmarkt nicht riesig, aber durchaus existent. Wir fanden Module mit einzelnen Buchstaben und Zahlen und auch breitete mit ganzen Zugzielen und Zugtypen (RE, ICE, ...). In den Warenkorb legten wir 10 alphanumerische Module für zusammen 180 Euro. Im Juni 2022 kann der Markt leider anders aussehen.

Andere Beschaffungsquellen könnten Foren für Eisenbahn-Sammler sein. Sachdienliche Hinweise gerne weitergeben.

## Grundlagen, Maße, Prinzip

Zunächst zu den Eckdaten, die entscheidend für die Planung eines Gehäuses sind

| Hersteller  | Typ | Breite  |  Höhe | Tiefe | Zeichenhöhe | Gewicht |
|---|---|---|---|---| --- | --- |
| ADtranz | Z100 M1/84-24V alpha-numeric| 70,5 mm | 150 mm  | 192 mm (ohne Anschlüsse) | 59 mm | 1474 g |

Das Funktionsprinzip der Module ist einfach. Die Buchstaben und Zahlen hängen an einer horizontalen Achse, die von einem Motor (Wechselspannung, um 24 Volt) betrieben wird. Die Reihenfolge der Zeichen haben wir [in der Datei charset_alphanum.txt](charset_alphanum.txt) abgelegt. Damit die Steuerung weiß, wie lange der Motor drehen muss und welches Zeichen gerade gefallen ist, hat jedes Modul einen Absolutencoder für die Position. Dafür ist unter dem Motor eine geschlitzte Kodierscheibe eingebaut, die von Lichtschranken ausgelesen wird. Mit diesem Hintergrundwissen ist es Zeit für die elektronischen Herausforderungen.

## Elektronik: Kontakte

Die Schaltung enthält keine Geheimnisse. Sie enthält ein Relais, das den Motor schaltet sowie die Auswertung der Lichtschranken. Mit der Außenwelt verbunden wird die Platine über Kontakte, die hinten aus dem Gehäuse herausragen. Damit wurden die Module vermutlich auf eine Mutterplatine gesteckt. Die Suche nach passenden Kantensteckverbindern war nicht ganz einfach – fündig wurden wir bei Steckern im Format RAST2.5 vom Hersteller TE Connectivity, verkauft vom Händler Mouser aus den USA. Insgesamt gibt es 14 Pins, nach 4 Pins ist die Platine geschlitzt. Ideal passt eine Kombination aus einem RAST-Stecker mit 4 und einem mit 10 Kontakten:

* [Stecker RAST 2.5 von Mouser mit 10 Kontakten](https://www.mouser.de/ProductDetail/TE-Connectivity-AMP/3-1534796-0?qs=BZh9n8c3cqNGebKATKvQPw%3D%3D&countrycode=DE&currencycode=EUR)
* [Stecker RAST 2.5 von Mouser mit 4 Kontakten](https://www.mouser.de/ProductDetail/TE-Connectivity/3-1534796-4?qs=BZh9n8c3cqOLeqkkrHp%2Fjw%3D%3D&countrycode=DE&currencycode=EUR)

Tipp: Fürs Reverse-Engineering und Programmieren nicht auf die Stecker aus den USA warten (Versand dauert etwa eine Woche). Stattdessen Jumper-Kabel provisorisch anlöten.

## Elektronik: Pinbelegung

Insgesamt kommen drei Spannungslevel vor. Der Motor braucht 24 Volt Wechselspannung (läuft ab etwa 12 Volt). Die Auswertung der Lichtschranke läuft zwischen 20 und 24 Volt Wechselspannung. Die sieben Digitalausgänge schließlich geben 0 oder 5 Volt zurück. Das muss die Auswertungselektronik (Arduino, ...) später verarbeiten.

![](images/pins.png)

| Pin | Belegung |
| --- | --- |
| 1/2 | 24V DC - |
| 3/4 | 24V DC + |
| - | - |
| 5 | Code 6 |
| 6 | Code 5 |
| 7 | Code 4 |
| 8 | Code 3 |
| 9 | Code 2 |
| 10 | Code 1 |
| 11 | Code 0 |
| 12 | Relais |
| 13 | 24V AC |
| 14 | 24V AC |