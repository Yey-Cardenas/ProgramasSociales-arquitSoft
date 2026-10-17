# Drivers arquitectónicos

## Drivers identificados

| ID | Driver arquitectónico | Origen | ¿Por qué influye en la arquitectura? |
|----|-----------------------|--------|--------------------------------------|
| DA01 | El sistema debe permitir registrar beneficiarios sin conexión y sincronizar después. | AC01 – Disponibilidad; RC02 – PWA | Define el uso de Service Worker, almacenamiento local en el cliente y una estrategia de sincronización asíncrona dentro de la aplicación web ASP.NET Core MVC. |
| DA02 | La validación de duplicados por DNI debe responder en menos de 0.5 segundos. | AC02 – Rendimiento; RF-02 | Condiciona el modelo de datos relacional en Microsoft SQL Server, los índices sobre DNI y la ejecución eficiente de la validación. La regla definitiva de duplicidad debe confirmarse con el modelo de negocio. |
| DA03 | El sistema debe proteger los datos personales y las copias de seguridad. | AC04 – Seguridad; AC09 – Respaldo | Influye en la autenticación y autorización por roles de ASP.NET Core, la protección de las comunicaciones mediante HTTPS, el control de acceso y la gestión segura de las copias de seguridad. Para la aplicación web MVC con Razor se recomienda ASP.NET Core Identity con autenticación basada en cookies y autorización por roles, por ser la opción coherente con una aplicación web renderizada en el servidor. JWT no se utilizará como mecanismo predeterminado; solo se evaluará si posteriormente se define una API que necesiten consumir clientes independientes. Esta recomendación describe el diseño previsto y no afirma que la autenticación ya esté implementada. |
| DA04 | Debe poder incorporar nuevos programas sociales sin rediseñar el sistema. | AC08 – Mantenibilidad | Exige módulos cohesionados y de bajo acoplamiento, con límites claros dentro del monolito modular y dependencias organizadas por capas. |
| DA05 | La solución debe ser de bajo costo y baja complejidad operativa. | RC01 – Monolito Modular; AC07 – Escalabilidad | Favorece un único sistema desplegable organizado en módulos, evitando microservicios innecesarios. La separación entre servidor de aplicación y servidor de base de datos es una decisión de infraestructura que debe confirmarse según el entorno de despliegue. |
| DA06 | El sistema debe notificar mediante un gateway SMS/WhatsApp externo. | RC06 – Gateway; RF-07 | Condiciona la integración con un servicio externo desde la capa de servicios. Se deben definir los estados de envío y el tratamiento de errores; una cola de mensajes puede evaluarse si la implementación y el proveedor lo requieren. |
| DA07 | Los padrones históricos en Excel/papel deben depurarse antes de consolidarse. | RF-11 – Staging | Obliga a disponer de un módulo de staging separado del padrón definitivo, con validación de formato, detección de duplicados, registro de errores y revisión antes de consolidar datos. |
| DA08 | La cobertura debe visualizarse geográficamente por zona. | RF-08, RF-09; RC05 – Microsoft SQL Server | Condiciona el modelado de zonas, distritos y datos geográficos necesarios para agrupar y presentar beneficiarios activos. La persistencia se realizará en Microsoft SQL Server mediante Entity Framework Core 9, de acuerdo con el stack aprobado en el README. La fuente de límites geográficos y población estimada debe definirse con datos municipales u otra fuente validada. No se requiere introducir PostgreSQL ni PostGIS como parte de la arquitectura actual. |

## Evaluación de otros elementos

| Elemento | ¿Puede ser driver? | Justificación |
|----------|--------------------|---------------|
| RNF-03 (Usabilidad: máximo 5 campos) | No, por sí solo | Afecta principalmente el diseño de la interfaz. Se mantiene como atributo de calidad que debe verificarse mediante pruebas de usabilidad. |
| RNF-06 (Auditabilidad) | Sí | Exige registrar usuario, fecha y motivo de cada cambio. Afecta el modelo de datos y atraviesa los módulos que modifican el padrón. |
| RF-10 (Exportar padrón en Excel/PDF) | No, por sí solo | Es una funcionalidad del módulo de Cobertura y Reportes, salvo que se definan restricciones especiales de volumen o formato que influyan en la arquitectura. |
| RC10 (Git y GitHub) | No | Es una restricción del proceso de desarrollo; no determina directamente la estructura ni el funcionamiento de la aplicación. |

## Criterio de consistencia tecnológica

Este documento debe mantenerse alineado con el `README.md` del repositorio. El stack aprobado para esta versión es:

- **Backend y aplicación web:** C# con ASP.NET Core 9 MVC.
- **Presentación:** vistas Razor (`.cshtml`), HTML5, CSS3 y Bootstrap.
- **ORM y persistencia:** Entity Framework Core 9, con enfoque Code-First.
- **Base de datos:** Microsoft SQL Server.
- **Estilo arquitectónico:** monolito modular organizado en capas.
- **PWA:** capacidades progresivas y funcionamiento offline dentro de la aplicación web existente.

PostgreSQL, PostGIS, React, Node.js, Express y Prisma no forman parte del stack aprobado. Solo podrían incorporarse mediante una decisión formal del equipo y la actualización coherente de todos los documentos afectados.
