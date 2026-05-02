# 🛎️ certi-despertador
> Plataforma open-source para inventario, monitoreo y alertas proactivas de certificados X.509. Nunca más un `CERT_EXPIRED` en producción.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-ready-blue)]()

## 📦 ¿Qué es?
`certi-despertador` es un sistema integral de gestión del ciclo de vida de certificados que permite **inventariar, validar y monitorear** certificados X.509 públicos en tiempo real [1]. Su enfoque principal es la visibilidad operativa y la prevención de caducidades, garantizando que nunca se almacenen claves privadas [1].

## ✨ Características Clave
- 🔍 **Parsing automático**: Soporte para `.pem`, `.p7b`, `.zip` y estructuras de directorio.
- 🔗 **Cadenas de confianza**: Reconstrucción automática `Root → Intermedia → Leaf`.
- 📅 **Alertas proactivas**: Notificaciones multietapa (`30/15/7/3/1` días) vía Email, Slack o Webhook.
- 📊 **Reportes técnicos**: Documentación lista para reemisión (SAN, key size, `EKU`, algoritmos, `issuer`).
- 🏷️ **Organización**: Agrupación por proyectos, activos y metadatos de CA.
- 🛡️ **Seguridad**: Política estricta de "zero private key", RBAC y registro de auditoría.

## 🏗️ Stack Tecnológico
Construido sobre una arquitectura moderna y escalable:
- **Frontend**: React 18 + Vite 5
- **Backend**: Node.js 20 + Fastify 4
- **Base de datos**: PostgreSQL 16 + Redis 7
- **Infraestructura**: Docker, HAProxy, PM2 [1]

## 🐳 Instalación Rápida (Docker)
```bash
# 1. Clona y configura
git clone https://github.com/TU_USUARIO/certi-despertador.git
cd certi-despertador
cp .env.example .env
docker compose up -d --build
