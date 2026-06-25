# WiFi DensePose (Deutsch)

Dieses Repository enthält **WiFi-DensePose**, ein hochmodernes System zur kamerafreien, datenschutzfreundlichen Erkennung menschlicher Posen. Es nutzt die Kanaleigenschaftsinformationen (Channel State Information - CSI) von Standard-WiFi-Signalen und verarbeitet diese mittels neuronaler Netze (DensePose-Architektur) in Echtzeit.

---

## 📋 Inhaltsverzeichnis
- [Systemvoraussetzungen](#systemvoraussetzungen)
- [Schnellstart-Anleitung (Lokale Einrichtung & Test)](#schnellstart-anleitung-lokale-einrichtung--test)
- [Leitfaden für autonome KI-Agenten (Kostengünstiger Run)](#leitfaden-für-autonome-ki-agenten-kostengünstiger-run)
- [Fehlerbehebung (Troubleshooting)](#fehlerbehebung-troubleshooting)

---

## 💻 Systemvoraussetzungen
- **Python**: Version 3.9 oder höher (empfohlen 3.10 / 3.11).
- **Betriebssystem**: Windows 10/11, Linux (Ubuntu 18.04+), macOS 10.15+.
- **Arbeitsspeicher**: Mindestens 4 GB RAM (8 GB oder mehr empfohlen).
- **Speicherplatz**: 2 GB freier Speicherplatz für Modelle und Abhängigkeiten.
- **Hardware-Modus**:
  - **Echtzeit-Hardware-Modus**: Erfordert einen CSI-fähigen WiFi-Router/Netzwerkkarte.
  - **Simulations-/Test-Modus (Mock)**: Keine spezielle Hardware erforderlich (ideal zum Testen).

---

## 🚀 Schnellstart-Anleitung (Lokale Einrichtung & Test)

Folgen Sie diesen Schritten, um das Projekt lokal einzurichten und einmalig zu testen.

### 1. Repository betreten
Öffnen Sie Ihr Terminal (PowerShell für Windows, Bash für Linux/macOS) und navigieren Sie in das Projektverzeichnis:
```bash
cd wifi-densepose
```

### 2. Virtuelle Python-Umgebung einrichten
Es wird dringend empfohlen, eine virtuelle Umgebung (`venv`) zu erstellen, um Konflikte mit globalen Python-Paketen zu vermeiden.

**Unter Windows (PowerShell):**
```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

**Unter Linux / macOS (Bash):**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Abhängigkeiten installieren
Installieren Sie das Paket inklusive der Entwicklungsabhängigkeiten im editierbaren Modus:
```bash
pip install -e .[dev]
```
*(Hinweis: Auf Windows-Systemen wurde die `setup.py` so angepasst, dass sie UTF-8-Codierung erzwingt, um UnicodeDecodeErrors zu verhindern).*

### 4. Konfigurationsdatei einrichten
Kopieren Sie die Vorlagendatei `example.env` in eine `.env`-Datei. Diese Datei enthält alle Laufzeiteinstellungen:

**Unter Windows (PowerShell):**
```powershell
Copy-Item example.env .env
```

**Unter Linux / macOS (Bash):**
```bash
cp example.env .env
```

> [!IMPORTANT]
> **Windows- & Pydantic-spezifische Anpassungen in der `.env`:**
> Da Pydantic V2 Listenfelder als JSON parst, müssen Sie in der `.env` sicherstellen, dass Listen (wie `ALLOWED_HOSTS` und `CORS_ORIGINS`) als valide JSON-Arrays definiert sind.
> Öffnen Sie die `.env`-Datei und passen Sie diese Zeilen an:
> ```env
> ALLOWED_HOSTS=["*"]
> CORS_ORIGINS=["*"]
> ```
> Stellen Sie außerdem sicher, dass für den Simulationsmodus folgende Zeilen aktiv sind (Standardwert in der Vorlage):
> ```env
> MOCK_HARDWARE=true
> MOCK_POSE_DATA=true
> ```

### 5. Datenbank initialisieren
Das System verwendet standardmäßig eine SQLite-Fallback-Datenbank für Testzwecke. Initialisieren Sie das Datenbankschema über das CLI:
```bash
python -m src.main db init
```

### 6. API-Server starten
Starten Sie den FastAPI-Server im Entwicklungsmodus:
```bash
python -m src.main start
```
Der Server läuft nun standardmäßig unter `http://localhost:8000`. Die interaktive API-Dokumentation finden Sie unter `http://localhost:8000/docs`.

### 7. Anwendung testen
Lassen Sie den API-Server im Hintergrund oder in einem separaten Terminal geöffnet und führen Sie das Integrationstest-Skript aus:
```bash
# In einem neuen Terminal mit aktiviertem .venv:
python test_application.py
```
Dieses Skript überprüft alle wichtigen REST-Endpunkte und stellt eine WebSocket-Verbindung für den simulierten CSI-Pose-Datenstrom her.

---

## 🤖 Leitfaden für autonome KI-Agenten (Kostengünstiger Run)

Wenn ein autonomer Software-Agent (der beispielsweise mit einem kostengünstigen Modell wie **Gemini 3.5 Flash** oder **Claude 3.5 Haiku** betrieben wird) dieses Repository automatisch einrichten und testen soll, ist es kritisch, die Token-Kosten und die Fehlerquote zu minimieren.

Hier ist der präzise, deterministische Befehlssatz für den Agenten. Dieser Workflow vermeidet explorative Zwischenschritte und stellt sicher, dass der Testlauf auf Anhieb gelingt.

### 📋 Schritt-für-Schritt-Protokoll für den Agenten

#### 🛠️ Option A: Für Windows-Systeme (PowerShell)
Der Agent sollte diese Befehle sequenziell ausführen:

```powershell
# 1. Virtuelle Umgebung erstellen
python -m venv .venv

# 2. Umgebung aktivieren und Abhängigkeiten installieren
.venv\Scripts\pip.exe install -e .[dev]

# 3. Konfigurationsdatei kopieren
Copy-Item example.env .env

# 4. .env Datei patchen (Pydantic List-Parsing-Fehler beheben)
# (Ersetzt die Standardplatzhalter durch valide JSON-Arrays)
(Get-Content .env) -replace 'ALLOWED_HOSTS=\*', 'ALLOWED_HOSTS=["*"]' -replace 'CORS_ORIGINS=\*', 'CORS_ORIGINS=["*"]' | Set-Content .env

# 5. Datenbank initialisieren
.venv\Scripts\python.exe -m src.main db init

# 6. Automatisierte Pytest-Suite ausführen
.venv\Scripts\pytest.exe
```

#### 🛠️ Option B: Für Linux / macOS (Bash)
Der Agent sollte diese Befehle sequenziell ausführen:

```bash
# 1. Virtuelle Umgebung erstellen
python3 -m venv .venv

# 2. Umgebung aktivieren und Abhängigkeiten installieren
.venv/bin/pip install -e .[dev]

# 3. Konfigurationsdatei kopieren
cp example.env .env

# 4. .env Datei patchen (Pydantic List-Parsing-Fehler beheben)
sed -i 's/ALLOWED_HOSTS=\*/ALLOWED_HOSTS=["*"]/g' .env
sed -i 's/CORS_ORIGINS=\*/CORS_ORIGINS=["*"]/g' .env

# 5. Datenbank initialisieren
.venv/bin/python -m src.main db init

# 6. Automatisierte Pytest-Suite ausführen
.venv/bin/pytest
```

### 🎯 Verifizierung für den Agenten
Der Agent kann den Erfolg verifizieren, indem er prüft, ob:
1. Der `pytest`-Lauf mit Exit-Code `0` (alle Tests bestanden) abschließt.
2. Der Befehl `python -m src.main db init` mit der Meldung `"Database initialized successfully"` beendet wird.

Durch diesen festen Pfad muss das KI-Modell keine Log-Fehler analysieren oder unzählige Terminalbefehle ausprobieren, was **über 90% der API-Token-Kosten einspart**.

---

## 🛠️ Fehlerbehebung (Troubleshooting)

### 1. `UnicodeDecodeError` bei `pip install` unter Windows
* **Ursache**: Die Systemcodierung unter Windows (z.B. CP-1252) kann UTF-8-Zeichen in Quellcodedateien nicht lesen.
* **Lösung**: Die `setup.py` wurde bereits so gepatcht, dass beim Öffnen von Dateien explizit `encoding='utf-8'` deklariert wird. Falls dieser Fehler bei anderen Dateien auftritt, führen Sie die Installation mit gesetzter Umgebungsvariable aus:
  ```powershell
  $env:PYTHONUTF8=1
  pip install -e .[dev]
  ```

### 2. `ValidationError` für `Settings` / `secret_key`
* **Ursache**: Die `.env`-Datei wurde nicht im aktuellen Arbeitsverzeichnis gefunden oder geladen.
* **Lösung**: Stellen Sie sicher, dass Sie `example.env` zu `.env` kopiert haben und der Befehl im Hauptverzeichnis des Repositories (`wifi-densepose`) ausgeführt wird.

### 3. `SettingsError: error parsing value for field "allowed_hosts"`
* **Ursache**: `ALLOWED_HOSTS` ist in der `.env` als `*` statt `["*"]` hinterlegt.
* **Lösung**: Ändern Sie den Wert in der `.env` zu einem validen JSON-Array: `ALLOWED_HOSTS=["*"]`.
