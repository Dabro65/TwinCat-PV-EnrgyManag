# Höhere Automatisierungstechnik – Dokumentation der bearbeiteten Assignments

Stand: 03.11.2026

## Zweck und Geltungsbereich

Diese Datei dokumentiert die im Übungsprojekt bearbeiteten Aufgaben. Sie dient als Lernunterlage und als Kontextquelle für die spätere, eigenständige Projektarbeit. Übungsprojekt und Projektarbeit sind getrennte Projekte. Die beschriebenen Übungsbausteine sind keine automatisch freigegebene Produktionsbibliothek.

Grundlagen sind die vom Nutzer bereitgestellten Aufgabenabbildungen, die Vorlesungsfolien von Christian Henke, die im Chat konsultierte offizielle Beckhoff-Dokumentation, die gemeldeten Testergebnisse und Screenshots sowie eine lesende Prüfung der gespeicherten Projektdateien am 03.11.2026. Aufgabenstellungen sind sinngemäß zusammengefasst. Tests wurden vom Nutzer in TwinCAT ausgeführt; sie wurden für dieses Dokument nicht erneut ausgeführt. Es wurden keine bestehenden Projektdateien geändert.

Umgebung: TwinCAT 3.1 Build 4024.78, lokale SPS-Runtime auf dem Laptop, Projektmappe Uebungen mit Uebungen_ctrl und Uebungen_hmi, SPS-Projekt ueb_plc. Arbeitsbranch: uebungen.

| Aufgabe | Bearbeitungsstand |
|---|---|
| 2.1 GitHub und lokales Repository | Einrichtung und Arbeitsablauf bearbeitet |
| 2.2 Variantenmanagement | Quellen gelesen, Konzept verstanden; keine Varianten implementiert |
| 2.3 EventLogger | Implementiert und erfolgreich getestet |
| 2.4 Initialisierung | Implementiert und beim vollständigen Download ausgewertet |
| 3.1 UML und Interface-Register | Implementiert; Registrierung und lesender Interface-Zugriff getestet |
| 3.2 Abgeleitete Produktbausteine | Aufgabenanalyse und Strukturplanung; Implementierung noch offen |

## Assignment 2.1 – GitHub-Account und Repository einrichten

### Aufgabenstellung

GitHub-Account und Repository erstellen und das Repository auf den lokalen Rechner klonen. Die Vorgabe nennt eine TwinCAT-Gitignore und empfiehlt kurze lokale Pfade.

### Hintergrund und Nutzen

Git speichert nachvollziehbare Entwicklungsstände. Ein Commit dokumentiert zusammengehörige Änderungen mit einer Beschreibung. Branches erlauben getrennte Entwicklungswege. Push überträgt lokale Commits auf GitHub; Pull übernimmt entfernte Änderungen. Speichern einer Datei ist noch kein Commit, und ein Commit ist noch kein Push. Das entspricht den Vorlesungszielen Versionierung, Rückverfolgbarkeit und getestete Freigaben.

### Unsere Umsetzung

Das Repository ist mit https://github.com/Dabro65/TwinCat-PV-EnrgyManag verbunden und liegt unter:

`C:\Users\dbros\Documents\Master Mechatronik MCI\3. Semester\Hoeh_Autom`

In diesem Repository liegt die Projektmappe Uebungen. Sie enthält ein HMI-Projekt Uebungen_hmi und ein Steuerungsprojekt Uebungen_ctrl mit dem SPS-Projekt ueb_plc. Die zunächst versehentlich gewählte reine HMI-Vorlage wurde durch ein zusätzliches TwinCAT-XAE-Projekt ergänzt. Nach einer Ordnerumbenennung wurde die HMI-Projektverknüpfung korrigiert.

Ein erster Commit war erforderlich, bevor aus main ein neuer Branch erstellt werden konnte. Anschließend wurde der Branch uebungen erstellt und ausgecheckt. Commit und Push wurden über den TwinCAT-Team-Explorer durchgeführt. Beide Projekte unterliegen demselben Repository und derselben Branch-Auswahl; ein Branch ist nicht auf einen Unterordner beschränkt.

### Ergebnis und Grenzen

Die Grundstruktur und der Git-Arbeitsablauf funktionieren laut Nutzerrückmeldung. Eine .gitignore ist vorhanden; ihre vollständige TwinCAT-spezifische Abdeckung ist nicht abschließend geprüft. Vor Commits werden deshalb temporäre Dateien, Paketverzeichnisse und Build-Ausgaben kontrolliert. Der tatsächliche Push-Stand jedes späteren Arbeitsschritts wurde nicht separat verifiziert.

Quelle: [Beckhoff – Source Control](https://infosys.beckhoff.com/content/1031/tc3_sourcecontrol/index.html)

## Assignment 2.2 – Modularisierung mit TwinCAT-Variantenmanagement

### Aufgabenstellung

Variantenmanagement und bedingte Pragmas kennenlernen, um ähnliche Hardware- und Softwareausführungen ohne doppelte Pflege abbilden zu können.

### Hintergrund und Nutzen

Maschinenvarianten teilen häufig eine gemeinsame Basis und unterscheiden sich nur durch Optionen. Einzelne Projektkopien würden gemeinsame Fehlerbehebungen und Erweiterungen mehrfach erforderlich machen. Variantenmanagement bündelt die gemeinsame Code- und Konfigurationsbasis und speichert freigegebene Einstellungen variantenspezifisch. Gruppen erlauben gemeinsame Einstellungen mehrerer Varianten. Zur Aktivierung wird eine einzelne Variante ausgewählt.

Compilerdefinitionen verbinden die Konfiguration mit der SPS-Software. Bedingte Pragmas entscheiden bereits beim Übersetzen, welche Deklarationen und Codeabschnitte aufgenommen werden. Ein normales IF entscheidet dagegen während der Programmausführung.

```iecst
{IF defined(MIT_SPEICHER)}
    // Speicherfunktion dieser Ausführung
{ELSE}
    // Ausführung ohne Speicher
{END_IF}
```

### Unsere Bearbeitung

Die Quellen wurden besprochen; im Übungsprojekt wurden noch keine Varianten angelegt. Für die eigenständige PV-Projektarbeit wurden zwei mögliche Varianten ausdrücklich vorgemerkt:

- PV ohne Speicher
- PV mit Speicher

Gemeinsame Energiemanagement-Funktionen sollen gemeinsam gepflegt werden. Speicherabhängige Funktionen und Ereignisse werden gezielt zugeordnet. Diese Festlegung ist eine Planungsentscheidung, keine bereits getestete Umsetzung.

### Quellen

- [Variantenmanagement – Konzept](https://infosys.beckhoff.com/content/1031/variant_management/6325752587.html)
- [Bedingte Pragmas](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/2529795979.html)

## Assignment 2.3 – Fehlerbehandlung und TwinCAT EventLogger

### Aufgabenstellung

Einen FB_MY_EVENT_MESSAGE erstellen, der bei einer Änderung von nError auf einen Wert größer null eine Meldung mit Quelle, Schweregrad, Ereignis-ID, Fehlercode, Fehlerbeschreibung und Zeitstempel erzeugt. Die Verarbeitung soll in einer Schrittkette erfolgen und mit zwei Instanzen getestet werden.

### Hintergrund und Nutzen

Einheitliche Meldungen erleichtern Fehlersuche, Betrieb und spätere HMI-Anzeige. Eventklassen bündeln Meldungsdefinitionen. Argumente ergänzen aktuelle Werte, ohne für jeden Wert eine neue Meldung definieren zu müssen. Protokollierung und Fehlerreaktion sind getrennte Aufgaben: Ein Logeintrag allein bringt eine Anlage nicht in einen geeigneten Zustand.

### Unsere Umsetzung

Bibliotheken: Tc3_EventLogger und Tc2_EtherCAT. Im Type System wurde EC_Uebungen mit dem Ereignis ExternalError, ID 1 und dem Text `Fehlercode {0}: {1}` angelegt. Platzhalter 0 erhält nError, Platzhalter 1 erhält sError.

Eingänge des FB: sSourcename, eLevel, nEventCode, nError, sError und nTimeStamp vom Typ T_DCTIME64. Intern werden FB_TcSourceInfo und FB_TcMessage sowie Zustands- und Zwischenspeichervariablen verwendet.

| Schritt | Verarbeitung |
|---|---|
| 0 | Fehlercodeänderung erkennen; auch Wechsel auf null merken; bei positivem Fehlercode alle Ereignisdaten zwischenspeichern |
| 10 | Quelle zurücksetzen, Quellname setzen, Meldung mit Create erzeugen |
| 20 | Argumente setzen, Zeitstempel konvertieren und Send aufrufen |
| 30 | Meldungsinstanz mit Release freigeben und zu Schritt 0 zurückkehren |

Die Ereignisklassen-GUID wird korrekt über `TC_EVENTS.EC_Uebungen.ExternalError.uuidEventClass` übergeben. Ein zunächst genanntes Feld uuid war falsch und wurde korrigiert. Rückgabecodes der Methoden werden auf S_OK, also 0, geprüft.

Send erwartet Windows File Time. Der DC-Zeitstempel wird deshalb mit DCTIME64_TO_FILETIME64 konvertiert. Ein Eingangswert null wird als Sonderfall unverändert weitergegeben und lässt den Logger die aktuelle Zeit verwenden. In MAIN stammen die Zeitwerte aus F_GetCurDcTickTime64; die beim Erkennen gespeicherten Werte bleiben für die gesamte Schrittkette zusammengehörig.

### Tests und Ergebnisse

Zwei Instanzen meldeten die Quellen Wechselrichter mit Error und Speicher mit Warning. Der Nutzer bestätigte die erfolgreiche Ausgabe einschließlich Zeitstempeln.

| Fehlercodefolge | Erwartetes und bestätigtes Verhalten |
|---|---|
| 0 → 100 | Neue Meldung |
| 100 unverändert | Keine zyklische Wiederholung |
| 100 → 200 | Neue Meldung |
| 200 → 0 | Keine Meldung; Zustand wird intern aktualisiert |
| 0 → 200 | Erneute Meldung |

Die Anzeige erfolgte über TwinCAT Logged Events. Testwerte wurden mit Werte schreiben, Strg+F7, gesetzt, nicht geforct.

### Grenzen und spätere Verwendung

Die Übung wiederholt fehlgeschlagene Logger-Aufrufe ohne festgelegtes Limit. Schnelle Zwischenänderungen während der mehrzyklischen Verarbeitung werden nicht vollständig gepuffert. Für die Projektarbeit sind begrenzte Wiederholung, Fehlerausgänge und gegebenenfalls eine Warteschlange zu prüfen. Eigene Eventklassen, aussagekräftige Quellen, Argumente und kontrollierte Fehlerreaktionen sind vorgemerkt; Speicherereignisse gehören zur Variante mit Speicher.

### Quellen

- [Eventklasse](https://infosys.beckhoff.com/content/1033/tc3_eventlogger/4852757259.html)
- [Argumente](https://infosys.beckhoff.com/content/1033/tc3_eventlogger/5057915915.html)
- [FB_TcMessage](https://infosys.beckhoff.com/content/1033/tc3_eventlogger/5003041163.html)
- [Zeitkonvertierung](https://infosys.beckhoff.com/content/1031/tcplclib_tc2_ethercat/10501992459.html)

## Assignment 2.4 – Initialisierungsprozess von Steuerungssystemen

### Aufgabenstellung

FB_init, FB_reinit, FB_exit und die Attribute call_after_init, global_init_slot und call_after_global_init_slot kennenlernen. Mit zwei FB-Instanzen, zwei GVLs und einem nach globaler Initialisierung aufgerufenen Programm Zeitstempel erfassen und die Reihenfolge auswerten.

### Hintergrund und Nutzen

Vor dem ersten zyklischen SPS-Aufruf werden Speicher und Variablen initialisiert und spezielle Methoden ausgeführt. Liest ein Baustein bei seiner Initialisierung Daten einer GVL, muss diese bereits bereitstehen. Eine bewusst gewählte Reihenfolge verhindert Zugriffe auf noch nicht vorbereitete Daten.

FB_init initialisiert eine Instanz. Eine mit call_after_init markierte Methode wird nach FB_init und den initialen Zuweisungen aufgerufen. FB_reinit betrifft insbesondere entsprechende Online Changes mit Übernahme vorhandener Instanzdaten; es läuft nicht automatisch bei jedem Download. FB_exit dient dem Aufräumen beim Entfernen beziehungsweise Ersetzen von Instanzen. Die Systemmethoden werden nicht manuell aufgerufen.

### Unsere Umsetzung

FB_MyTimingSample besitzt die Methoden FB_init, FB_reinit und MyInit. Jede speichert F_GetActualDcTime64 in einer eigenen Variablen. Das Attribut call_after_init steht sowohl am FB als auch an MyInit. In MAIN wurden fbTiming1 und fbTiming2 angelegt.

GVL_InitEarly besitzt global_init_slot 40001, GVL_InitLate den Slot 59999. Beide erfassen ihre Zeit über einen Initialisierungsausdruck. POUs liegen ohne explizite Anpassung standardmäßig bei Slot 50000. Gleich belegte Slots legen keine eindeutige Reihenfolge untereinander fest.

PRG_AfterGlobalInit erfasst einen Zeitstempel bei Variableninitialisierung und einen weiteren im Implementierungsteil. Es verwendet `call_after_global_init_slot := '1'`. Diese 1 ordnet die Aufrufe nach globaler Initialisierung und ist nicht mit den GVL-Slots zu vergleichen. Zusätzlich wurde linkalways ergänzt, damit das sonst nicht zyklisch aufgerufene Programm zuverlässig eingebunden wird. Das Programm wird weder aus MAIN noch aus der Task zyklisch aufgerufen.

### Gemessene Reihenfolge

Vollständiger Download vom 31.10.2026, Zeitbasis jeweils 21:47:50:

| Reihenfolge | Messstelle | Uhrzeit |
|---|---|---|
| 1 | GVL_InitEarly.nTimestamp | 21:47:50.0530364 |
| 2 | PRG_AfterGlobalInit.nTimestampDeclaration | 21:47:50.0530690 |
| 3 | fbTiming1.nTimestampFB_init | 21:47:50.0530888 |
| 4 | fbTiming1.nTimestampMyInit | 21:47:50.0530889 |
| 5 | fbTiming2.nTimestampFB_init | 21:47:50.0530890 |
| 6 | fbTiming2.nTimestampMyInit | 21:47:50.0530891 |
| 6 | GVL_InitLate.nTimestamp | 21:47:50.0530891 |
| 7 | PRG_AfterGlobalInit.nTimestampExecution | 21:47:50.0530892 |

Gleiche Zeitstempel belegen keine Gleichzeitigkeit; die Messung kann die beiden Vorgänge nicht unterscheiden. Beide FB_reinit-Werte blieben null, angezeigt als 2000-01-01 00:00:00. Die beobachtete Reihenfolge gleich priorisierter POUs ist keine allgemeine Garantie.

### Ergebnis und Grenzen

Der geforderte Download-Versuch wurde durchgeführt und verstanden. Ein gesonderter Online-Change-Versuch und eine Messung von FB_exit wurden nicht durchgeführt. Für die eigenständige Projektarbeit ist vorgemerkt, tatsächliche Initialisierungsabhängigkeiten unter Software-Architektur zu beschreiben; das Übungsbeispiel muss nicht übernommen werden.

### Quellen

- [Initialisierungsmethoden](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/5044757003.html)
- [call_after_init](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/2529600907.html)
- [global_init_slot](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/2529649291.html)
- [call_after_global_init_slot](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/2529595531.html)
- [linkalways](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/2529686923.html)

## Assignment 3.1 – UML-Klassendiagramm und Interface-Register

### Aufgabenstellung

Eine Schnittstelle ITF_BASE mit der Property Error : UDINT definieren, durch FB_BASE implementieren und Interface-Referenzen mehrerer Instanzen in FB_REGISTER speichern. Eine Methode soll registrieren und den Arrayindex zurückgeben. Die Registrierung erfolgt automatisch in FB_init. Ein UML-Klassendiagramm bildet die Architektur ab.

### Hintergrund und Nutzen

Ein Interface beschreibt einen gemeinsamen Vertrag, ohne die interne Umsetzung festzulegen. Das Register kann Fehler beliebiger kompatibler Bausteine einheitlich lesen. Es speichert Referenzen auf die Instanzen, keine Kopien ihrer Werte. Neue Produkttypen können dieselbe Schnittstelle implementieren, ohne dass das Register ihre Details kennen muss. UML macht diese Beziehungen sichtbar und ist in TwinCAT mit den Programmobjekten synchronisiert.

### Unsere Umsetzung

Tc2_Utilities wurde hinzugefügt. Im Diagramm CD_Register wurden ITF_Base, FB_Base und FB_Register angelegt. FB_Base ist ein FUNCTION_BLOCK und implementiert ITF_Base; eine zunächst versehentlich als PROGRAM angelegte Klasse wurde korrigiert. Realisierung bedeutet IMPLEMENTS, nicht Vererbung mit EXTENDS.

FB_Base enthält nErrorId und nRegisterPos. Die Property Error besitzt:

```iecst
// Get
Error := nErrorId;
// Set
nErrorId := Error;
```

FB_Register speichert `arrRegister : ARRAY[1..100] OF ITF_Base` und den Zähler nNoOfInterfaces. AddInterfacePointer erhält ipBase und liefert UDINT:

1. Rückgabe zunächst auf 0 setzen.
2. Leere Referenz ablehnen.
3. Bereits belegte Plätze auf dieselbe Referenz prüfen; bei Fund den tatsächlichen Index zurückgeben.
4. Bei 100 belegten Plätzen ohne Zähleränderung abbrechen.
5. Andernfalls Zähler erhöhen, Referenz speichern und neuen Index zurückgeben.

Damit wurden zwei Schwachstellen der Aufgabenfolie korrigiert: Bei Duplikaten wird n statt der Gesamtzahl zurückgegeben, und ein volles Register erhöht den Zähler nicht weiter.

Eine GVL mit global_init_slot 40001 enthält `Register : FB_Register`. In FB_Base.FB_init steht:

```iecst
nRegisterPos := GVL.Register.AddInterfacePointer(ipBase := THIS^);
FB_init := TRUE;
```

THIS^ bezeichnet die aktuelle Instanz. Das früh initialisierte Register nimmt deren Interface-Referenz auf.

### Tests und Ergebnisse

Drei Instanzen fbBase1, fbBase2 und fbBase3 registrierten sich auf den Plätzen 1, 2 und 3. nNoOfInterfaces zeigte 3. Nach Schreiben von 123 auf fbBase1.nErrorId konnte der Wert über das Interface gelesen werden:

```iecst
IF GVL.Register.arrRegister[1] <> 0 THEN
    nErrorFromRegister := GVL.Register.arrRegister[1].Error;
END_IF
```

MAIN.nErrorFromRegister zeigte 123. Damit ist der lesende Zugriff durch die Interface-Referenz nachgewiesen.

### Monitoring und offene Punkte

Die direkte Watch-Anzeige von arrRegister[1].Error meldete weiterhin „Monitoring deaktiviert“. Das beeinträchtigt den nachgewiesenen Programmzugriff nicht. Im Gespräch wurde angegeben, das Monitoring-Attribut sei vorhanden. Die am 03.11.2026 gespeicherte Datei FB_Base.TcPOU enthält an Error jedoch noch ein generiertes warning-Pragma und kein monitoring-Attribut. Dieser Unterschied zwischen Gesprächsangabe und Dateistand ist nicht aufgelöst; die Datei wurde nicht geändert.

Separate Tests von Set-Zugriff, Duplikaten, leerer Referenz, vollem Register und Online-Change-Verhalten stehen aus. Für eine produktive Bibliothek muss insbesondere der Lebenszyklus von Registrierung und Instanzersetzung geprüft werden; die Duplikatprüfung allein beweist keine vollständige Online-Change-Sicherheit.

### Quellen

- [UML-Klassendiagramme](https://infosys.beckhoff.com/content/1031/tf1910_tc3_uml/1510519307.html)
- [Schnittstellen](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/4256428299.html)
- [Properties und Monitoring](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/2530335371.html)

## Assignment 3.2 – Abgeleitete Produktbausteine

### Aufgabenstellung

Mindestens vier von FB_BASE abgeleitete Produktbausteine erstellen: Melder (DE), Fühler (AE), Schalter (DA) und Regler (AA). Prozessdaten über VAR_INPUT empfangen beziehungsweise über VAR_OUTPUT ausgeben. Sensoren speichern den Zeitstempel der letzten Wertänderung. Aktoren ermitteln die Betriebsdauer aus aktiven Zyklen und Task-Zykluszeit. Die Verarbeitung erfolgt in Execute-Methoden; ein Zähler in FB_BASE.Execute kontrolliert die Nutzung von SUPER^. Alle vier Produkte sind in MAIN zu instanziieren und zu testen.

### Hintergrund und Nutzen

Vererbung ermöglicht, gemeinsame Funktionen zentral zu pflegen. Sensoren teilen die Änderungszeit-Erfassung, Aktoren die Betriebsdauerzählung. Produktbausteine ergänzen spezifische Ein- und Ausgänge. SUPER^.Execute() ruft die Implementierung der unmittelbar übergeordneten Klasse auf. Ein vollständiger Aufrufpfad bis FB_Base verhindert, dass Basisfunktionen beim Überschreiben unbeabsichtigt entfallen.

### Bisher geplante Struktur

```text
FB_Base
├── FB_SENSOR
│   ├── FB_Melder
│   └── FB_Fuehler
└── FB_ACTOR
    ├── FB_Schalter
    └── FB_Regler
```

Ein neues leeres Diagramm CD_Produkte wurde als nächster Schritt vorgeschlagen. Der vorhandene FB_Base soll hineingezogen, die sechs weiteren Typen sollen als Funktionsbausteine angelegt und anschließend mittels EXTENDS verbunden werden. Die unvollständige Formulierung „Ausgangswert ungleich“ wurde als Ausgangswert ungleich null interpretiert; dies ist eine Annahme.

### Tatsächlicher Stand und noch erforderliche Arbeit

Die Aufgabe wurde erläutert und strukturiert, aber ihre Umsetzung wurde vom Nutzer noch nicht bestätigt. Bei der Dateiprüfung am 03.11.2026 waren die sechs neuen Bausteine und CD_Produkte noch nicht vorhanden.

Offen sind Vererbungsbeziehungen, Execute-Methoden, Prozessdatenschnittstellen, Änderungsdetektion, Zeitstempel, Betriebsdauerberechnung, Basis-Aufrufzähler und Tests. Die Betriebsdauerberechnung muss die dokumentierte Einheit von _TaskInfo[].CycleTime beachten. Außerdem sind ein Aufruf pro Taskzyklus, erste Sensorwerterfassung, Analogwertänderungen und das Verhalten bei geänderter Task-Zykluszeit festzulegen. Ein vollständiger PID-Regler ist durch die bisherige Aufgabenabbildung nicht ausdrücklich spezifiziert.

### Quellen

- [Vererbte Methoden und SUPER](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/3537664139.html)
- [Aktuelle DC-Zeit](https://infosys.beckhoff.com/content/1031/tcplclib_tc2_ethercat/2268416395.html)
- [Task-Systeminformationen](https://infosys.beckhoff.com/content/1031/tc3_plc_intro/714821259.html)

## Übergreifende Erkenntnisse und Kontext für die eigenständige Projektarbeit

1. Modularisierung: Gemeinsame Logik zentral implementieren; nicht für jede Variante kopieren.
2. Varianten: PV ohne Speicher und PV mit Speicher sind ausdrücklich vorgemerkt, noch nicht implementiert.
3. Diagnose: EventLogger, eigene Eventklassen, Argumente, Quellen und Rückgabecodeprüfung berücksichtigen. Fehlerreaktionen getrennt festlegen.
4. Initialisierung: Datenabhängigkeiten bestimmen und GVLs/Instanzen entsprechend ordnen.
5. Schnittstellen: Einheitliche Fehlerabfrage über ITF_Base ermöglicht eine gemeinsame Verwaltung unterschiedlicher Produkte.
6. Vererbung: Gemeinsame Sensor-/Aktorfunktionen sind geplant, ihre Umsetzung ist noch offen.
7. Git: Änderungen nachvollziehbar committen und getestete Stände bewusst freigeben. Ein im Chat vorgeschlagener Commit ist kein Nachweis, dass er tatsächlich durchgeführt wurde.

Die lokale Runtime konnte zunächst wegen aktivem Hyper-V/VBS nicht starten. Der Nutzer deaktivierte den Hypervisorstart und startete Windows neu; anschließend liefen die SPS-Tests. Diese Anpassung ist eine Besonderheit des Entwicklungsrechners und keine fachliche Anforderung an die spätere PV-Anwendung. Hypervisorabhängige Schutz- und Virtualisierungsfunktionen sind bei dieser Einstellung eingeschränkt. Eine Usermode-Runtime wurde als alternative Testumgebung besprochen, aber nicht eingerichtet.

Die Vorlesungsunterlagen befinden sich im Unterordner scripte als `2026_MCI_Mechanik_Vorlesung_Hoehere_Automatisierungstechnik_Vorlesung1_1.0.0.0.pdf`. Dieses Dokument ergänzt die Originalaufgaben und offiziellen Quellen; es ersetzt sie nicht. Bei Weitergabe als Projektkontext sollen insbesondere die Statusangaben und offenen Tests beibehalten werden.
