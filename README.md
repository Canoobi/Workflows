# Workflows

Dieses Repository enthält eine Sammlung von **GitHub Actions Workflows** für automatisierte Builds, Tests, Linting und Deployments.

## 📋 Übersicht

Dieses Projekt automatisiert wichtige Entwicklungs- und Deployment-Prozesse mit folgenden GitHub Actions:

| Workflow | Beschreibung                                          |
|----------|-------------------------------------------------------|
| **lint-python.yml** | Prüft Python Code auf Stilkonventionen                |
| **lint-typescript.yml** | Prüft TypeScript/JavaScript Code auf Stilkonventionen |
| **test-app-android.yml** | Führt Android-App-Tests (Gradle) aus                  |
| **test-backend-python.yml** | Führt Backend-Tests (Python) aus                      |
| **test-backend-node.yml** | Führt Backend-Tests (Node) aus, optional mit Prisma   |
| **test-frontend.yml** | Führt Frontend-Tests aus                              |
| **test-e2e-playwright.yml** | Führt End-to-End-Tests mit Playwright aus             |
| **deployment.yml** | Führt Deployment aus                                  |
| **deploy-slash-commands.yml** | Führt Deployment von Slash-Befehlen aus               |
| **trigger-deploy.yml** | Startet den Deployment-Prozess                        |

## 🚀 Workflows

### Backend (Python)
- **Linting**: `lint-python.yml` - Automatische Codequalitätsprüfung
- **Tests**: `test-backend-python.yml` - Unit- und Integrationstests

### Backend (Node)
- **Tests**: `test-backend-node.yml` - Unit- und Integrationstests für Node-Backends

#### test-backend-node.yml

Der Workflow checkt das aufrufende Repository aus, installiert die Dependencies über `npm ci`, generiert bei Bedarf den Prisma-Client und führt anschließend die Test-Suite aus. Der npm-Cache leitet sich aus der `package-lock.json` des Backend-Verzeichnisses ab.

| Input | Pflicht | Standard | Zweck |
|-------|---------|----------|-------|
| `node_version` | nein | `'22'` | Node-Version für die Testausführung |
| `backend_directory` | nein | `.` | Arbeitsverzeichnis des Backends |
| `run_prisma_generate` | nein | `false` | Führt vor den Tests `npx prisma generate` aus |
| `test_command` | nein | `npm test` | Befehl für die Testausführung |

Voraussetzung im aufrufenden Repository: eine `package-lock.json` im `backend_directory` und ein Test-Script, das dem `test_command` entspricht. Wird `run_prisma_generate` aktiviert, muss Prisma als Dependency vorhanden sein.

### Frontend
- **Linting**: `lint-typescript.yml` - TypeScript/JavaScript Qualitätsprüfung
- **Tests**: `test-frontend.yml` - Frontend-Test-Suite
- **End-to-End-Tests**: `test-e2e-playwright.yml` - Baut die Anwendung und führt Playwright gegen den Build aus

#### test-e2e-playwright.yml

Der Workflow installiert die Dependencies, holt die Browser-Binaries, baut die Anwendung und startet anschließend die Playwright-Suite. Der HTML-Report wird als Artefakt hochgeladen.

| Input | Pflicht | Standard | Zweck |
|-------|---------|----------|-------|
| `node_version` | nein | `'22'` | Node-Version für Build und Tests |
| `frontend_directory` | nein | `Frontend` | Arbeitsverzeichnis der Anwendung |
| `build_command` | nein | `npm run build-prod` | Befehl für den Produktionsbuild |
| `test_command` | nein | `npx playwright test` | Befehl für die Testausführung |
| `browsers` | nein | `chromium` | An `playwright install` übergebene Browser |
| `report_path` | nein | `playwright-report` | Pfad des HTML-Reports, relativ zu `frontend_directory` |
| `report_retention_days` | nein | `7` | Aufbewahrungsdauer des Report-Artefakts |

Die Browser-Binaries werden unter `~/.cache/ms-playwright` gecacht; der Cache-Key leitet sich aus der `package-lock.json` des Frontend-Verzeichnisses ab. Bei einem Cache-Treffer werden die Binaries nicht erneut geladen, die System-Bibliotheken aber über `playwright install-deps` nachgezogen, da sie außerhalb des gecachten Verzeichnisses liegen.

Voraussetzung im aufrufenden Repository: `@playwright/test` als Dependency, eine Playwright-Konfiguration und ein Build-Script, das der `build_command` entspricht.

### Infrastruktur
- **nginx-Konfiguration**: `validate-nginx-config.yml` - Prüft eine `nginx.conf` mit `nginx -t`

#### validate-nginx-config.yml

Der Workflow checkt das aufrufende Repository aus und lässt `nginx -t` in einem Wegwerf-Container gegen die angegebene Konfiguration laufen.

| Input | Pflicht | Standard | Zweck |
|-------|---------|----------|-------|
| `config_path` | nein | `config/nginx.conf` | Pfad der zu prüfenden Datei, relativ zur Repository-Wurzel |
| `nginx_image` | nein | `'nginx:latest'` | Image, gegen das geprüft wird |

Geprüft wird bewusst gegen **dasselbe Image**, das der Container später verwendet. Eine Prüfung gegen ein auf dem Runner installiertes nginx sagte über die dort geltenden Direktiven und die mitgelieferte `mime.types` nichts aus.

Sinnvoll ist der Workflow überall dort, wo ein Deployment `docker-compose down` ausführt, bevor es neu startet: Eine fehlerhafte `nginx.conf` lässt den Container nicht hochkommen, und der Dienst bliebe nach dem `down` unten. Der aufrufende Workflow sollte den Deploy-Job daher über `needs` an diesen Job hängen.

### Android App
- **Tests**: `test-app-android.yml` - Android-Unit- und Integrationstests via Gradle

### Deployment
- **Deployment**: `deployment.yml` - Deployment-Prozess
- **Deploy Slash-Commands**: `deploy-slash-commands.yml` - Deployment von Slash-Befehlen nach Discord
- **Trigger Deploy**: `trigger-deploy.yml` - Startet den Deploy-Prozess

#### trigger-deploy.yml

Der Workflow ist der Einstiegspunkt für aufrufende Repositories. Er stößt `deployment.yml` über die GitHub-API an und wartet auf dessen Ergebnis.

| Input | Pflicht | Standard | Zweck |
|-------|---------|----------|-------|
| `container_names` | nein | `""` | Container, die ohne Compose neu gestartet werden |
| `run_pull_changes` | nein | `true` | Führt `git pull` im Repository-Verzeichnis aus |
| `run_stop_compose_containers` | nein | `false` | Führt `docker-compose down --remove-orphans` aus |
| `run_build_docker_compose_file` | nein | `false` | Führt `docker-compose build` aus |
| `run_start_compose_containers` | nein | `false` | Führt `docker-compose up -d` aus |
| `run_restart_non_compose_containers` | nein | `false` | Startet die Container aus `container_names` neu |
| `health_url` | nein | `""` | URL, die nach dem Deploy gesund antworten muss. Leer bedeutet: keine Prüfung |
| `health_timeout_seconds` | nein | `180` | Wie lange auf eine gesunde Antwort gewartet wird |
| `health_expected_status` | nein | `200` | Erwarteter HTTP-Statuscode |

##### Verwaiste Container

`docker-compose down` läuft seit dem 09.08.2026 mit `--remove-orphans`. Ohne das Flag überlebt ein Container, dessen Dienst aus der `docker-compose.yml` entfernt wurde, jedes `down` und `up -d`: Compose kennt ihn nicht mehr und fasst ihn deshalb nicht an. Er läuft mit dem alten Image weiter, belegt seinen Port und sieht für jede Überwachung wie ein gesunder Dienst aus. Genau so überlebte `InfraMonitor-SnmpExporter` am 09.08.2026 seinen eigenen Ausbau und musste von Hand entfernt werden.

Der Wirkungsbereich ist eng: Betroffen sind ausschließlich Container desselben Compose-Projekts, also solche, die früher einmal in derselben `docker-compose.yml` standen. Container aus anderen Projekten und von Hand gestartete fasst Compose nicht an.

Ein eigener Schalter dafür ist nicht möglich: `workflow_dispatch` erlaubt höchstens zehn Eingaben, und `deployment.yml` hat sie bereits vergeben — deshalb steckt auch die Deploy-Verifikation als drei Werte in einer einzelnen `health_check`-Eingabe.

##### Deploy-Verifikation

Ist `health_url` gesetzt, pollt der Workflow nach dem Start der Container den angegebenen Endpunkt und schlägt fehl, wenn innerhalb von `health_timeout_seconds` kein `health_expected_status` zurückkommt.

Der Schritt ist vollständig optional. Ohne `health_url` wird der zugehörige Job übersprungen, und alle bestehenden aufrufenden Workflows laufen unverändert weiter. Repositories ohne HTTP-Schnittstelle, etwa reine Discord-Bots, bleiben deshalb ohne Angabe.

Beispiel für einen aufrufenden Workflow:

```yaml
    uses: Canoobi/Workflows/.github/workflows/trigger-deploy.yml@main
    with:
      run_stop_compose_containers: true
      run_build_docker_compose_file: true
      run_start_compose_containers: true
      health_url: https://YOUR_SERVER_URL_HERE/health
      health_timeout_seconds: 180
      health_expected_status: 200
    secrets:
      WORKFLOWS_REPO_TOKEN: ${{ secrets.WORKFLOWS_REPO_TOKEN }}
      REPO_PATH: ${{ secrets.REPO_PATH }}
```

Eigenschaften der Prüfung:

- Sie läuft per SSH **auf dem Zielserver**, nicht auf dem GitHub-Runner. Interne Adressen sind vom Runner aus nicht erreichbar.
- Der Aufruf erfolgt mit `curl -k`, da die internen Dienste selbstsignierte Zertifikate verwenden, und mit `-L`, weil ein Health-Endpunkt hinter einem Reverse-Proxy umleiten kann (z. B. http→https oder ein Trailing-Slash), ohne dass das selbst ein Fehlerzustand ist. Ein Endpunkt, der auf eine externe, nicht erreichbare Adresse umleitet, liefe dadurch weiterhin in den Timeout statt in einen sofortigen Fehler — das ist der abgewogene Nachteil dieser Wahl.
- Vor dem ersten Versuch wartet der Schritt 5 Sekunden. `docker-compose up -d` kehrt zurück, sobald die Container angelegt sind, nicht sobald sie tatsächlich Verbindungen annehmen; ohne diese Anlaufpause könnte ein bereits antwortender alter Container oder ein davorliegender Reverse-Proxy die Prüfung auf dem allerersten Versuch fälschlich als gesund erscheinen lassen.
- Erfolgreich ist die Prüfung erst nach **zwei aufeinanderfolgenden** erfolgreichen Antworten, nicht bereits nach einer. Eine einzelne passende Antwort kann noch der alte Container oder eine zwischengespeicherte Proxy-Antwort sein; jede fehlgeschlagene Zwischenantwort setzt den Zähler zurück. Ein `health_timeout_seconds` von 0 kann dadurch nicht mehr erfolgreich sein, da für zwei Versuche im Abstand von 5 Sekunden keine Zeit bleibt — es wird weiterhin mindestens einmal geprüft, dieser eine Versuch reicht aber nicht mehr für ein positives Ergebnis.
- Umgekehrt gilt: Kommt der erste Erfolg erst kurz vor Ablauf des Zeitfensters und schlägt ausgerechnet die Bestätigungsantwort einmalig fehl, gilt die Prüfung als gescheitert, obwohl der Dienst gesund ist. Dieses Fenster ist schmal — die Schleife räumt dem Bestätigungsversuch noch rund fünf Sekunden über `health_timeout_seconds` hinaus ein —, aber es existiert. Wer einen Dienst mit stark schwankender Anlaufzeit prüft, sollte `health_timeout_seconds` großzügig wählen, statt knapp zu kalkulieren.
- Zwischen zwei Versuchen liegen 5 Sekunden.
- Antwortet der Endpunkt nicht, meldet die Ausgabe `last status 000`; bei falschem Statuscode den tatsächlich empfangenen Code.
- Der Job hängt über `needs` an allen vorherigen Deploy-Jobs und wird nur ausgeführt, wenn diese erfolgreich waren oder abgeschaltet sind. **Das sagt nichts darüber aus, ob die Prüfung tatsächlich etwas kontrolliert, das der Deploy verändert hat:** Ein Aufrufer, der ausschließlich `health_url` setzt, lässt `run_pull_changes` auf seinem Standardwert `true` und alle übrigen `run_*`-Schalter auf `false` -- der Job `deploy-changes` (nur `git pull`) läuft dann zwar, aber weder Compose-Container werden neu gebaut oder gestartet noch Nicht-Compose-Container neu gestartet. Die Prüfung testet in diesem Fall eine Instanz, die der Deploy nie angefasst hat, und ein grüner Workflow belegt dann nur, dass der Endpunkt zufällig gesund war, nicht dass der Deploy funktioniert hat. Aussagekräftig ist die Prüfung nur in Kombination mit mindestens einem der Schalter, die tatsächlich etwas neu starten (`run_stop_compose_containers` + `run_start_compose_containers`, oder `run_restart_non_compose_containers`).
- Das Zeitbudget in `trigger-deploy.yml`, das auf das Ergebnis des gesamten `deployment.yml`-Laufs wartet, wächst mit `health_timeout_seconds` mit (siehe Kommentar in `trigger-deploy.yml`): Basis 600s für die ursprünglichen fünf Jobs, plus 30s Anlaufmarge für den zusätzlichen Verifikations-Job, plus `health_timeout_seconds`. Ohne `health_url` bleibt es bei den ursprünglichen 600s.
- Die SSH-Sitzung selbst (`appleboy/ssh-action`, Standard-Timeout 10 Minuten) bekommt einen von `health_timeout_seconds` abgeleiteten `command_timeout` (Wert plus 60s Marge für Verbindungsaufbau und den letzten Prüfversuch), damit ein länger als 10 Minuten konfigurierter `health_timeout_seconds` nicht durch die SSH-Aktion selbst abgeschnitten wird, bevor die Prüfschleife fertig ist.

##### Warum die drei Eingaben in `deployment.yml` als ein Wert ankommen

`deployment.yml` wird über `workflow_dispatch` angestoßen. GitHub erlaubt dort **höchstens 10 Top-Level-Inputs**; neun waren bereits belegt. Die drei Eingaben oben werden deshalb von `trigger-deploy.yml` zu einem einzigen Input `health_check` im Format

```text
URL|timeout_seconds|expected_status
```

zusammengesetzt und im Zielworkflow wieder zerlegt. Ist `health_url` leer, wird ein leerer Wert übergeben und der Job übersprungen.

Damit ist das Input-Kontingent von `deployment.yml` ausgeschöpft. Weitere Eingaben müssen in einen bestehenden Wert eingebettet werden, sonst wird die Workflow-Datei ungültig und **alle** Deployments schlagen fehl.

Der Wert wird als Umgebungsvariable (`env` zusammen mit `envs:`) an das SSH-Skript übergeben und nicht per `${{ }}` in den Skripttext eingesetzt. Ein direkt eingesetzter Ausdruck würde von der Shell des Zielservers ausgeführt; ein Wert mit `$(…)` oder Backticks liefe dort als Deploy-Benutzer.

## 📁 Projektstruktur

```
.
├── .github/
│   └── workflows/              # GitHub Actions Workflows
│       ├── lint-python.yml
│       ├── lint-typescript.yml
│       ├── test-app-android.yml
│       ├── test-backend-python.yml
│       ├── test-backend-node.yml
│       ├── test-frontend.yml
│       ├── test-e2e-playwright.yml
│       ├── validate-nginx-config.yml
│       ├── deployment.yml
│       ├── deploy-slash-commands.yml
│       └── trigger-deploy.yml
├── .gitignore
└── README.md
```
