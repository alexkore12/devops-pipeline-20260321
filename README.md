# Jenkins CI/CD Pipeline

## Descripción
Pipeline completo para CI/CD de aplicación backend.

## Stages
1. Checkout - Código fuente
2. Build - Compilación
3. Test - Pruebas unitarias
4. Security Scan - Análisis de seguridad
5. Docker Build - Imagen container
6. Deploy to Staging - Deploy staging
7. Integration Tests - Pruebas integración
8. Deploy to Production - Deploy producción (solo main)

## Uso
Crear Jenkinsfile en proyecto y configurar Pipeline en Jenkins.

## Requisitos
- Jenkins con plugins: Git, Docker, Kubernetes, Slack
- Kubernetes cluster
- Docker registry
