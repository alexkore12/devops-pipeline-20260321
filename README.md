# DevOps Pipeline

Pipeline de CI/CD con Jenkins para aplicaciones Node.js con escaneo de seguridad - Versión 2.0

> ⚠️ **Marzo 2026**: Trivy fue comprometido por segunda vez en un mes via supply chain attack.
> 
> **Versión comprometida**: 0.69.4  
> **Vector**: GitHub Actions malicioso  
> 
> ✅ **Solución**: Usar alternativas como Grype o Checkov.

## Alternativas Seguras a Trivy

### Grype

```bash
# Instalación
brew install grype

# Uso
grype nginx:latest --severity Critical,High
```

### Checkov

```bash
# Instalación
pip install checkov

# Uso
checkov -d ./terraform
```

## Stages del Pipeline

| Stage | Descripción |
|-------|-------------|
| Checkout | Clona el repositorio |
| Build | Instala dependencias y compila |
| Test | Ejecuta tests unitarios |
| Dependency Scan | npm audit para vulnerabilidades |
| Security Scan | Escaneo con Grype (alternativa a Trivy) |
| Container Scan | Escaneo profundo de vulnerabilidades |
| Docker Build | Construye imagen Docker |
| Deploy to Staging | Despliega a staging |
| Integration Tests | Tests de integración |
| Deploy to Production | Despliega a producción (solo main) |

## 🤖 GitHub Actions

El proyecto incluye workflows de CI/CD:

### Workflows Incluidos

| Workflow | Descripción | Frecuencia |
|----------|-------------|------------|
| `ci.yml` | Pipeline completo CI/CD | Push + Manual |
| | Linting con shellcheck | |
| | Escaneo de secretos | |
| | Security scan con Grype | |
| | Validación de Jenkinsfile | |
| | Build de Docker | |
| | Deploy (staging/production) | |

### Uso de GitHub Actions

```bash
# Trigger manual deployment
gh workflow run ci.yml -f environment=staging
gh workflow run ci.yml -f environment=production
```

### Configuración de Secrets

- `DOCKER_USERNAME`: Usuario de Docker Hub
- `DOCKER_PASSWORD`: Token de Docker Hub
- `KUBECONFIG`: Configuración de Kubernetes

## Configuración

### Variables de Entorno

```bash
DOCKER_REGISTRY=docker.io
APP_NAME=backend-api
GRYPE_VERSION=0.80.0  # Recomendado
CHECKOV_VERSION=3.0.0 # Para IaC
```

### Credentials Requeridas

- `DOCKER_REGISTRY` - Credenciales del registry
- `KUBECONFIG` - Configuración de Kubernetes

## Escaneo de Seguridad

### npm audit

Detecta vulnerabilidades en dependencias npm.

### Grype

## Estructura

```
devops-pipeline-20260321/
├── Jenkinsfile           # Pipeline de Jenkins
├── README.md             # Este archivo
├── DEPLOYMENT.md         # Guía de deployment
├── SECURITY.md           # Políticas de seguridad
└── .github/
    └── workflows/
        └── ci.yml        # GitHub Actions CI/CD
```

## Ejecución

```bash
grype myapp:latest --severity Critical,High
```

### Checkov

Escaneo de infraestructura como código.

```bash
checkov -d ./terraform
checkov -f Dockerfile
```

## Políticas de Seguridad

### Versión verificada

Evita versiones comprometidas.

### Umbral de severidad

- **CRITICAL**: Falla el build
- **HIGH**: Notificación a Slack

## Configuración de Notificaciones

### Slack

Configurar webhook en Jenkins:

✅ Build succeeded - Security scans passed  
❌ Build failed - Check security scans

## Reglas de Despliegue

Solo se despliega a producción cuando:

- Branch es `main`
- Todos los tests pasan
- No hay vulnerabilidades CRITICAL
- Scan de contenedores pasa

---

*Actualizado: 2026-03-22 - Agregado GitHub Actions CI/CD con Grype*
