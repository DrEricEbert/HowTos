Um aus einem Python-Programm eine **ausführbare .exe-Datei** für Windows zu erstellen, kannst du das bewährte Tool **PyInstaller** verwenden. Damit kannst du dein Projekt in eine portable `.exe`-Datei umwandeln, die auch auf Systemen **ohne Python-Installation** läuft.

---

## ✅ Schritt-für-Schritt-Anleitung: `.exe` aus Python erstellen

### 🔧 Voraussetzungen

- Du hast **Python** installiert (z. B. Python 3.10 oder 3.11)
- Du nutzt **Windows**
- Dein Python-Programm ist z. B. `main.py` oder `app.py`

---

## 🚀 Schritt 1: PyInstaller installieren

Öffne dein Terminal oder die Eingabeaufforderung (CMD) und installiere PyInstaller:

```bash
pip install pyinstaller
```

---

## 🚀 Schritt 2: `.exe` erzeugen

Navigiere zu dem Ordner, in dem deine Python-Datei liegt:

```bash
cd C:\Pfad\zu\deinem\projekt
```

Dann führe folgenden Befehl aus:

```bash
pyinstaller --onefile app.py
```

### 🔍 Erklärung der Optionen:
- `--onefile`: erstellt **eine einzige .exe-Datei**
- `app.py`: dein Python-Skript (ersetze durch deinen Dateinamen)

---

## 📁 Nach dem Build findest du:

- Im Projektordner wird ein neuer Ordner **`dist/`** erstellt.
- Darin liegt deine ausführbare Datei:  
  `dist/app.exe`

Du kannst diese `.exe` jetzt auf anderen Windows-Rechnern starten.

---

## 💡 Optional: Mit Icon & ohne Konsole

Wenn du ein Icon einbinden und die schwarze Konsole ausblenden willst (z. B. bei GUI-Apps wie PyQt oder Tkinter), kannst du folgendes verwenden:

```bash
pyinstaller --onefile --noconsole --icon=icon.ico app.py
```

- `--noconsole`: keine CMD-Konsole anzeigen (nur bei GUI-Apps verwenden!)
- `--icon=icon.ico`: fügt ein eigenes Icon hinzu (muss `.ico`-Datei sein)

---

## ⚠️ Hinweise:

- Achte darauf, dass **alle benötigten Dateien (z. B. Bilder, JSONs, Datenbanken)** sich im richtigen Pfad befinden oder beim Build berücksichtigt werden.
- Für **PySide6 oder PyQt6** (wie in deinem Fall) funktioniert PyInstaller gut – beachte aber, dass die `.exe` relativ groß sein kann (~60–100 MB), weil alle Qt-Bibliotheken eingebunden werden.

---

## 🧪 Testen

Starte die `.exe` aus dem `dist`-Ordner – sie sollte genauso funktionieren wie dein Python-Skript.

---

## 🧹 Aufräumen (optional)

PyInstaller erzeugt viele Dateien. Diese kannst du nach dem Build löschen:

```bash
build/
__pycache__/
app.spec
```

Nur der `dist/`-Ordner ist relevant.

---

## 🆘 Fehlerbehebung

Falls du Fehler bekommst:
- Lies die Ausgabe im Terminal genau.
- Nutze `--debug` für mehr Details.
- Stelle sicher, dass alle Imports korrekt sind.
- Bei PySide6 kann manchmal `--hidden-import` nötig sein:
  
  ```bash
  pyinstaller --onefile --noconsole --hidden-import PySide6.QtWidgets app.py
  ```

---

## ✅ Bonus: Build-Skript

Wenn du häufiger baust, erstelle dir ein kleines Skript `build.bat`:

```bat
pyinstaller --onefile --noconsole --icon=icon.ico app.py
pause
```

Dann doppelklicken – fertig.

---

Wenn du möchtest, kann ich dir auch eine passende `.spec`-Datei oder ein Build-Script für dein konkretes Projekt erstellen. Sag einfach Bescheid!