# C4 — Nivel 2: Contenedores

Este nivel presenta las principales piezas de ejecución y almacenamiento propuestas para el sistema.

```mermaid
flowchart TB
    dirigente["Dirigente de comité"]
    personal["Personal municipal autorizado"]
    subgraph sistema["Sistema de Gestión de Programas Sociales"]
        web["Aplicación web<br/>C# · ASP.NET Core 10 MVC · Razor<br/>Monolito modular en capas"]
        pwa["Capacidades PWA y almacenamiento local<br/>Operaciones mínimas pendientes"]
        db[("Azure SQL Database<br/>Personas, programas, comités,<br/>inscripciones y auditoría")]
    end
    gateway["Proveedor SMS / WhatsApp<br/>Servicio externo"]
    padrones[("Padrones históricos<br/>Excel / CSV revisados")]

    dirigente -->|"HTTPS"| web
    personal -->|"HTTPS"| web
    web <-->|"Recursos y sincronización autorizada"| pwa
    web -->|"EF Core 10 · TLS"| db
    web -->|"API HTTPS"| gateway
    padrones -->|"Carga controlada a staging"| web
```

## Decisiones de arquitectura

- La interfaz Razor y la lógica de aplicación forman parte de la misma aplicación ASP.NET Core MVC; no se propone un frontend React separado.
- Azure SQL Database es el almacenamiento central seleccionado.
- El almacenamiento local de la PWA no confirma por sí solo una inscripción: la confirmación depende de la respuesta del servidor.
- Los datos locales deben minimizarse y protegerse; no se almacenará información personal sensible en la caché estática.
- La conectividad privada a la base de datos es preferida y no debe habilitarse acceso público irrestricto.
