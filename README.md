# Home Assistant – Package-basierte Konfiguration

Private Home-Assistant-Konfiguration für eine Wohnung in Stuttgart. Das System läuft auf **HA OS** und ist vollständig **package-basiert** aufgebaut – jede Funktionseinheit ist in einer eigenen YAML-Datei gekapselt.

## Raumstruktur

| Kürzel | Raum |
|--------|------|
| AZ | Arbeitszimmer (Studio) |
| BZ | Badezimmer |
| WZ | Wohnzimmer |
| KU | Küche |
| KI | Kinderzimmer |
| SZ | Schlafzimmer |
| ZS | Zentral / Systemweit |

## Packages

### Heizung (V4.0)

| Datei | Beschreibung |
|-------|-------------|
| `heating_global.yaml` | Globale Steuerung: Master-Schalter, Nacht-/Frühnachtschaltung, Außentemperatur-Schwelle |
| `heating_az.yaml` | Raumsteuerung Arbeitszimmer |
| `heating_bz.yaml` | Raumsteuerung Badezimmer |
| `heating_wz.yaml` | Raumsteuerung Wohnzimmer |
| `heating_ku.yaml` | Raumsteuerung Küche |
| `heating_ki.yaml` | Raumsteuerung Kinderzimmer |
| `heating_sz.yaml` | Raumsteuerung Schlafzimmer |
| `heating_status.yaml` | Status-Sensoren für alle Räume (Zusammenfassung, Dashboard-Anzeige) |

Thermostate: 8× Tado° Smart Radiator Thermostat X via Matter over Thread.

### Beleuchtung & Nachtlicht

| Datei | Beschreibung |
|-------|-------------|
| `nachtlicht.yaml` | Nachtlichtsteuerung für alle Räume (V1.5) – zeitgesteuert mit Pause-Funktion |

### Anwesenheit & Urlaub

| Datei | Beschreibung |
|-------|-------------|
| `presence.yaml` | Anwesenheitssteuerung – GPS + WLAN, Nahbereich-Vorwärmzone, Abfahrtsverzögerung |
| `urlaub_simulation.yaml` | Urlaubssteuerung mit Anwesenheitssimulation |

### Energie

| Datei | Beschreibung |
|-------|-------------|
| `energie_cockpit.yaml` | Energie-Cockpit (V2.1) – Gesamtüberblick Stromverbrauch |
| `global_energy.yaml` | Globaler Strompreis via aWATTar API |

### Geräte

| Datei | Beschreibung |
|-------|-------------|
| `az_studio_gear.yaml` | Studio-Equipment im AZ – Template-Switches für Audio-Gear (SSL, Eurorack, Neumann, etc.) |
| `bz_waschmaschine.yaml` | Waschmaschinen-Tracking (Laufzeit, Status, Benachrichtigung) |
| `ku_spuelmaschine.yaml` | Spülmaschinen-Tracking (Laufzeit, Status, Benachrichtigung) |
| `battery_tracking.yaml` | Batterie-Monitoring für alle batteriebetriebenen Geräte |

### Klima & Lüftung

| Datei | Beschreibung |
|-------|-------------|
| `klima_uebersicht.yaml` | Kombinierte Klima-Übersicht (Heizung + Fenster) |
| `lueftung.yaml` | Stoßlüften-Automation für alle Räume |

### Dashboard & UI

| Datei | Beschreibung |
|-------|-------------|
| `burger_menue.yaml` | Helper für Bubble Card Custom-Dropdown-Modul |
| `zusammenfassung_sensoren.yaml` | Zusammenfassungs-Sensoren für Bubble Card Anzeigen |

## Namenskonventionen

**Entity-IDs:** `{domain}.{raum}_{geraet}_{nr}` – z.B. `switch.az_ssl`, `climate.wz_heizung_01`

**Friendly Names:** `{RAUM} – {Gerätename}` – z.B. `AZ – SSL 360 Konsole`, `WZ – Heizung 01`

## Dashboard

Primäres Dashboard basiert auf Bubble Card 3 und Mushroom Cards mit dem Theme *Caule Black Aqua Glass Custom*. Weitere Komponenten: Mini-Graph-Card, ApexCharts, Stack-in-Card, Card-Mod, Auto-Entities.

## Integration mit Claude

Die Datei `filelist.txt` im Root des Repos enthält eine maschinenlesbare Liste aller Package-Dateinamen. Sie wird von Claude (Anthropic) genutzt, um zu Beginn jeder Session automatisch den aktuellen Stand aller Packages zu laden – ohne manuelles Hochladen.

## Auto-Push

Änderungen werden per `shell_command` aus Home Assistant heraus committed und gepusht:

```yaml
shell_command:
  git_push_config: >-
    cd /config && git add .gitignore filelist.txt README.md
    packages/ configuration.yaml &&
    git diff --cached --quiet ||
    (git commit -m "auto-update $(date +%Y-%m-%d_%H:%M)" && git push)
```
