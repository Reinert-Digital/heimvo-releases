# Sicherheit

Heimvo ist ein Sicherheitsprodukt. Wir nehmen Schwachstellen ernst und danken für jede
verantwortungsvolle Meldung.

## Unterstützte Versionen

Sicherheitskorrekturen gibt es für die jeweils neueste Version (aktuell `0.17.x`).
Bitte aktualisieren Sie vor einer Meldung auf die
[neueste Version](https://github.com/Reinert-Digital/heimvo-releases/releases/latest).

## Schwachstelle melden

**Bitte melden Sie Sicherheitsprobleme nicht als öffentliches Issue.**

- Nutzen Sie die **[vertrauliche Meldung über GitHub](../../security/advisories/new)**, oder
- schreiben Sie an **contact@reinert-digital.de** (Betreff „Security“) mit Beschreibung,
  Schritten zum Nachstellen und möglichen Auswirkungen.

| Schritt | Ziel |
| --- | --- |
| Eingangsbestätigung | innerhalb von 3 Werktagen |
| Erste Einschätzung | innerhalb von 7 Werktagen |
| Korrektur oder Gegenmaßnahme | je nach Schwere, wir halten Sie auf dem Laufenden |

Auf Wunsch nennen wir Sie in den Release-Notes, sonst bleiben Sie anonym.

## Umfang

Dazu gehören: der Heimvo-Dienst, das Dashboard, der Windows-Installer, die Docker-Images und die
Standardkonfiguration.

Nicht dazu gehören: Schwachstellen in Komponenten Dritter (Npcap, Suricata, CrowdSec, …), bitte
direkt beim jeweiligen Projekt melden. Angriffe, die bereits Administrator- oder Root-Rechte auf
dem Heimvo-Rechner voraussetzen, liegen in der Regel außerhalb.

## Hinweise für den sicheren Betrieb

- Prüfen Sie vor der Installation die SHA-256-Prüfsumme der Setup-Datei
  (`Heimvo-Setup-<version>.exe.sha256` im Release).
- Machen Sie das Dashboard nie direkt aus dem Internet erreichbar. Für Fernzugriff ein VPN oder
  einen Reverse Proxy mit TLS nutzen und dann `HEIMVO_COOKIE_SECURE=true` setzen.
- Geben Sie jeder Person nur die Rolle, die sie braucht (Betrachter, Analyst, Admin).
- Zugangsdaten für Benachrichtigungen (ntfy, SMTP, …) sind verschlüsselt gespeichert, der
  Schlüssel liegt als `heimvo.key` neben der Datenbank. Sichern Sie beide Dateien gemeinsam und
  schützen Sie die Sicherung wie ein Passwort.
- Lassen Sie Heimvo im Modus „Beobachten“, bis die Ausnahmeliste passt; „Schützen“ kann auch
  erwünschten Verkehr sperren.

**Docker:**

- `HEIMVO_AUTH_DISABLED=true` nur für die Demo und die Demo nur auf `127.0.0.1` betreiben (wie in
  `docker-compose.demo.yml`).
- Mit `network_mode: host` lauscht das Dashboard auf allen Schnittstellen. Auf einem Gateway oder
  einem Rechner mit öffentlicher Schnittstelle `HEIMVO_HOST` auf die LAN-Adresse setzen.
- `HEIMVO_API_TOKEN` hat Admin-Rechte: mindestens 24 zufällige Zeichen (`openssl rand -hex 32`),
  nicht in Shell-Verlauf oder Screenshots.
- Hinter einem Reverse Proxy diesen in `HEIMVO_TRUSTED_PROXIES` eintragen und dort zusätzlich ein
  Rate-Limit setzen. Nie Adressen eintragen, die Sie nicht kontrollieren: `X-Forwarded-For` ist
  leicht zu fälschen.
- Der Capture-Container braucht `NET_RAW`/`NET_ADMIN`. Die mitgelieferte `docker-compose.yml`
  entzieht alle anderen Rechte, setzt `no-new-privileges` und ein schreibgeschütztes
  Dateisystem; diese Einstellungen bitte beibehalten.
- Das CrowdSec-Profil lässt die Central API standardmäßig aus
  (`CROWDSEC_DISABLE_ONLINE_API=true`), damit keine Angriffssignale Ihr Netz verlassen.

**Windows-Installer:** Setup vertraut `%ProgramData%\Heimvo` nur, wenn der Ordner
Administratoren/SYSTEM gehört oder einem Administrator gehört und nur SYSTEM/Administratoren
schreiben dürfen. Ein nicht erhöhter Prozess desselben Administrators könnte einen solchen Ordner
vorbereiten; das nehmen wir hin, weil UAC keine Sicherheitsgrenze ist. Dateien von
Standardbenutzern werden nicht übernommen.
