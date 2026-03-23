# DevOps Pipeline

Pipeline de DevOps automatizado con Jenkins y Docker.

## 📋 Descripción

Pipeline de CI/CD para despliegues automatizados usando Jenkins y Docker.

## 🚀 Inicio Rápido

```bash
# Configurar pipeline
./setup.sh

# Verificar salud
python3 health_check.py
```

## 📦 Estructura

```
.
├── Jenkinsfile          # Definición del pipeline
├── docker-compose.yml  # Orquestación de servicios
├── setup.sh           # Script de configuración
└── README.md          # Este archivo
```

## 🔧 Configuración

### Variables de Entorno

| Variable | Descripción | Valor por defecto |
|----------|-------------|-------------------|
| `JENKINS_URL` | URL de Jenkins | localhost:8080 |
| `DOCKER_REGISTRY` | Registry Docker | docker.io |

## 📄 Licencia

MIT License - voir `LICENSE` para detalles.
