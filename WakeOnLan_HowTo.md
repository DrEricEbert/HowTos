**Wake-on-LAN (WOL)**

ermöglicht es dir, einen ausgeschalteten Computer über ein Netzwerk einzuschalten. Hier ist eine Erklärung, wie du es unter Windows und Linux einrichtest und verwendest:

**Grundsätzliche Voraussetzungen (für Windows und Linux):**

1.  **BIOS/UEFI-Einstellungen:**
    *   Du musst WOL im BIOS/UEFI deines Computers aktivieren. Die genaue Bezeichnung variiert je nach Hersteller, aber suche nach Optionen wie "Wake-on-LAN", "Power On by PCI-E/PCI", "Remote Wake Up" oder ähnlichem. Stelle sicher, dass diese Option aktiviert ist.
    *   Deaktiviere, falls vorhanden, Optionen wie "Deep Sleep" oder "ErP/EuP Ready", die den Stromverbrauch im ausgeschalteten Zustand so stark reduzieren, dass WOL nicht mehr funktioniert.

2.  **Netzwerkkarte:**
    *   WOL funktioniert normalerweise nur über kabelgebundene Ethernet-Verbindungen (LAN). WLAN-Karten unterstützen WOL oft nicht zuverlässig oder gar nicht im ausgeschalteten Zustand.
    *   Stelle sicher, dass du die aktuellsten Treiber für deine Netzwerkkarte installiert hast.

3.  **Netzwerkkonfiguration:**
    *   Dein Computer und das Gerät, von dem aus du das WOL-Signal sendest (z. B. ein anderes Gerät im Netzwerk oder ein Router), müssen sich im selben lokalen Netzwerk (Subnetz) befinden.
    *   Du benötigst die MAC-Adresse der Netzwerkkarte des Computers, den du aufwecken möchtest.  Die MAC-Adresse ist eine eindeutige Hardware-Adresse.

**Windows-spezifische Einrichtung:**

1.  **Netzwerkkarteneinstellungen:**
    *   Öffne den Geräte-Manager (drücke `Windows-Taste + X`, dann `M`).
    *   Erweitere den Eintrag "Netzwerkadapter".
    *   Mache einen Rechtsklick auf deine Ethernet-Netzwerkkarte und wähle "Eigenschaften".
    *   Gehe zum Tab "Energieverwaltung".
    *   Aktiviere die folgenden Optionen:
        *   "Gerät kann den Computer aus dem Ruhezustand aktivieren"
        *   "Nur Magic Packet kann Computer aus dem Ruhezustand aktivieren" (Wichtig: Diese Option stellt sicher, dass der Computer nur auf WOL-Pakete reagiert und nicht auf beliebigen Netzwerkverkehr.)

2.  **Schnellstart deaktivieren (empfohlen):**
    *   Windows' Schnellstart-Funktion kann WOL manchmal stören.  Es ist oft besser, sie zu deaktivieren:
        *   Öffne die Systemsteuerung (suche nach "Systemsteuerung").
        *   Gehe zu "Hardware und Sound" -> "Energieoptionen" -> "Auswählen, was beim Drücken von Netzschaltern geschehen soll".
        *   Klicke auf "Einige Einstellungen sind momentan nicht verfügbar".
        *   Entferne das Häkchen bei "Schnellstart aktivieren (empfohlen)".
        *   Klicke auf "Änderungen speichern".

3. **MAC-Adresse herausfinden:**
   * Öffne die Eingabeaufforderung (cmd) als Administrator.
   * Tippe `ipconfig /all` und drücke Enter.
   * Suche nach dem Abschnitt für deine Ethernet-Verbindung (z.B. "Ethernet-Adapter Ethernet").
   * Die MAC-Adresse steht neben "Physikalische Adresse" (z.B. 00-1A-2B-3C-4D-5E). Notiere sie dir.

**Linux-spezifische Einrichtung (Beispiel für Ubuntu/Debian):**

1.  **`ethtool` installieren (falls nicht vorhanden):**
    ```bash
    sudo apt update
    sudo apt install ethtool
    ```

2.  **WOL aktivieren (temporär):**
    *   Finde den Namen deiner Netzwerkschnittstelle heraus (meistens `eth0`, `enp0s3` oder ähnlich):
        ```bash
        ip link show
        ```
    *   Aktiviere WOL mit `ethtool`:
        ```bash
        sudo ethtool -s <interface_name> wol g
        ```
        (Ersetze `<interface_name>` mit dem tatsächlichen Namen deiner Schnittstelle, z.B. `sudo ethtool -s eth0 wol g`)
    *   **Wichtig:** Diese Einstellung ist nur temporär und wird beim nächsten Neustart zurückgesetzt.

3.  **WOL dauerhaft aktivieren (verschiedene Methoden):**

    *   **Methode 1:  `/etc/network/interfaces` (ältere Debian/Ubuntu-Systeme):**
        *   Öffne die Datei `/etc/network/interfaces` mit einem Texteditor als Root:
            ```bash
            sudo nano /etc/network/interfaces
            ```
        *   Füge die Zeile `ethernet-wol g` *innerhalb* des Blocks für deine Netzwerkschnittstelle hinzu.  Beispiel:
            ```
            auto eth0
            iface eth0 inet dhcp
                ethernet-wol g
            ```
        *   Speichere die Datei und starte das Netzwerk neu:  `sudo systemctl restart networking` (oder starte den Computer neu).

    *   **Methode 2: `systemd` (modernere Systeme):**
        *   Erstelle eine neue systemd-Service-Datei:
            ```bash
            sudo nano /etc/systemd/system/wol.service
            ```
        *   Füge folgenden Inhalt in die Datei ein (ersetze `<interface_name>`):
            ```ini
            [Unit]
            Description=Enable Wake On LAN

            [Service]
            Type=oneshot
            ExecStart=/sbin/ethtool -s <interface_name> wol g

            [Install]
            WantedBy=basic.target
            ```
        *   Aktiviere und starte den Service:
            ```bash
            sudo systemctl enable wol.service
            sudo systemctl start wol.service
            ```

    *   **Methode 3: NetworkManager (wenn du NetworkManager verwendest):**
        *   Öffne die NetworkManager-Verbindungseinstellungen (normalerweise über die grafische Oberfläche erreichbar).
        *   Suche in den Einstellungen deiner Ethernet-Verbindung nach einer Option für "Wake-on-LAN" und aktiviere sie.  Die genaue Position und Bezeichnung kann je nach Desktop-Umgebung variieren.

4.  **MAC-Adresse herausfinden:**
    ```bash
    ip link show <interface_name> | grep ether
    ```
    (Ersetze `<interface_name>` mit dem Namen deiner Schnittstelle, z.B. `ip link show eth0 | grep ether`). Die MAC-Adresse wird nach "link/ether" angezeigt.

**Senden des WOL-Signals (Magic Packet):**

Es gibt viele Möglichkeiten, ein WOL-Signal zu senden:

*   **Von einem anderen Computer im selben Netzwerk:**
    *   **Windows:**  Es gibt zahlreiche kostenlose Tools, z.B. "Wake On LAN GUI", "NirSoft WakeMeOnLan", oder du kannst PowerShell verwenden:
        ```powershell
        # PowerShell (ersetze die MAC-Adresse)
        Send-Wol -Mac "00:1A:2B:3C:4D:5E" -Broadcast 192.168.1.255  # 192.168.1.255 ist die Broadcast-Adresse deines Subnetzes.
        ```
    *   **Linux:** Verwende das `wakeonlan`-Tool (muss oft installiert werden, z.B. `sudo apt install wakeonlan`):
        ```bash
        wakeonlan 00:1A:2B:3C:4D:5E
        ```
        Oder `etherwake` (muss evtl. auch erst installiert werden):
        ```bash
        sudo etherwake -i <interface_name> 00:1A:2B:3C:4D:5E
        ```

*   **Von einem Smartphone:**  Es gibt zahlreiche WOL-Apps für Android und iOS.  Du musst die MAC-Adresse und oft auch die IP-Adresse oder Broadcast-Adresse deines Netzwerks eingeben.

*   **Vom Router:** Viele Router (z.B. Fritz!Box) haben eine integrierte WOL-Funktion.  Du findest sie normalerweise in den Einstellungen unter "Heimnetz" oder "Netzwerk".  Du kannst dann den Computer direkt über die Router-Oberfläche aufwecken.

*  **Über das Internet:** Das ist komplizierter, da du Portweiterleitung (Port Forwarding) auf deinem Router einrichten musst und dein Router eine statische öffentliche IP-Adresse oder einen Dynamic-DNS-Dienst benötigt.  Es ist auch unsicherer.  Statt Port 9 (UDP) direkt weiterzuleiten, solltest du aus Sicherheitsgründen einen anderen Port verwenden und diesen dann auf Port 9 an den Zielcomputer weiterleiten.  Einige Router bieten auch spezielle WOL-Proxy-Funktionen an, die sicherer sind.
