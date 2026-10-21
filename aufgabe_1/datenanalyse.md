# Aufgabe 1: Datenanalyse und Normalisierung

## 1. Ausgangssituation

Die Ausgangsdatei enthält Bestellungen eines Online-Shops.
Kunden-, Bestell-, Produkt- und Herstellerdaten stehen gemeinsam
in einer Tabelle.

Die Daten umfassen 20 Bestellungen mit 31 Bestellpositionen,
10 Kunden, 12 Produkten und 9 Herstellern.

## 2. Probleme der ursprünglichen Struktur

### Redundanzen

Mehrere Angaben werden wiederholt gespeichert:

- Kundenname und Kundenadresse bei jeder Bestellung eines Kunden.
- Produktbezeichnung bei jeder Bestellung eines Produkts.
- Herstellername und Hersteller-Land bei mehreren Produkten.

### Änderungsanomalie

Wird beispielsweise eine Kundenadresse geändert, müssen mehrere
Einträge angepasst werden. Wird ein Eintrag übersehen, entstehen
widersprüchliche Daten.

### Einfügeanomalie

In der ursprünglichen Struktur lässt sich ein neues Produkt ohne
Bestellung nicht sinnvoll speichern, ohne Bestellfelder leer zu lassen
oder künstliche Bestelldaten einzutragen.

### Löschanomalie

Wird die einzige Bestellung eines Kunden gelöscht, können dadurch
auch die einzigen gespeicherten Angaben zu diesem Kunden verloren gehen.

### Berechnete Gesamtkosten

Die Gesamtkosten ergeben sich aus:

Summe(Menge × Einzelpreis) + Versandkosten

Werden sie zusätzlich gespeichert, können sie bei Änderungen an
Bestellpositionen von der tatsächlichen Summe abweichen.

Bei Bestellung 1007 enthält die Ausgangsdatei einen Rechenfehler:

180.00 + 3 × 30.00 + 26.00 + 7.99 = 303.99

Gespeichert sind jedoch 293.99.

## 3. Abhängigkeiten

Für das Modell gelten folgende fachliche Annahmen:

- Jede Bestellung gehört zu genau einem Kunden.
- Jedes Produkt ist genau einem Hersteller zugeordnet.
- Jede Produktnummer kommt innerhalb einer Bestellung höchstens
  einmal vor. Mehrere Stück werden über die Menge erfasst.
- Zu jedem Kunden wird eine aktuelle Adresse gespeichert.
- Jeder Hersteller erhält eine neu vergebene Herstellernummer.

Daraus ergeben sich folgende funktionale Abhängigkeiten:

- Kundennummer → Kundenname, Straße, Hausnummer, Postleitzahl,
  Wohnort, Land
- Bestellnummer → Bestelldatum, Kundennummer, Versandkosten
- Produktnummer → Produktbezeichnung, Herstellernummer
- Herstellernummer → Herstellername, Hersteller-Land
- Bestellnummer + Produktnummer → Menge, Einzelpreis

Der Einzelpreis wird in der Bestellposition gespeichert, weil
dasselbe Produkt bei verschiedenen Bestellungen unterschiedliche
Preise haben kann.

## 4. Normalisierung

### Erste Normalform (1NF)

Jede Bestellposition wird als eigene Zeile dargestellt.
Zusammengefasste Zellen der Ausgangstabelle werden aufgelöst.

Die Kundenadresse wird in Straße, Hausnummer, Postleitzahl,
Wohnort und Land aufgeteilt, damit diese Angaben einzeln
verarbeitet werden können.

### Zweite Normalform (2NF)

Die erste Normalform ist erfüllt.

Angaben, die nur von einem Teil des zusammengesetzten Schlüssels
aus Bestellnummer und Produktnummer abhängen, werden ausgelagert:

- Bestelldaten hängen nur von der Bestellnummer ab.
- Produktdaten hängen nur von der Produktnummer ab.

In den Bestellpositionen bleiben Menge und Einzelpreis.
Diese hängen von der gesamten Schlüsselkombination ab.

### Dritte Normalform (3NF)

Die zweite Normalform ist erfüllt.

Transitive Abhängigkeiten von Nichtschlüsselattributen werden
durch weitere Tabellen vermieden:

- Kundendaten werden über die Kundennummer in Kunden gespeichert.
- Herstellerdaten werden über die Herstellernummer in Hersteller
  gespeichert.

Beispiel einer aufgelösten transitiven Abhängigkeit:

Produktnummer → Herstellernummer → Hersteller-Land

Die Gesamtkosten werden bei Bedarf berechnet und nicht zusätzlich
gespeichert.

## 5. Ergebnis: Tabellen in der dritten Normalform

### Kunden – kunden.csv

- Kundennummer: Primärschlüssel
- Kundenname
- Strasse
- Hausnummer
- Postleitzahl
- Wohnort
- Land

### Hersteller – hersteller.csv

- Herstellernummer: Primärschlüssel
- Herstellername
- Hersteller_Land

### Produkte – produkte.csv

- Produktnummer: Primärschlüssel
- Produktbezeichnung
- Herstellernummer: Fremdschlüssel zu Hersteller

### Bestellungen – bestellungen.csv

- Bestellnummer: Primärschlüssel
- Bestelldatum
- Kundennummer: Fremdschlüssel zu Kunden
- Versandkosten

### Bestellpositionen – bestellpositionen.csv

- Bestellnummer: Fremdschlüssel zu Bestellungen
- Produktnummer: Fremdschlüssel zu Produkte
- Menge
- Einzelpreis

Bestellnummer und Produktnummer bilden gemeinsam den
zusammengesetzten Primärschlüssel.

## 6. Beziehungen

- Ein Kunde kann mehrere Bestellungen aufgeben: 1:n.
- Eine Bestellung enthält mehrere Bestellpositionen: 1:n.
- Ein Produkt kann in mehreren Bestellpositionen vorkommen: 1:n.
- Ein Hersteller kann mehrere Produkte herstellen: 1:n.

Bestellungen und Produkte stehen dadurch in einer n:m-Beziehung,
die durch die Tabelle Bestellpositionen aufgelöst wird.

## 7. Dateiformat

Alle CSV-Dateien verwenden:

- UTF-8 als Zeichenkodierung
- Semikolon als Trennzeichen
- Eine Kopfzeile mit den Spaltennamen
- Einen Punkt als Dezimaltrennzeichen
- Das Datumsformat YYYY-MM-DD