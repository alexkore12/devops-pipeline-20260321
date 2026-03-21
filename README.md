# Jenkins CI/CD Pipeline

Pipeline completo para CI/CD de aplicación backend con análisis de seguridad.

## 🚀 Características

- **Multi-stage** - Build, Test, Security, Deploy
- **Security Scanning** - Trivy, SonarQube
- **Docker** - Build y push de imágenes
- **Kubernetes** - Deploy automático
- **Notifications** - Slack/Discord alerts
- **Environment** - Staging y Production

## ⚠️ Alerta de Seguridad: Trivy Supply Chain

**⚠️ IMPORTANTE (21-Marzo-2026):** Se ha detectado el **SEGUNDO ataque de supply chain** contra Trivy.

| Aspecto | Detalle |
|---------|---------|
| Versiones afectadas | 0.69.4 (maliciosa), 0.69.3-0.69.0 |
| Vector | GitHub Actions comprometidas |
| Acción | Verificar versión: `trivy --version` |

**Recomendación:** Usar versión parcheada o descarga directa de binarios.

## 📦 Requisitos

- Jenkins 2.480+
- Docker 27.0+
- Kubernetes 1.31+
- Trivy (última versión parcheada)
- Plugins: Git, Docker, Kubernetes, Slack

## 📋 Stages del Pipeline

| Stage | Descripción |
|-------|-------------|
| Checkout | Código fuente |
| Build | Compilación |
| Test | Pruebas unitarias |
| Security Scan | Análisis de vulnerabilidades |
| Docker Build | Imagen container |
| Scan Image | Escaneo de imagen Docker |
| Deploy Staging | Deploy a staging |
| Integration Tests | Pruebas de integración |
| Deploy Production | Deploy a producción (solo main) |

## 🔧 Configuración

### Variables de Entorno

```groovy
environment {
    DOCKER_REGISTRY = 'registry.example.com'
    DOCKER_IMAGE = 'myapp'
    K8S_NAMESPACE = 'production'
    TRIVY_VERSION = '0.57.0'  // Usar versión parcheada
}
```

### Secrets Requeridos

- `DOCKER_USERNAME` - Usuario registry
- `DOCKER_PASSWORD` - Password registry
- `KUBECONFIG` - Configuración Kubernetes
- `SLACK_WEBHOOK` - Webhook Slack

## 🐳 Docker Security

```dockerfile
# Usar imagen base minimal
FROM python:3.11-slim

# No correr como root
USER 1000

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s \
    CMD python -c "import requests; requests.get('/health')"
```

## 🔒 Security Scanning

### Trivy

```bash
# Escaneo de filesystem
trivy fs --security-checks vuln,config .

# Escaneo de imagen
trivy image --severity HIGH,CRITICAL myapp:latest

# Actualizar DB
trivy db update
```

### Best Practices

1. **Imágenes minimal** - Usar slim/alpine
2. **No root** - USER 1000 en Dockerfile
3. **Multi-stage** - Reducir tamaño final
4. **Scanning** - Trivy en CI/CD
5. **Secrets** - Nunca hardcodear

## ☸️ Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 3
  template:
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
```

## 📊 Notificaciones

El pipeline envía alertas a Slack:

- ✅ Build exitoso
- ❌ Build fallido
- 🚀 Deploy a producción

## 📝 Licencia

MIT - Alejandro Kore

## 🤖 Actualizado por

OpenClaw AI Assistant - 2026-03-21
*Mejoras: Alerta Trivy supply chain, security scanning docs*
