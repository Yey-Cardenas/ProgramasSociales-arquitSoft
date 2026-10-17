# Drivers arquitectónicos

Los drivers se derivan de los requisitos funcionales, restricciones y atributos de calidad. Los criterios verificables asociados a RNF-01–RNF-09 se encuentran en [07-requisitos-no-funcionales.md](07-requisitos-no-funcionales.md), y su relación con los escenarios AC01–AC09 se documenta en [04-atributos-de-calidad.md](04-atributos-de-calidad.md).

## Drivers identificados

| ID | Driver arquitectónico | Origen | ¿Por qué influye en la arquitectura? |
|----|-----------------------|--------|--------------------------------------|
| DA01 | El sistema debe permitir registrar beneficiarios sin conexión y sincronizar después. | AC01 – Disponibilidad; RC02 – PWA | Define el uso de capacidades PWA dentro de ASP.NET Core MVC, almacenamiento local de registros pendientes (por ejemplo, IndexedDB) y sincronización asíncrona. Cada registro tendrá un identificador único de operación para evitar que los reintentos creen altas repetidas. El servidor será la autoridad para validar reglas de negocio y duplicados de DNI; si existe un conflicto, el registro quedará pendiente de revisión sin sobrescribir datos automáticamente. Los fallos temporales se reintentarán con espera progresiva y se mostrará al usuario el estado de la sincronización. |
| DA02 | La detección de inscripciones duplicadas por DNI debe responder en menos de 0.5 segundos. | AC02 – Rendimiento; RF-02 | Condiciona el modelo de datos relacional en Azure SQL Database, los índices sobre DNI y la ejecución eficiente de la validación. El DNI identifica a la persona y las inscripciones se gestionan por programa y comité: se bloquea una inscripción activa repetida en el mismo programa y comité; una inscripción activa en otro comité del mismo programa genera una alerta para revisión de la Sub Gerencia; una inscripción en otro programa no se considera duplicada automáticamente y debe evaluarse según sus reglas de elegibilidad. |
| DA03 | El sistema debe proteger los datos personales y las copias de seguridad. | AC04 – Seguridad; AC09 – Respaldo | Influye en la autenticación y autorización por roles de ASP.NET Core, la protección de las comunicaciones mediante HTTPS, el control de acceso y la gestión segura de las copias de seguridad. Para la aplicación web MVC con Razor se recomienda ASP.NET Core Identity con autenticación basada en cookies y autorización por roles, por ser la opción coherente con una aplicación web renderizada en el servidor. JWT no se utilizará como mecanismo predeterminado; solo se evaluará si posteriormente se define una API que necesiten consumir clientes independientes. Esta recomendación describe el diseño previsto y no afirma que la autenticación ya esté implementada. |
| DA04 | Debe poder incorporar nuevos programas sociales sin rediseñar el sistema. | AC08 – Mantenibilidad | Exige módulos cohesionados y de bajo acoplamiento, con límites claros dentro del monolito modular y dependencias organizadas por capas. |
| DA05 | La solución debe ser de bajo costo y baja complejidad operativa. | RC01 – Monolito Modular; RC07 – Infraestructura y nube; AC07 – Escalabilidad | Se mantiene un único sistema desplegable (monolito modular) y se define la separación de la aplicación y la base de datos como servicios administrados: Azure App Service para ASP.NET Core MVC y Azure SQL Database para persistencia. La separación no implica microservicios. Se debe restringir el acceso a la base de datos y estimar los costos de planes, conectividad privada, almacenamiento y respaldos antes de contratar el servicio. |
| DA06 | El sistema debe notificar mediante un gateway SMS/WhatsApp externo. | RC06 – Gateway; RF-07 | Condiciona la integración con un servicio externo desde la capa de servicios. Se deben definir los estados de envío y el tratamiento de errores; una cola de mensajes puede evaluarse si la implementación y el proveedor lo requieren. |
| DA07 | Los padrones históricos en Excel/papel deben depurarse antes de consolidarse. | RF-11 – Staging | Obliga a disponer de un módulo de staging separado del padrón definitivo, con validación de formato, detección de duplicados, registro de errores y revisión antes de consolidar datos. |
| DA08 | La cobertura debe visualizarse geográficamente por zona. | RF-08, RF-09; RC05 – Azure SQL Database | Condiciona el modelado de zonas, distritos y datos geográficos necesarios para agrupar y presentar beneficiarios activos. La persistencia se realizará en Azure SQL Database mediante Entity Framework Core 10, de acuerdo con el stack aprobado en el README. La fuente de límites geográficos y población estimada debe definirse con datos municipales u otra fuente validada. No se requiere introducir PostgreSQL ni PostGIS como parte de la arquitectura actual. |

## Seguridad de red y acceso a la base de datos

El diseño de despliegue debe impedir el acceso público irrestricto a Azure SQL Database. La opción preferida es la conectividad privada desde Azure App Service. Si no es viable, se documentarán reglas de firewall limitadas a los orígenes necesarios y se revisarán las direcciones de salida de App Service. Cuando sea compatible, se utilizará identidad administrada y permisos mínimos; los secretos permanecerán fuera del repositorio. Cualquier regla temporal de desarrollo se limitará a las IP autorizadas y se retirará después de las pruebas. La configuración y los permisos deben verificarse antes de producción.

## Compatibilidad de persistencia

La decisión de usar Azure SQL Database se implementará mediante Entity Framework Core 10 y su proveedor para SQL Server (Microsoft.EntityFrameworkCore.SqlServer). La compatibilidad deberá comprobarse en una base de datos de prueba de Azure SQL Database mediante pruebas de migraciones, consultas LINQ, índices, restricciones, transacciones y cualquier función específica que se requiera. No se considera verificada hasta que existan código, migraciones y resultados de pruebas. Las decisiones de persistencia no autorizan cambiar el motor seleccionado sin documentar y aprobar la alternativa.

## Evaluación de otros elementos

| Elemento | ¿Puede ser driver? | Justificación |
|----------|--------------------|---------------|
| RNF-03 (Usabilidad: máximo 5 campos) | No, por sí solo | Afecta principalmente el diseño de la interfaz. Se mantiene como atributo de calidad que debe verificarse mediante pruebas de usabilidad. |
| RNF-06 (Auditabilidad) | Sí | Exige registrar usuario, fecha y motivo de cada cambio. Afecta el modelo de datos y atraviesa los módulos que modifican el padrón. |
| RF-10 (Exportar padrón en Excel/PDF) | No, por sí solo | Es una funcionalidad del módulo de Cobertura y Reportes, salvo que se definan restricciones especiales de volumen o formato que influyan en la arquitectura. |
| RC10 (Git y GitHub) | No | Es una restricción del proceso de desarrollo; no determina directamente la estructura ni el funcionamiento de la aplicación. |

## Criterio de consistencia tecnológica

Este documento debe mantenerse alineado con el `README.md` del repositorio. El stack aprobado para esta versión es:

- **Backend y aplicación web:** C# con ASP.NET Core 10 MVC.
- **Presentación:** vistas Razor (`.cshtml`), HTML5, CSS3 y Bootstrap.
- **ORM y persistencia:** Entity Framework Core 10, con enfoque Code-First.
- **Base de datos:** Azure SQL Database.
- **Estilo arquitectónico:** monolito modular organizado en capas.
- **PWA:** capacidades progresivas y funcionamiento offline dentro de la aplicación web existente.

PostgreSQL, PostGIS, React, Node.js, Express y Prisma no forman parte del stack elegido. Azure SQL Database es el servicio de base de datos seleccionado para el despliegue recomendado. Antes de implementarlo se comprobará que las consultas, tipos de datos y migraciones de EF Core necesarias sean compatibles. Si se detecta una limitación, cualquier alternativa deberá aprobarse y documentarse formalmente antes de cambiar la decisión.
