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
| **test-frontend.yml** | Führt Frontend-Tests aus                              |
| **test-e2e-playwright.yml** | Führt End-to-End-Tests mit Playwright aus             |
| **deployment.yml** | Führt Deployment aus                                  |
| **deploy-slash-commands.yml** | Führt Deployment von Slash-Befehlen aus               |
| **trigger-deploy.yml** | Startet den Deployment-Prozess                        |

## 🚀 Workflows

### Backend (Python)
- **Linting**: `lint-python.yml` - Automatische Codequalitätsprüfung
- **Tests**: `test-backend-python.yml` - Unit- und Integrationstests

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

### Android App
- **Tests**: `test-app-android.yml` - Android-Unit- und Integrationstests via Gradle

### Deployment
- **Deployment**: `deployment.yml` - Deployment-Prozess
- **Deploy Slash-Commands**: `deploy-slash-commands.yml` - Deployment von Slash-Befehlen nach Discord
- **Trigger Deploy**: `trigger-deploy.yml` - Startet den Deploy-Prozess

## 📁 Projektstruktur

```
.
├── .github/
│   └── workflows/              # GitHub Actions Workflows
│       ├── lint-python.yml
│       ├── lint-typescript.yml
│       ├── test-app-android.yml
│       ├── test-backend-python.yml
│       ├── test-frontend.yml
│       ├── test-e2e-playwright.yml
│       ├── deployment.yml
│       ├── deploy-slash-commands.yml
│       └── trigger-deploy.yml
├── .gitignore
└── README.md
```
