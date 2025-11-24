# Servidores Well-Known Components

Esta sección proporciona guías completas para construir servicios usando el estándar [Well-Known Components (WKC)](https://well-known-components.github.io/documentation/). Seguir estos estándares asegura consistencia, probabilidad y mantenibilidad a través de todos los servicios de Decentraland.

## Resumen

Los servicios Well-Known Components se construyen usando una arquitectura modular basada en dos bloques de construcción principales:

* **[Componentes](components.md)** - Unidades reutilizables y probables de caja negra que encapsulan funcionalidad
* **[Controladores](controllers.md)** - Manejadores de solicitudes que gestionan comunicación HTTP y WebSocket

### Tipos de Componentes

Distinguimos entre dos tipos principales de componentes:

* **[Adaptadores](adapters.md)** - Componentes que manejan procesos de I/O e integraciones externas
* **[Componentes Lógicos](logic-components.md)** - Componentes que contienen lógica de negocio

## Principios Clave

Los servicios WKC siguen estos principios fundamentales:

1. **Modularidad** - Cada componente es auto-contenido con interfaces claras
2. **Probabilidad** - Los componentes están diseñados para ser fácilmente probados unitaria e integradamente
3. **Inyección de Dependencias** - Los componentes reciben sus dependencias explícitamente
4. **Intercambiabilidad** - Los componentes pueden ser intercambiados con implementaciones alternativas
5. **Separación de Responsabilidades** - La lógica de negocio está separada de las operaciones de I/O

## Arquitectura

```
Servicio
├── Componentes
│   ├── Adaptadores (operaciones I/O)
│   │   ├── Adaptadores de base de datos
│   │   ├── Clientes de API externa
│   │   └── Adaptadores de almacenamiento
│   └── Lógica (Lógica de negocio)
│       ├── Lógica de dominio
│       └── Orquestación
└── Controladores (Capa de transporte)
    ├── Manejadores HTTP
    └── Manejadores WebSocket
```

## Fuentes de Componentes

Los componentes pueden importarse de:

* [Organización WKC](https://github.com/well-known-components/) - Componentes WKC estándar
* [Repositorio Core Components](https://github.com/decentraland/core-components) - Componentes específicos de Decentraland

## Comenzando

Para construir un servicio compatible con WKC:

1. Revisar la documentación de [Componentes](components.md) para entender la estructura de componentes
2. Aprender sobre [Adaptadores](adapters.md) para manejar integraciones externas
3. Entender [Componentes Lógicos](logic-components.md) para implementar lógica de negocio
4. Estudiar [Controladores](controllers.md) para manejar solicitudes HTTP/WebSocket

## Cumplimiento de Estándares

Las palabras clave "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" y "OPTIONAL" en esta documentación deben interpretarse como se describe en [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).
