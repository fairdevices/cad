# Entwicklungstagebuch WaMa 0.1

[toc]


## Einleitung

Es soll eine universale Waschmaschinen-Steuerung entstehen!


## Lastenheft

-   Anschluss: 230 V, 16 A?
-   Steuerspannungen auf Platine: 230 V, 3,3 V für Arduino


## Komponenten


### Netzteil

Das Netzteil soll im Sinne eines robusten Design aus einem Trafo mit Gleichrichter für die 12-V-Ebene sein. Die 12-V-Ebene versorgt alle Relais mit Spannung.

Verbraucher zur Lastschätzung

| | |
|---------------------------|-----------|-----------|
| Kaltwasserventil-Relais   | 0.1A      | 1 W       |
| Warmwasserventil-Relais   | 0.1A      | 1 W       |
| Relais ?                  |           |           |
| ESP 32                    | 0,2 A     | 0,7 W     |
| P                         |           | 2 Watt?   |

Der 12 Vdc-Ebene wird ein Linearregler (500mA) auf 3,3 V nachgeschaltet, um die spannungsstabile Energieversorgung des ESP32 zu gewährleisten.

Die Kalt- und Warmwasser-Relais sind schalten nicht gleichzeitig. So lässt sich der Gleichzeitigkeitsfaktor und damit die Bemessungsleistung des Netzteils reduzieren.

Funktionen der Komponenten

SVR100 ... Variator zum Schutz gegen Überspannungen aus dem Netz und die Selbstinduktion beim Ausschalten des Trafos T100


### Temperaturreglung

#### Allgemeines

Die Wassertemperatur wird konventionell durch eine Widerstandsheizung (230V, 2kW, → 10A) realisiert. Die Temperaturmessung erfolgt über ein NTC-Element. Die meisten Waschmaschinen verwenden NTC-Fühler mit einem Widerstand von etwa 4,8 kΩ bei 20°C. Wenn in einer bestimmten Waschmaschine ein anderes Modell verwendet wird, dann muss dies entsprechend in der Software für den µC hinterlegt werden.

Die Regelung übernimmt der zentrale µC. Das elektrische Schaltelement ist ein Relais (12 V/230).

Verschiedene Schaltelemente haben jeweils ihre Vor- und Nachteile:

| Vorteile                              | Nachteile                         |
|:--------------------------------------|:----------------------------------|
| **Relais**                                                                |
| - galvanische Trennung                | - Abbrand an den Schaltelektroden \
                                            und mögliches Kontaktkleben \
                                            → vorzeitiger Ausfall           |
| - geringer elektrischer Widerstand \
    → geringere thermische Verluste, \
    keine Kühlung notwendig             |                                   |
| - geringere Kosten                    |                                   |
| **Triacs**                                                                |
| - keine mechanisch beweglichen Teile  | - hohe thermische Verluste 1W/A \
                                            → Kühlkörper notwendig          |
| - kein Lichtbogen und damit Alterung  | - keine galvanische Trennung \
                                            → Optokoppler notwendig         |

Aufgrund des Entwicklungsziels einer möglichst hohen Langlebigkeit wird sich für den Einsatz eines Triacs entschieden. Die 10W Verluste bei einem Strom von 10 A entsprechen vertretbaren 10W/2000W = 0,5 % Verlustleistung. Ein ausreichend großer Kühlkörper wird durch Versuche ermittelt.


#### Randbedingungen für Regelung

Wasservolumen, geschätzt, 10-25 L

Wärmekapazität Wasser: 4,19 kJ/(kg K)

Worst case: 90-°C-Wäsche, dT=80 K, 10..25 kg → 30\...60 min Heizphase ohne Thermische Verluste

Regelung mit Relais: EIN-Schaltdauer im Bereich von Minuten, 2-Punkt-Regelung mit +/- 2 °C ausreichend

Regelung mit Thyristor: Ein-Schaltdauer funktionsbedingt 2 Sinus-Halbwellen = 20 ms, jede Periode müssten die Thyristoren neu getriggert werden.


### Motorsteuerung


### Magnetventile für Kalt- und Warmwasser

Elektrische Kenndaten für Ventile: 230 V, xxx? A

```math
f_g = \frac{ 0,35 }{ 30 MHz }
```


## Fehlererkennungsstrategien

Ähnlich wie in aktuellen PKW, die einem sagen, dass die hintere rechte Bremsleuchte defekt ist, sollte die Steuerung sich selbst überprüfen und Fehler in den angesteuerten Komponenten erkennen können.


### Dämpfer

Waschtrommel ist eine Feder-Masse-Schwinger → Eigenfrequenz → ist eine entsprechende Stromharmonische im Motorstrom zu hoch, dann Dämpfer verschlissen.


### Hauptlager

Akustische Signal bzw. Körperschall sollten sich detektieren lassen.


### Heizung

Kein Strom, dann Sicherung raus, weil Schluss gegen Masse


### Motor / Laugenpumpe

Kein Strom, dann Bürsten defekt?


## Fragen

- 12V vs 24 V bei den Relais?
- ESP32 vs Arduino?
- Relais vs Thyristoren vs SSR?