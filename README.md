# DevOps Pipeline

Pipeline de CI/CD con Jenkins para aplicaciones Node.js con escaneo de seguridad.

## ⚠️ Alerta de Seguridad - Trivy

**Marzo 2026**: Trivy v0.69.4 fue comprometido via supply chain attack.

Este pipeline:
- ❌ Evita usar Trivy 0.69.4
- ✅ Usa Trivy 0.69.3 (o versión segura)
- ✅ Escanea contenedores
- ✅ Escanea dependencias npm

## Stages

| Stage | Descripción |
|-------|-------------|
| Checkout | Clona el repositorio |
| Build | Instala dependencias y compila |
| Test | Ejecuta tests unitarios |
| Dependency Scan | npm audit para vulnerabilidades |
| Trivy Security Scan | Escanea imágenes de contenedor |
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
TRIVY_VERSION=0.69.3  # NOT 0.69.4!
```

### Secrets (Jenkins)

- `DOCKER_REGISTRYCredentials`: Credenciales del registry
- `KUBECONFIG`: Configuración de Kubernetes

## Seguridad

### Supply Chain Protection

1. **npm audit**: Detecta vulnerabilidades en dependencias
2. **Trivy**: Escanea la imagen construida
3. **Versión verificada**: Evita versiones comprometidas

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

### Error: Trivy 0.69.4 detected

```
ERROR: Trivy 0.69.4 is COMPROMISED!
```

**Solución**: Actualizar `TRIVY_VERSION` a 0.69.3 o superior.

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
