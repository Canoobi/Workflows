# Workflows

Dieses Repository enthält eine Sammlung von **GitHub Actions Workflows** für automatisierte Builds, Tests, Linting und Deployments.

## 📋 Übersicht

Dieses Projekt automatisiert wichtige Entwicklungs- und Deployment-Prozesse mit folgenden GitHub Actions:

| Workflow | Beschreibung                                          |
|----------|-------------------------------------------------------|
| **lint-python.yml** | Prüft Python Code auf Stilkonventionen                |
| **lint-typescript.yml** | Prüft TypeScript/JavaScript Code auf Stilkonventionen |
| **test-backend-python.yml** | Führt Backend-Tests (Python) aus                      |
| **test-frontend.yml** | Führt Frontend-Tests aus                              |
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
│       ├── test-backend-python.yml
│       ├── test-frontend.yml
│       ├── deployment.yml
│       ├── deploy-slash-commands.yml
│       └── trigger-deploy.yml
├── .gitignore
└── README.md
```
