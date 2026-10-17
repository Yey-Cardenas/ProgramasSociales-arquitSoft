# Restricciones

## Restricciones de la propuesta

| ID | Restricción | Descripción |
|----|-------------|-------------|
| RC01 | Arquitectura | La solución debe construirse como un **Monolito Modular**: un único sistema desplegable, organizado en módulos cohesionados, sin separar la lógica del negocio en microservicios. |
| RC02 | Aplicación web / PWA | La aplicación debe desarrollarse con **ASP.NET Core MVC 10** y vistas Razor (`.cshtml`). Debe ofrecer una interfaz web adaptable y capacidades PWA para apoyar el trabajo en zonas con conectividad limitada. |
| RC03 | Comunicación web e integraciones | La interacción principal entre el navegador y el servidor seguirá el flujo de peticiones HTTP de ASP.NET Core MVC. Cuando una integración lo requiera, el sistema podrá consumir o exponer endpoints HTTP/API, sin imponer una arquitectura de frontend independiente. |
| RC04 | Tecnología de backend | El backend debe desarrollarse en **C# con ASP.NET Core 10**, siguiendo el patrón Modelo-Vista-Controlador (MVC). El acceso a datos se realizará mediante **Entity Framework Core 10**. |
| RC05 | Base de datos | La base de datos objetivo para el despliegue recomendado es **Azure SQL Database**. El acceso se realizará mediante el proveedor Microsoft.EntityFrameworkCore.SqlServer de Entity Framework Core 10 y un enfoque Code-First. Antes de producción se probarán las migraciones contra Azure SQL Database y se comprobará la compatibilidad de consultas, tipos de datos, restricciones, transacciones y funciones requeridas. |
| RC06 | Gateway de mensajería | El sistema debe integrarse con un proveedor externo de SMS/WhatsApp mediante la capa de servicios, sin acoplarlo a las vistas. El proveedor se seleccionará considerando cobertura, costos, protección de datos, requisitos de consentimiento, estados de entrega, límites de envío y política de reintentos. |
| RC07 | Infraestructura y nube | Se recomienda desplegar la aplicación ASP.NET Core 10 MVC en Azure App Service y utilizar Azure SQL Database como servicio administrado separado. En producción se prohíbe el acceso público irrestricto a la base de datos; se prefiere conectividad privada y, si no es viable, un punto de conexión público con reglas de firewall restrictivas y documentadas. El acceso temporal desde IP pública en desarrollo debe limitarse a las IP autorizadas y retirarse al terminar las pruebas. Los secretos no se guardarán en el repositorio. |
| RC08 | Dispositivos | La interfaz debe funcionar en navegadores web modernos y adaptarse a celulares, incluidos dispositivos Android 8 o superior, en la medida permitida por el navegador y las capacidades PWA utilizadas. |
| RC09 | Alcance de integraciones | En la primera versión, la validación del DNI se limita a cruzar los datos registrados en el propio sistema; las integraciones con RENIEC y SISFOH quedan para una segunda fase. |
| RC10 | Control de versiones | El código fuente y la documentación deben gestionarse con Git y mantenerse en un repositorio compartido en GitHub. |

## Restricciones adicionales propuestas por el equipo

| ID | Tipo | Restricción | Descripción |
|----|------|-------------|-------------|
| RC11 | Legal | Protección de datos personales | El sistema maneja datos personales, como DNI y dirección; su tratamiento debe respetar la normativa peruana de protección de datos personales (Ley N.° 29733). |
| RC12 | Organizacional | Presupuesto municipal limitado | La solución debe mantener bajos los costos de infraestructura y operación, evitando una arquitectura distribuida innecesariamente compleja. |
| RC13 | Organizacional | Usuarios con poca experiencia | Los dirigentes de comité tienen poca experiencia con tecnología, por lo que se requiere una interfaz simple, instrucciones claras y capacitación básica. |

## Criterios de compatibilidad con Azure SQL Database

- La selección de Azure SQL Database no equivale a asumir compatibilidad automática con todas las funciones de una instalación autoadministrada de SQL Server.
- Se utilizará el proveedor de EF Core para SQL Server, con versiones compatibles con .NET 10 y EF Core 10.
- Las migraciones deberán revisarse y ejecutarse primero en un entorno de prueba de Azure SQL Database.
- No se incorporarán procedimientos almacenados, funciones específicas, tipos especiales ni dependencias de herramientas hasta confirmar que están soportados y que son necesarios.
- Las credenciales y cadenas de conexión se guardarán en configuración segura, fuera del control de versiones.
- La verificación queda pendiente de la implementación, ya que el repositorio contiene documentación y no código ni migraciones ejecutables.

## Criterio de consistencia tecnológica

Las decisiones tecnológicas de este documento deben mantenerse alineadas con el `README.md`: **C#, ASP.NET Core 10 MVC, vistas Razor, Entity Framework Core 10 y Azure SQL Database**. La opción de despliegue recomendada es Azure App Service para la aplicación y Azure SQL Database para la persistencia. Antes de implementar, se verificarán consultas, tipos de datos, migraciones de EF Core, procedimientos, funciones y herramientas requeridas. Si se identifica una necesidad que Azure SQL Database no cubra, el equipo deberá evaluar y documentar formalmente una alternativa compatible, incluidos sus costos y responsabilidades operativas. React, Node.js, Express, Prisma y PostgreSQL no forman parte del stack elegido.


## Política de acceso seguro a Azure SQL Database

- **Producción:** no permitir acceso público irrestricto a la base de datos. Se prefiere un punto de conexión privado con la conectividad de red correspondiente entre Azure App Service y Azure SQL Database.
- **Alternativa condicionada:** si la conectividad privada no es viable por el plan o el presupuesto, evaluar el punto de conexión público con reglas de firewall restrictivas y documentadas. Solo se permitirán los orígenes de red necesarios; se revisarán las direcciones de salida de App Service cuando cambien la configuración o el plan.
- **Identidad y permisos:** cuando sea compatible con la configuración elegida, preferir la identidad administrada de App Service para autenticarse en Azure SQL Database. Conceder a la aplicación únicamente los permisos que necesita y separar los permisos de ejecución de los permisos administrativos.
- **Desarrollo:** cualquier acceso temporal desde una IP pública deberá autorizarse solo para las IP de desarrollo necesarias, usar credenciales de desarrollo y retirarse al terminar las pruebas. No se utilizarán excepciones de desarrollo como configuración de producción.
- **Secretos y transporte:** no guardar contraseñas ni cadenas de conexión con secretos en GitHub. Configurarlas mediante mecanismos seguros del entorno de ejecución; mantener el cifrado en tránsito y verificar el certificado del servidor.
- **Verificación:** antes del despliegue, revisar las reglas de red, los permisos efectivos y la conexión desde la aplicación. Estas son medidas de diseño pendientes de configuración y prueba, no una afirmación de que la infraestructura ya esté protegida.



## Política de notificaciones y fallos del proveedor

- **Destinatario y consentimiento:** antes de enviar, verificar que exista un número válido y que el contacto corresponda a la persona o representante autorizado. El uso de WhatsApp/SMS y cualquier requisito de consentimiento o plantilla del proveedor deberán validarse con la Municipalidad y el proveedor elegido.
- **Contenido mínimo:** los mensajes deben evitar DNI completo, detalles sensibles y datos innecesarios. Se limitarán a informar que existe una actualización o tarea y a indicar un canal oficial para consultar más detalles.
- **Estados trazables:** registrar el tipo de aviso, inscripción relacionada, canal, fecha/hora, proveedor, identificador de mensaje del proveedor cuando exista y estado: pendiente, enviado al proveedor, entregado, fallido o estado desconocido. “Enviado” no equivale a “entregado”.
- **Fallos y reintentos:** los errores temporales podrán reintentarse con espera progresiva y un límite configurable. Los errores permanentes (por ejemplo, número inválido o destinatario no disponible) no deben reintentarse indefinidamente; se marcarán para revisión o corrección del contacto.
- **Evitar duplicados:** cada aviso tendrá un identificador interno único y se comprobará el estado previo antes de reintentar. Si el proveedor no ofrece idempotencia, se reconocerá que un tiempo de espera puede dejar el resultado incierto y se evitará afirmar que no se envió; el caso se registrará para reconciliación.
- **Desacoplamiento:** la falla del proveedor no debe revertir una inscripción ya confirmada ni impedir el uso principal del sistema. La tarea de notificación quedará registrada para seguimiento. Se puede implementar una cola o patrón Outbox cuando exista la aplicación; no se afirma que ya exista.
- **Supervisión:** personal autorizado podrá consultar fallos y reintentar o corregir contactos según sus permisos. Los registros de notificación deberán respetar las reglas de privacidad y auditoría.

La selección del proveedor, costos, consentimiento, plantillas, límites de reintento y reglas de contacto deben confirmarse antes de la implementación. Este apartado define el comportamiento esperado, no una integración ya desplegada.

