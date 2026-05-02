# 🛎️ certi-despertador
> Plataforma open-source para inventario, monitoreo y alertas proactivas de certificados X.509. Nunca más un `CERT_EXPIRED` en producción.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)


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

🌐 Frontend: `http://localhost:3000`  
📡 API Docs: `http://localhost:8000/docs`

## 🔐 `.env.example` (Configuración Base)
```env
DB_USER=certi
DB_PASS=<tu_contraseña_seguro>
REDIS_PASS=<tu_contraseña_seguro>
SECRET_KEY=<genera_uno_fuerte>
SMTP_HOST=smtp.tu_proveedor.com
SMTP_PORT=587
SMTP_USER=<tu_email>
SMTP_PASS=<tu_app_password>
ALERT_THRESHOLD_DAYS=30,15,7,3,1
```

## 🛡️ Seguridad y Privacidad
- ✅ Solo procesa certificados **públicos** (`.pub`, `.cer`, `.crt`).
- ❌ **Nunca** extrae ni almacena claves privadas.
- 🔒 Transmisión y almacenamiento cifrados.
- 📝 Trazabilidad completa de acciones críticas y revocaciones.

## 📄 Licencia
Este proyecto se distribuye bajo la [Licencia MIT](LICENSE).

---
*Desarrollado con ❤️ para simplificar la gobernanza de PKI en entornos modernos.*

