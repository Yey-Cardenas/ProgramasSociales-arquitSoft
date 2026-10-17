# C4 — Nivel 3: Componentes de la aplicación web

Vista lógica propuesta de los componentes principales dentro de la aplicación ASP.NET Core 10 MVC.

```mermaid
flowchart TB
    usuario["Dirigente de comité /<br/>Personal municipal autorizado"]

    subgraph app["Aplicación ASP.NET Core 10 MVC"]
        vistas["Vistas Razor<br/>HTML / Bootstrap"]
        controladores["Controladores MVC"]
        identidad["Autenticación y autorización<br/>ASP.NET Core Identity · cookies · roles"]
        servicios["Servicios de aplicación y dominio<br/>Inscripciones · duplicados · revalidación<br/>staging · cobertura"]
        auditoria["Servicio de auditoría<br/>Actor · fecha · operación · resultado · motivo"]
        notificaciones["Servicio de notificaciones<br/>Estados · reintentos limitados · errores"]
        persistencia["Acceso a datos<br/>EF Core 10 · Repository / Unit of Work<br/>cuando corresponda"]
    end

    db[("Azure SQL Database")]
    gateway["Proveedor SMS / WhatsApp"]

    usuario -->|"HTTPS"| vistas
    vistas --> controladores
    controladores --> identidad
    controladores --> servicios
    servicios --> persistencia
    servicios --> auditoria
    servicios --> notificaciones
    persistencia -->|"TLS"| db
    notificaciones -->|"HTTPS"| gateway
```

## Responsabilidades y controles

- Los controladores coordinan las solicitudes; las reglas de negocio deben residir en servicios y no depender de la interfaz.
- La autorización se verifica en el servidor según rol y ámbito del comité.
- La auditoría conserva eventos relevantes como nuevas operaciones; los usuarios ordinarios no pueden editar ni borrar el historial.
- Los fallos de SMS/WhatsApp se registran y gestionan sin revertir una inscripción confirmada.
- Este diseño es una propuesta pendiente de implementación y pruebas.
