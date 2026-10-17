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
| RC08 | Dispositivos | La interfaz debe adaptarse a celulares y escritorio. Android 8 o superior es un objetivo sujeto a que el navegador instalado siga recibiendo actualizaciones de seguridad y soporte HTTPS, Service Workers, IndexedDB y las APIs PWA requeridas. Los dispositivos o navegadores que no cumplan esas condiciones no se declararán compatibles. La matriz inicial de prueba se documenta en los requisitos no funcionales. |
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



## Restricciones de importación de padrones históricos

Los padrones históricos se importarán primero a un área temporal de staging; no se cargarán directamente al padrón definitivo. Solo personal autorizado podrá aprobar la consolidación. Los archivos y reportes que contengan datos personales deberán tener acceso restringido y una retención definida por la Municipalidad. Los formatos aceptados, tamaño y número máximo de filas/columnas, responsables y plazos de conservación quedan pendientes de validación. La importación debe tratar los archivos como entrada no confiable y neutralizar valores peligrosos al producir reportes CSV.


## Restricciones de exportación de padrones

Los archivos exportados contienen potencialmente datos personales y estarán sujetos a permisos por rol y ámbito. El dirigente se limitará a su comité asignado; el personal municipal operará dentro de su ámbito autorizado. El padrón completo y el DNI completo no estarán disponibles por defecto en reportes generales. La Municipalidad debe definir campos permitidos, responsables de excepciones, canales de intercambio y retención de copias.


## Restricciones de auditoría

Los eventos de auditoría no podrán ser modificados ni eliminados por usuarios ordinarios. El acceso a la consulta del historial y a la administración de la plataforma deberá limitarse por permisos. La Municipalidad debe definir el plazo de conservación y los responsables de supervisión. La aplicación no debe registrar contraseñas, tokens ni datos personales innecesarios en los logs.


## Matriz inicial de compatibilidad de dispositivos y navegadores

Esta matriz es una propuesta para pruebas de aceptación, no una afirmación de que ya se haya comprobado la compatibilidad. Las versiones exactas deberán registrarse en cada ejecución porque los navegadores y su soporte cambian.

| Plataforma | Navegador objetivo | Cobertura de prueba |
|---|---|---|
| Android 8 o superior | Google Chrome para Android, en una versión que todavía reciba actualizaciones de seguridad y soporte las APIs requeridas | Interfaz adaptable, inicio de sesión, permisos por rol, registro y validación de formularios, instalación/uso como PWA cuando esté disponible, almacenamiento local, operación offline, reconexión, reintentos, conflictos y cierre de sesión con operaciones pendientes |
| Android 8 o superior (alternativa) | Microsoft Edge para Android, si está disponible y actualizado en el dispositivo de prueba | Repetir las pruebas principales y offline antes de declararlo soportado |
| Escritorio Windows | Google Chrome y Microsoft Edge, versiones estables con soporte vigente en la fecha de prueba | Flujos principales, accesibilidad básica, exportaciones autorizadas, bloqueo por inactividad y sincronización |
| Otros navegadores o iOS | Fuera del alcance inicial | No declarar compatibilidad hasta añadirlos a la matriz y ejecutar las pruebas correspondientes |

**Regla de compatibilidad:** el requisito Android 8+ no significa que cualquier dispositivo antiguo sea compatible. El dispositivo debe tener un navegador actualizado y compatible con HTTPS, Service Workers, IndexedDB y las APIs PWA usadas. Si el navegador ya no recibe actualizaciones de seguridad o no soporta una API necesaria, se debe marcar como no compatible y documentar la limitación; no se debe debilitar la seguridad para admitirlo.

**Escenarios mínimos de prueba offline:**
1. Abrir la aplicación y autenticarse con conexión.
2. Registrar una operación de prueba sin conexión y comprobar que aparece como pendiente, sin afirmar que está confirmada.
3. Cerrar y volver a abrir la aplicación sin conexión y comprobar el comportamiento documentado de persistencia y bloqueo.
4. Recuperar la conexión y comprobar sincronización, validación del servidor e idempotencia ante reintentos.
5. Provocar un conflicto de prueba y comprobar que se marca para revisión, sin sobrescribir datos.
6. Cerrar sesión o cambiar de usuario con operaciones pendientes y comprobar la advertencia, el aislamiento de la cola y la ausencia de borrado silencioso.
7. Comprobar que el fallo de red, la actualización de la aplicación o la limpieza del almacenamiento del navegador no se presentan como almacenamiento infalible; documentar los límites y orientar al usuario antes de acciones que puedan perder datos.

**Evidencia por ejecución:** modelo de dispositivo, versión Android/Windows, nombre y versión exacta del navegador, fecha, conectividad, resultado por escenario, defectos encontrados y capturas/logs sin datos personales reales. La matriz debe actualizarse al menos cuando cambie la versión principal del navegador o la implementación PWA.

La matriz y las pruebas quedan pendientes de implementación. No se considera cumplido RNF-05 hasta conservar resultados reales en los navegadores incluidos.


## Controles adicionales para importaciones seguras

### Estados del lote
Cada lote tendrá un estado explícito: **Cargado**, **Validando**, **Requiere corrección**, **Pendiente de aprobación**, **Aprobado para consolidación**, **Consolidado**, **Cancelado** o **Fallido**. Solo se podrá consolidar un lote validado y aprobado. Los cambios de estado, el responsable y la fecha/hora se auditarán.

### Separación de responsabilidades
La persona que carga el archivo podrá revisar errores y preparar el lote, pero la aprobación de la consolidación debe corresponder a un usuario con permiso específico. Para importaciones masivas o excepcionales, la Municipalidad debe decidir si exige que quien aprueba sea distinto de quien cargó el archivo. La interfaz no sustituye la autorización del servidor.

### Validación y protección del archivo
- Aceptar únicamente formatos documentados (.xlsx y/o .csv), con tamaño, número de filas y columnas máximos definidos antes de producción.
- Verificar estructura, encabezados, tipos, codificación y campos obligatorios; no confiar solo en la extensión del archivo.
- Rechazar o poner en cuarentena archivos corruptos, protegidos con contraseña cuando no se admitan, o que no puedan procesarse de forma segura. Evaluar análisis antimalware según las capacidades del entorno.
- Tratar los valores importados como datos, nunca como fórmulas o instrucciones. Al generar reportes CSV de errores, neutralizar valores que puedan interpretarse como fórmulas por Excel u otras hojas de cálculo.
- No registrar contenido completo de filas ni DNI completos en logs técnicos. Limitar el acceso al archivo original y a la vista previa según rol.
- Mantener los archivos temporales solo durante el plazo aprobado; eliminar los archivos de staging y las copias temporales al vencer ese plazo, conservando los metadatos de auditoría necesarios.
- Usar identificadores de lote y claves de idempotencia para impedir que un reintento del mismo proceso vuelva a insertar filas duplicadas.

### Fallos y recuperación
Si la validación falla, el lote queda en revisión y el padrón definitivo no cambia. Si falla la transacción de consolidación, debe revertirse toda la transacción. Una importación ya consolidada no se debe “deshacer” borrando a ciegas: se evalúa el impacto, se verifica que no haya cambios posteriores en los registros afectados y se ejecuta una reversión autorizada y auditada o una corrección manual supervisada.

### Pruebas de aceptación adicionales
- El archivo inválido, con estructura inesperada o que excede los límites es rechazado sin modificar el padrón.
- Un usuario sin permiso no puede cargar, aprobar ni consolidar un lote fuera de su ámbito.
- Un lote no aprobado no puede consolidarse invocando directamente una URL o solicitud al servidor.
- Reintentar una importación no crea duplicados.
- Un error durante la transacción no deja filas parcialmente consolidadas.
- Los reportes de error no ejecutan fórmulas procedentes de los datos importados.
- La cancelación y la reversión quedan auditadas; la reversión no elimina cambios posteriores.
- Se puede demostrar el vencimiento y la eliminación de los archivos temporales según el plazo aprobado.

Los límites, formatos admitidos, retención y regla de aprobación por dos personas para cargas masivas requieren decisión municipal. Estos controles son requisitos de diseño pendientes de implementación y pruebas.
