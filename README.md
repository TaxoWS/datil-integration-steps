# Dátil API Integration Guide

<div align="center">

![Dátil Logo](https://app.taxo.ws/logo-dark.svg)

**Guía técnica para integración con Dátil API - Facturación Electrónica Ecuador**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Documentation](https://img.shields.io/badge/docs-Dátil-blue.svg)](https://datil.dev)
[![API Status](https://img.shields.io/badge/API-Active-green.svg)](https://link.datil.co)

</div>

---

## 🇺🇸 English Version

### Overview

This repository provides a comprehensive technical guide for integrating with the **Dátil API** to issue electronic invoices in Ecuador. The guide includes complete implementation examples in multiple programming languages, error handling strategies, and production-ready code patterns.

### 🚀 Quick Start

1. **Choose your implementation**
   - [Node.js Implementation](issue-invoice-en.md#nodejs-implementation)
   - [Python Implementation](issue-invoice-en.md#python-implementation)
   - [PHP/Laravel Implementation](issue-invoice-en.md#php-implementation)

### 📋 Prerequisites

- Dátil account with API access
- Valid electronic signature certificate (.p12/.pfx)
- API Key from Dátil dashboard
- Certificate password

### 🔧 Technical Requirements

- HTTP outbound access to `https://link.datil.co`
- Proper timezone handling for `fecha_emision` (ISO-8601 format)
- Sequential number control per series (establishment + emission point)
- Environment variable management for credentials

### 📚 Documentation

- **[Complete Integration Guide](issue-invoice-en.md)** - Step-by-step implementation
- **[API Reference](https://datil.dev/#facturas)** - Official Dátil documentation
- **[Error Handling](issue-invoice-en.md#error-handling)** - Common issues and solutions

### 🛠️ Supported Technologies

- **Node.js** - Full implementation with validation
- **Python** - Request-based implementation
- **PHP/Laravel** - Framework-specific integration
- **cURL** - Direct API calls

### 🔒 Security

- Environment variable management
- Certificate-based authentication
- Idempotency controls
- Error logging and monitoring

---

## 🇪🇨 Versión en Español

### Descripción General

Este repositorio proporciona una guía técnica completa para integrar con la **API de Dátil** para emitir facturas electrónicas en Ecuador. La guía incluye ejemplos de implementación completos en múltiples lenguajes de programación, estrategias de manejo de errores y patrones de código listos para producción.

### 🚀 Inicio Rápido

1. **Elige tu implementación**
   - [Implementación Node.js](issue-invoice-es.md#implementación-nodejs)
   - [Implementación Python](issue-invoice-es.md#implementación-python)
   - [Implementación PHP/Laravel](issue-invoice-es.md#implementación-php)

### 📋 Requisitos Previos

- Cuenta en Dátil con acceso a API
- Certificado de firma electrónica válido (.p12/.pfx)
- API Key desde el panel de Dátil
- Contraseña del certificado

### 🔧 Requisitos Técnicos

- Acceso HTTP saliente a `https://link.datil.co`
- Manejo correcto de zona horaria para `fecha_emision` (formato ISO-8601)
- Control de secuencial único por serie (establecimiento + punto de emisión)
- Gestión de credenciales mediante variables de entorno

### 📚 Documentación

- **[Guía Completa de Integración](issue-invoice-es.md)** - Implementación paso a paso
- **[Referencia de API](https://datil.dev/#facturas)** - Documentación oficial de Dátil
- **[Manejo de Errores](issue-invoice-es.md#manejo-de-errores)** - Problemas comunes y soluciones

### 🛠️ Tecnologías Soportadas

- **Node.js** - Implementación completa con validación
- **Python** - Implementación basada en requests
- **PHP/Laravel** - Integración específica del framework
- **cURL** - Llamadas directas a la API

### 🔒 Seguridad

- Gestión de variables de entorno
- Autenticación basada en certificados
- Controles de idempotencia
- Logging y monitoreo de errores

---

## 📁 Project Structure

```
datil-integration/
├── README.md                 # This file
├── issue-invoice-en.md       # English integration guide
├── issue-invoice-es.md       # Spanish integration guide
├── .env.example             # Environment variables template
├── payload.json             # Sample invoice payload
├── examples/
│   ├── nodejs/             # Node.js implementation
│   ├── python/             # Python implementation
│   └── php/                # PHP/Laravel implementation
└── docs/                   # Additional documentation
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

- **Documentation**: [https://datil.dev](https://datil.dev)
- **API Status**: [https://link.datil.co](https://link.datil.co)
- **Issues**: [GitHub Issues](https://github.com/your-username/datil-integration/issues)

## ⭐ Star History

[![Star History Chart](https://api.star-history.com/svg?repos=your-username/datil-integration&type=Date)](https://star-history.com/#your-username/datil-integration&Date)

---

<div align="center">

**Made with ❤️ for the Ecuadorian developer community**

[![Taxo](https://app.taxo.ws/logo-dark.svg)](https://datil.dev)

</div>
