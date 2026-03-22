# DevOps Pipeline

Pipeline de CI/CD con Jenkins para aplicaciones Node.js con escaneo de seguridad.

## ⚠️ Alerta de Seguridad - Trivy

**Marzo 2026**: Trivy fue comprometido por segunda vez en un mes via supply chain attack.

**Versión comprometida**: 0.69.4
**Vector**: GitHub Actions malicioso

**Recomendación**: Usar alternativas como Grype o Checkov.

## 🔐 Alternativas a Trivy

### Grype (Contenedores)

```bash
# Instalación
brew install grype

# Uso
grype nginx:latest --severity Critical,High
```

### Checkov (IaC)

```bash
# Instalación
pip install checkov

# Uso
checkov -d ./terraform
```

## Stages

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

## Configuración

### Variables de Entorno

```bash
DOCKER_REGISTRY=docker.io
APP_NAME=backend-api
GRYPE_VERSION=0.80.0  # Recomendado
CHECKOV_VERSION=3.0.0  # Para IaC
```

### Secrets (Jenkins)

- `DOCKER_REGISTRYCredentials`: Credenciales del registry
- `KUBECONFIG`: Configuración de Kubernetes

## Seguridad

### Supply Chain Protection

1. **npm audit**: Detecta vulnerabilidades en dependencias
2. **Grype**: Escanea la imagen construida (alternativa a Trivy)
3. **Checkov**: Escaneo de infraestructura como código
4. **Versión verificada**: Evita versiones comprometidas

### Alertas

- **CRITICAL**: Falla el build
- **HIGH**: Notificación a Slack

## Ejecución

```bash
# En Jenkins
# 1. Crear pipeline job
# 2. Point to Jenkinsfile
# 3. Run build
```

## Troubleshooting

### Error: Supply Chain Attack Detected

```
WARNING: Trivy 0.69.4 is COMPROMISED!
Use Grype or Checkov instead.
```

**Solución**: Cambiar a Grype o Checkov como escáner de seguridad.

### Error: CRITICAL vulnerabilities

```
CRITICAL vulnerabilities found in dependencies!
```

**Solución**: Actualizar dependencias vulnerables antes de hacer build.

## Slack Notifications

Configurar webhook en Jenkins:

```
✅ Build succeeded - Security scans passed
❌ Build failed - Check security scans
```

## Production Deployment

Solo se despliega a producción cuando:
- Branch es `main`
- Todos los tests pasan
- No hay vulnerabilidades CRITICAL
- Scan de contenedores pasa
