# Workflows

Dieses Repository enthält eine Sammlung von **GitHub Actions Workflows** für automatisierte Builds, Tests, Linting und Deployments.

## 📋 Übersicht

Dieses Projekt automatisiert wichtige Entwicklungs- und Deployment-Prozesse mit folgenden GitHub Actions:

| Workflow | Beschreibung |
|----------|-------------|
| **build-docker-compose-file.yml** | Erstellt Docker Compose Dateien |
| **check-authorization.yml** | Validiert Autorisierungen und Berechtigungen |
| **deploy-changes.yml** | Deployt Änderungen in der Produktionsumgebung |
| **lint-python.yml** | Prüft Python Code auf Stilkonventionen |
| **lint-typescript.yml** | Prüft TypeScript/JavaScript Code auf Stilkonventionen |
| **restart-containers-compose.yml** | Startet Docker Container mit Compose neu |
| **restart-containers-non-compose.yml** | Startet Docker Container ohne Compose neu |
| **test-backend-python.yml** | Führt Backend-Tests (Python) aus |
| **test-frontend.yml** | Führt Frontend-Tests aus |

## 🚀 Workflows

### Backend (Python)
- **Linting**: `lint-python.yml` - Automatische Codequalitätsprüfung
- **Tests**: `test-backend-python.yml` - Unit- und Integrationstests

### Frontend
- **Linting**: `lint-typescript.yml` - TypeScript/JavaScript Qualitätsprüfung
- **Tests**: `test-frontend.yml` - Frontend-Test-Suite

### Infrastruktur & Deployment
- **Docker Build**: `build-docker-compose-file.yml` - Build von Docker Images
- **Autorisierung**: `check-authorization.yml` - Berechtigungsprüfungen
- **Deployment**: `deploy-changes.yml` - Production Deployment
- **Container Management**: 
  - `restart-containers-compose.yml`
  - `restart-containers-non-compose.yml`

## 📁 Projektstruktur

```
.
├── .github/
│   └── workflows/              # GitHub Actions Workflows
│       ├── build-docker-compose-file.yml
│       ├── check-authorization.yml
│       ├── deploy-changes.yml
│       ├── lint-python.yml
│       ├── lint-typescript.yml
│       ├── restart-containers-compose.yml
│       ├── restart-containers-non-compose.yml
│       ├── test-backend-python.yml
│       └── test-frontend.yml
├── .gitignore
└── README.md
```
