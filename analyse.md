
# Analyse der Förderbandsimulation – Industrial Conveyor Digital Twin

## 1. Allgemeine Informationen

| Eigenschaft | Information |
|---|---|
| Projekt | Industrial Conveyor Digital Twin |
| Unity-Version | 6000.3.24f1 |
| Programmiersprache | C# |
| Entwicklungsumgebung | Unity 6, Visual Studio Code |
| Rendering Pipeline | Universal Render Pipeline (URP) |
| GitHub-Repository | https://github.com/5bhits-veng/04-15-DigitalTwin |
| Teammitglieder | Medine Dinc, Sham Shaar |
| Datum | 16.11.2026 |

## 2. Projekt einrichten

- **Repository geklont:** Ja
- **Unity-Projekt geöffnet:** Ja
- **Verwendete Unity-Version:** 6000.3.24f1
- **Hauptszene:** `Assets/Scenes/ConveyorDigitalTwin.unity`
- **Simulation erfolgreich gestartet:** Ja

Das Projekt wurde aus dem bereitgestellten GitHub-Repository geklont und anschließend mit Unity Hub geöffnet.

Laut README wurde das Projekt ursprünglich mit Unity 6000.3.20f1 entwickelt. Ein erfolgreicher Laufzeittest wurde jedoch auch mit Unity 6000.3.24f1 durchgeführt. Daher wurde für die Untersuchung die Version 6000.3.24f1 verwendet.

Nach dem Öffnen der Hauptszene `ConveyorDigitalTwin.unity` kann die Simulation über den Play-Modus gestartet werden.

### Beobachtungen beim Start

Beim Start der Simulation wird eine virtuelle industrielle Förderbandanlage dargestellt. Auf dem Förderband werden Werkstücke automatisch erzeugt und entlang der Anlage transportiert.

Die Werkstücke bewegen sich in Richtung des Ausgangs, wo sie von einem virtuellen Sensor erkannt werden. Nach erfolgreicher Erkennung werden sie gezählt und aus der Simulation entfernt.

Zusätzlich verfügt die Anwendung über ein Dashboard, das verschiedene Betriebsinformationen der Anlage darstellt. Dazu gehören unter anderem die Fördergeschwindigkeit, die Motortemperatur, der elektrische Strom, die Anzahl der transportierten Werkstücke sowie die Zustände der virtuellen Sensoren.

Die Anlage besitzt außerdem eine optische Signalanzeige (Stack Light), welche den aktuellen Betriebszustand visualisiert.

---

## 3. Simulation untersuchen

### 3.1 Funktionen der Simulation

| Funktion | Beobachtung |
|---|---|
| Start | Startet den Fördervorgang. Werkstücke werden automatisch in konfigurierbaren Zeitabständen erzeugt und über das Förderband transportiert. |
| Stop | Hält den regulären Fördervorgang an. Die Anlage wechselt in den Stillstand. |
| Reset | Ermöglicht das Zurücksetzen der Anlage. Welche Werte und Komponenten dabei genau zurückgesetzt werden, muss anhand der Simulation überprüft werden. |
| Not-Halt | Versetzt die Anlage in den Zustand Emergency Stop. Dieser Zustand wird über das Dashboard und die optische Signalanzeige dargestellt. |
| Fördergeschwindigkeit verändern | Die Fördergeschwindigkeit kann über das Dashboard angepasst werden. Die Motorrotation und die simulierte Stromaufnahme hängen ebenfalls von der Geschwindigkeit ab. |
| Temperaturüberwachung | Die Motortemperatur wird künstlich simuliert und überwacht. Bei Überschreitung definierter Grenzwerte werden Warnungen oder ein Überhitzungsfehler ausgelöst. |

### 3.2 Verhalten der Förderanlage

**Was passiert mit einem Werkstück, wenn das Förderband gestoppt wird?**

Die Bewegung der Werkstücke wird mithilfe von Rigidbody-Komponenten umgesetzt und ist an den Betriebszustand des Förderbands gekoppelt.

Während des regulären Betriebs werden die Werkstücke entlang des Förderbands transportiert. Beim Stoppen wird der Fördervorgang angehalten.

Das genaue physikalische Verhalten der Werkstücke nach dem Stoppen hängt von der Implementierung der Bewegungssteuerung ab und sollte direkt in der Simulation überprüft werden.

**Welche Werte werden im Dashboard angezeigt?**

Das Dashboard stellt verschiedene Betriebsinformationen der Förderanlage in Echtzeit dar.

Dazu gehören:

- Aktueller Betriebszustand der Anlage
- Fördergeschwindigkeit
- Simulierte Motortemperatur
- Simulierter elektrischer Strom
- Anzahl der fertig transportierten Werkstücke
- Status des Eingangssensors
- Status des Ausgangssensors

Zusätzlich werden entsprechende Status- und Fehlermeldungen angezeigt.

Über das Dashboard kann außerdem die Fördergeschwindigkeit angepasst werden. Dadurch ist eine direkte Interaktion mit der virtuellen Anlage möglich.

**Was passiert bei einer Störung?**

Die Simulation kann verschiedene Betriebszustände darstellen. Dazu zählen:

- Normalbetrieb (Normal)
- Warnzustand (Warning)
- Fehlerzustand (Fault)
- Stillstand (Stopped)
- Not-Halt (Emergency Stop)

Eine Störung kann beispielsweise durch eine zu hohe Motortemperatur ausgelöst werden.

Dazu werden die simulierten Temperaturwerte kontinuierlich überwacht. Bei Überschreitung eines konfigurierten Warnwerts wird zunächst ein Warnzustand ausgelöst. Wird zusätzlich der kritische Temperaturgrenzwert überschritten, kann die Anlage automatisch in einen Überhitzungsfehler wechseln.

Der jeweilige Zustand wird sowohl im Dashboard als auch über die optische Signalanzeige dargestellt.

Die Signalanzeige verwendet drei Farben:

- Grün: Normalbetrieb
- Gelb: Warnzustand
- Rot: Fehler- oder kritischer Zustand

---

## 4. Programmstruktur analysieren

Die Anwendung wurde in C# entwickelt und besitzt eine modulare Programmstruktur. Die Skripte sind nach ihren jeweiligen Aufgabenbereichen organisiert.

Die wichtigsten Programmkomponenten befinden sich im Verzeichnis `Assets/Scripts/`.

### 4.1 Zuordnung der C#-Skripte

| Funktion | Skript / Dateiname | Aufgabe des Skripts |
|---|---|---|
| Bewegung des Förderbands bzw. der Werkstücke | `ConveyorMover.cs` | Steuert die Bewegung der Werkstücke entlang des Förderbands. Die Bewegung erfolgt mithilfe von Rigidbody-Komponenten und berücksichtigt den Betriebszustand sowie die Fördergeschwindigkeit. |
| Erkennung durch Sensoren | `IndustrialSensor.cs` | Simuliert virtuelle Ein- und Ausgangssensoren. Die Werkstücke werden mithilfe von Triggern erkannt, sobald sie den jeweiligen Sensorbereich betreten. |
| Speicherung der Betriebsdaten | `ConveyorTwinData.cs` | Verwaltet den gemeinsamen Betriebszustand und die Betriebsdaten der Förderanlage. Andere Komponenten können auf diese Daten zugreifen oder sie aktualisieren. |
| Protokollierung der Daten | `DigitalTwinLogger.cs` | Erfasst Betriebsinformationen in konfigurierbaren Zeitabständen und speichert diese mit einem Zeitstempel in einer CSV-Datei. |

### 4.2 Weitere wichtige Skripte

| Skript | Aufgabe |
|---|---|
| `MachineConditionSimulator.cs` | Simuliert die Motortemperatur und den elektrischen Strom. Überwacht Temperaturgrenzwerte und löst entsprechende Warn- oder Fehlerzustände aus. |
| `MotorRotation.cs` | Steuert die sichtbare Rotation des Motors abhängig von der Fördergeschwindigkeit. |
| `ProductSpawner.cs` | Erzeugt Werkstücke in konfigurierbaren Zeitabständen und berücksichtigt eine maximale Anzahl gleichzeitig aktiver Werkstücke. |
| `DigitalTwinDashboard.cs` | Stellt die aktuellen Betriebsdaten dar und ermöglicht die Bedienung der Anlage über eine grafische Benutzeroberfläche. |
| `StackLightController.cs` | Steuert die optische Signalanzeige entsprechend dem aktuellen Betriebszustand der Anlage. |
| `SimpleCameraController.cs` | Ermöglicht die Steuerung der Kamera innerhalb der virtuellen Umgebung. |

### 4.3 Erkenntnisse zur Programmstruktur

Die Anwendung ist modular aufgebaut. Die einzelnen Skripte übernehmen unterschiedliche Aufgaben und sind in die Bereiche Core, Equipment, Sensors, UI und Logging unterteilt.

Eine zentrale Rolle spielt dabei `ConveyorTwinData.cs`. Dieses Skript stellt den gemeinsamen Betriebszustand der Anlage bereit. Die verschiedenen Komponenten können auf diese Informationen zugreifen oder entsprechende Werte aktualisieren.

Beispielsweise erkennt `IndustrialSensor.cs` ein Werkstück und aktualisiert den entsprechenden Sensorstatus. Das Dashboard kann diesen Status anschließend darstellen. Gleichzeitig kann `DigitalTwinLogger.cs` die aktuellen Betriebsdaten erfassen und dauerhaft speichern.

Dadurch sind die einzelnen Funktionen voneinander getrennt, können jedoch über den gemeinsamen Datenbestand miteinander kommunizieren.

Die Architektur ermöglicht eine übersichtliche Aufteilung der Aufgaben und erleichtert die Erweiterung sowie Wartung der Anwendung.

### 4.4 Speicherung und Protokollierung der Betriebsdaten

Die Anwendung besitzt eine Logging-Funktion, mit der Betriebsdaten in einer CSV-Datei gespeichert werden.

Das Skript `DigitalTwinLogger.cs` erstellt eine Datei mit einem Zeitstempel und erfasst die Betriebsinformationen in konfigurierbaren Zeitabständen.

Folgende Daten werden protokolliert:

| Datenfeld | Bedeutung |
|---|---|
| Timestamp | Zeitpunkt der Datenerfassung |
| State | Aktueller Betriebszustand |
| Speed | Aktuelle Fördergeschwindigkeit |
| Temperature | Simulierte Motortemperatur |
| Current | Simulierter elektrischer Strom |
| ProductionCount | Anzahl der fertig transportierten Werkstücke |
| EntrySensor | Status des Eingangssensors |
| ExitSensor | Status des Ausgangssensors |

Die CSV-Dateien werden im Verzeichnis gespeichert, das Unity über `Application.persistentDataPath` bereitstellt.

Die gespeicherten Informationen können anschließend für eine einfache Auswertung und Überprüfung der Betriebsabläufe verwendet werden.

---

## 5. Einordnung in Virtual Engineering

### 5.1 Virtuell nachgebildete Komponenten

In der Simulation wurden verschiedene Bestandteile einer industriellen Förderanlage virtuell nachgebildet.

Dazu gehören:

- Förderband und Antriebsmotor
- Werkstücke
- Virtueller Eingangssensor
- Virtueller Ausgangssensor
- Temperaturüberwachung des Motors
- Überwachung des elektrischen Stroms
- Steuerungs- und Betriebszustände
- Optische Signalanzeige (Stack Light)
- Dashboard zur Überwachung und Steuerung
- Protokollierung der Betriebsdaten

Diese Komponenten bilden gemeinsam eine vereinfachte industrielle Förderanlage ab.

### 5.2 Welche Bestandteile sind reine 3D-Geometrie?

Die geometrischen Bestandteile der Simulation dienen hauptsächlich der visuellen Darstellung der realen Förderanlage.

Dazu zählen beispielsweise:

- Förderband und Anlagenaufbau
- Werkstücke
- Gehäuse und Gestellkomponenten
- Sichtbare Bestandteile des Motors

Diese Objekte bilden die räumliche Struktur der Anlage nach.

Einige geometrische Komponenten besitzen zusätzlich eine funktionale Bedeutung. Beispielsweise wird die sichtbare Rotation des Motors über ein eigenes Skript gesteuert. Die Werkstücke werden ebenfalls nicht nur dargestellt, sondern aktiv durch die Simulation bewegt.

### 5.3 Welche Bestandteile simulieren ein Verhalten?

Neben der geometrischen Darstellung enthält die Anwendung verschiedene funktionale Komponenten.

Dazu zählen:

- **Förderbandbewegung:** Werkstücke werden abhängig vom Betriebszustand und der Fördergeschwindigkeit transportiert.
- **Werkstückerzeugung:** Neue Werkstücke werden automatisch in festgelegten Zeitabständen erzeugt.
- **Sensorik:** Virtuelle Sensoren erkennen Werkstücke beim Eintritt und Austritt.
- **Maschinenüberwachung:** Temperatur und elektrischer Strom werden künstlich erzeugt und überwacht.
- **Steuerungsfunktionen:** Die Anlage kann gestartet, gestoppt, zurückgesetzt und über einen Not-Halt angehalten werden.
- **Dashboard:** Betriebsinformationen werden dargestellt und Steuerungsmöglichkeiten bereitgestellt.
- **Signalisierung:** Der aktuelle Betriebszustand wird über eine optische Signalanzeige visualisiert.
- **Datenprotokollierung:** Betriebsdaten werden regelmäßig in CSV-Dateien gespeichert.

Damit werden nicht nur die äußeren Eigenschaften einer Förderanlage nachgebildet, sondern auch ausgewählte Abläufe und Funktionen simuliert.

### 5.4 Welche Vorteile hat eine solche Simulation bei der Entwicklung einer realen Anlage?

Eine virtuelle Förderbandsimulation bietet verschiedene Vorteile bei der Planung, Entwicklung und Überprüfung industrieller Anlagen.

**Testen ohne reale Anlage**

Funktionen und Betriebsabläufe können bereits virtuell getestet werden. Dafür müssen keine physischen Maschinen oder Förderbänder aufgebaut werden.

**Simulation von Störungen**

Unterschiedliche Betriebszustände und Fehler können künstlich erzeugt und untersucht werden. Dadurch lassen sich beispielsweise Warnungen und Fehlerreaktionen überprüfen.

**Kosten- und Zeitersparnis**

Änderungen und Tests können in einer virtuellen Umgebung durchgeführt werden, wodurch Materialkosten und Entwicklungszeiten reduziert werden können.

**Visualisierung komplexer Abläufe**

Die räumliche Darstellung erleichtert das Verständnis der Anlage und ihrer einzelnen Komponenten.

**Überwachung und Auswertung**

Durch das Dashboard und die CSV-Protokollierung können Betriebszustände und Kennzahlen beobachtet und für spätere Auswertungen gespeichert werden.

**Frühzeitige Fehlererkennung**

Mögliche Probleme in den simulierten Abläufen und Steuerungsfunktionen können bereits vor der Umsetzung einer realen Anlage erkannt werden.

### 5.5 Welche Informationen würden noch benötigt, um die Simulation mit einer realen Förderanlage zu verbinden?

Damit die virtuelle Förderanlage mit einer realen industriellen Anlage verbunden werden kann, wären zusätzliche technische Komponenten und Kommunikationsschnittstellen notwendig.

Dazu zählen insbesondere:

- **Reale Sensoren:** Erfassen beispielsweise die Position von Werkstücken oder den Zustand einzelner Anlagenkomponenten.
- **Aktoren:** Ermöglichen die tatsächliche Steuerung von Motoren und anderen mechanischen Komponenten.
- **Speicherprogrammierbare Steuerung (SPS):** Übernimmt die Steuerung und Verarbeitung der Signale einer realen Anlage.
- **Kommunikationsschnittstellen:** Ermöglichen den Austausch von Daten zwischen der SPS und der Unity-Anwendung.
- **Netzwerkverbindung:** Dient zur Übertragung der Betriebsdaten in Echtzeit.
- **Reale Messwerte:** Die derzeit synthetisch erzeugten Temperatur-, Strom- und Sensorwerte müssten durch tatsächliche Messdaten ersetzt werden.

Aktuell ist die Simulation nicht mit einer realen Förderanlage oder einer SPS verbunden. Alle Sensor- und Maschinenwerte werden künstlich innerhalb der Unity-Anwendung erzeugt.

Eine zukünftige Erweiterung könnte darin bestehen, reale Betriebsdaten zu übernehmen und die virtuelle Anlage dadurch mit dem tatsächlichen Zustand einer physischen Förderanlage zu synchronisieren.

---

## 6. Zusammenfassung

Im Rahmen der Analyse wurde eine virtuelle industrielle Förderbandanlage untersucht. Die Anwendung wurde mit Unity entwickelt und bildet sowohl die geometrischen Bestandteile als auch ausgewählte funktionale Abläufe einer realen Förderanlage nach.

Werkstücke werden automatisch erzeugt, über das Förderband transportiert und mithilfe virtueller Sensoren erkannt. Zusätzlich werden Maschinenzustände wie Temperatur und elektrischer Strom simuliert und überwacht. Über ein Dashboard können die Betriebsinformationen eingesehen und verschiedene Steuerungsfunktionen ausgeführt werden.

Die Anwendung besitzt außerdem eine modulare Programmstruktur, bei der unterschiedliche C#-Skripte für die einzelnen Funktionen zuständig sind. Über den gemeinsamen Datenbestand `ConveyorTwinData` werden die Betriebsinformationen zwischen den Komponenten ausgetauscht. Mithilfe von `DigitalTwinLogger.cs` können die Betriebsdaten zusätzlich in CSV-Dateien protokolliert werden.

### Was haben wir gelernt?

Durch die Untersuchung wurde deutlich, dass eine virtuelle Förderanlage aus mehreren miteinander verbundenen Komponenten besteht. Neben der 3D-Darstellung spielen insbesondere die Bewegungssteuerung, virtuelle Sensorik, Maschinenüberwachung, Zustandsverwaltung und Datenprotokollierung eine wichtige Rolle.

Außerdem wurde die Bedeutung einer modularen Softwarearchitektur deutlich, da die verschiedenen Funktionen in getrennten Skripten implementiert werden und über gemeinsame Betriebsdaten miteinander kommunizieren.

### Warum handelt es sich um mehr als ein einfaches 3D-Modell?

Ein einfaches 3D-Modell stellt lediglich die geometrische Form und das Aussehen einer Anlage dar. Die untersuchte Anwendung geht darüber hinaus, da sie zusätzlich funktionale Abläufe und Betriebszustände simuliert.

Werkstücke werden dynamisch transportiert, Sensoren erkennen ihre Position, Maschinenzustände werden überwacht und Betriebsdaten werden gespeichert.

Dadurch entsteht eine virtuelle operationale Simulation einer industriellen Förderanlage, die zur Untersuchung und Demonstration von Produktionsabläufen verwendet werden kann.

Allerdings handelt es sich noch nicht um einen vollständig mit einer realen Anlage verbundenen Digital Twin, da keine physischen Sensoren, SPS-Systeme oder Echtzeitdaten einer tatsächlichen Förderanlage eingebunden sind.

---

**Ende der Analyse**
