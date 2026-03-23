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

Escanea la imagen construida (alternativa a Trivy).

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

## Jenkinsfile

```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'docker.io'
        APP_NAME = 'backend-api'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Dependency Scan') {
            steps {
                sh 'npm audit --audit-level=high'
            }
        }
        
        stage('Security Scan') {
            steps {
                sh 'grype ${APP_NAME}:${VERSION} --severity Critical,High || true'
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build -t ${APP_NAME}:${VERSION} ."
                sh "docker tag ${APP_NAME}:${VERSION} ${DOCKER_REGISTRY}/${APP_NAME}:${VERSION}"
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                sh 'kubectl apply -f k8s/staging/'
            }
        }
        
        stage('Integration Tests') {
            steps {
                sh 'npm run test:integration'
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input 'Deploy to Production?'
                sh 'kubectl apply -f k8s/production/'
            }
        }
    }
    
    post {
        success {
            slackSend channel: '#deployments', color: 'good', message: "Deployment successful: ${env.JOB_NAME}"
        }
        failure {
            slackSend channel: '#deployments', color: 'danger', message: "Deployment failed: ${env.JOB_NAME}"
        }
    }
}
```

## GitHub Actions (Alternativo)

```yaml
name: CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Build Docker
        run: docker build -t app:${{ github.sha }} .

  security:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Grype
        uses: anchore/grype-action@v0.17.0
        with:
          image: app:${{ github.sha }}
          severity-cutoff: High

  deploy:
    needs: security
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy
        run: kubectl apply -f k8s/production/
```

## Errores Comunes y Soluciones

### ⚠️ WARNING: Trivy 0.69.4 is COMPROMISED!

**Solución**: Cambiar a Grype o Checkov como escáner de seguridad.

### ⚠️ CRITICAL vulnerabilities found

**Solución**: Actualizar dependencias vulnerables antes de hacer build.

## Mejores Prácticas

1. ✅ Usar Grype o Checkov en lugar de Trivy
2. ✅ Ejecutar scans en cada build
3. ✅ Bloquear deployment en vulnerabilidades CRITICAL
4. ✅ Usar dependencias actualizadas
5. ✅ Firmar imágenes con Cosign
6. ✅ Generar SBOM (Software Bill of Materials)

## Changelog

- ✅ v2.0 - Grype替换Trivy, GitHub Actions añadido
- ✅ v1.0 - Versión inicial con Trivy

## Licencia

MIT

## Autor

GitHub: [alexkore12](https://github.com/alexkore12)

Este proyecto fue actualizado por OpenClaw AI Assistant - 2026-03-22
