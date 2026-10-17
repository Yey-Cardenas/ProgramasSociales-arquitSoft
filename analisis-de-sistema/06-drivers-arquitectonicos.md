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
| DA06 | El sistema debe notificar mediante un gateway SMS/WhatsApp externo y registrar fallos. | RC06 – Gateway; RF-07 | Condiciona una integración desacoplada desde la capa de servicios, con estados diferenciados de envío y entrega, reintentos limitados para errores temporales y revisión de fallos permanentes. Una cola o patrón Outbox puede evaluarse para mejorar la confiabilidad cuando se implemente; no se presupone que exista. La falla del proveedor no revierte una inscripción confirmada. |
| DA07 | Los padrones históricos en Excel/CSV deben depurarse y aprobarse antes de consolidarse. | RF-11 – Staging | Obliga a disponer de lotes temporales separados del padrón definitivo, con validación de formato, clasificación de errores, detección de duplicados, revisión por personal autorizado, consolidación transaccional y auditoría del resultado. Una falla no debe dejar una carga parcial no controlada. |
| DA08 | La cobertura debe visualizarse geográficamente por zona. | RF-08, RF-09; RC05 – Azure SQL Database | Condiciona el modelado de zonas, distritos y datos geográficos necesarios para agrupar y presentar beneficiarios activos. La persistencia se realizará en Azure SQL Database mediante Entity Framework Core 10, de acuerdo con el stack aprobado en el README. La fuente de límites geográficos y población estimada debe definirse con datos municipales u otra fuente validada. No se requiere introducir PostgreSQL ni PostGIS como parte de la arquitectura actual. |

## Autorización y segregación de responsabilidades

La aplicación utilizará autorización por roles y ámbito de comité. Un dirigente solo podrá operar sobre su comité asignado; el personal autorizado de la Sub Gerencia podrá consultar el padrón consolidado y revisar duplicidades dentro de su ámbito; la administración de cuentas, roles y asignaciones quedará reservada a personal expresamente designado. La autorización se comprobará en el servidor en cada solicitud y nuevamente al sincronizar operaciones offline. Los intentos no autorizados deberán rechazarse y las decisiones sobre duplicados entre comités deberán auditarse. Esta es la regla de diseño prevista y requiere pruebas de autorización al implementar.

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


## Driver de revalidación y continuidad de inscripción

La revalidación semestral se modelará por inscripción de una persona en un programa/comité. El sistema creará tareas, registrará estados y decisiones auditables, y derivará los casos dudosos a la Sub Gerencia. La ausencia de respuesta no provocará una baja automática; deberá existir un recordatorio o escalamiento según el procedimiento que apruebe la Municipalidad. Una decisión solo afectará la inscripción correspondiente y no las demás inscripciones de la persona. La regla y sus plazos requieren validación del área usuaria.


## Resolución auditada de alertas de duplicados

La Sub Gerencia designará a los usuarios autorizados para resolver alertas. El dirigente solo aportará información y no podrá decidir unilateralmente casos entre comités. Los resultados previstos son: no es duplicado, duplicado confirmado, requiere información adicional o derivado para decisión superior. Mientras no se resuelva, la nueva inscripción en conflicto no se confirma. La decisión registrará alerta, inscripciones/comités, resultado, motivo/evidencia, usuario y fecha/hora. No se borrarán ni fusionarán registros automáticamente por coincidencia de DNI; cualquier cambio posterior se realizará por una operación autorizada y auditada. La política requiere aprobación municipal.


## Política de notificaciones y fallos del proveedor

- **Destinatario y consentimiento:** antes de enviar, verificar que exista un número válido y que el contacto corresponda a la persona o representante autorizado. El uso de WhatsApp/SMS y cualquier requisito de consentimiento o plantilla del proveedor deberán validarse con la Municipalidad y el proveedor elegido.
- **Contenido mínimo:** los mensajes deben evitar DNI completo, detalles sensibles y datos innecesarios. Se limitarán a informar que existe una actualización o tarea y a indicar un canal oficial para consultar más detalles.
- **Estados trazables:** registrar el tipo de aviso, inscripción relacionada, canal, fecha/hora, proveedor, identificador de mensaje del proveedor cuando exista y estado: pendiente, enviado al proveedor, entregado, fallido o estado desconocido. “Enviado” no equivale a “entregado”.
- **Fallos y reintentos:** los errores temporales podrán reintentarse con espera progresiva y un límite configurable. Los errores permanentes (por ejemplo, número inválido o destinatario no disponible) no deben reintentarse indefinidamente; se marcarán para revisión o corrección del contacto.
- **Evitar duplicados:** cada aviso tendrá un identificador interno único y se comprobará el estado previo antes de reintentar. Si el proveedor no ofrece idempotencia, se reconocerá que un tiempo de espera puede dejar el resultado incierto y se evitará afirmar que no se envió; el caso se registrará para reconciliación.
- **Desacoplamiento:** la falla del proveedor no debe revertir una inscripción ya confirmada ni impedir el uso principal del sistema. La tarea de notificación quedará registrada para seguimiento. Se puede implementar una cola o patrón Outbox cuando exista la aplicación; no se afirma que ya exista.
- **Supervisión:** personal autorizado podrá consultar fallos y reintentar o corregir contactos según sus permisos. Los registros de notificación deberán respetar las reglas de privacidad y auditoría.

La selección del proveedor, costos, consentimiento, plantillas, límites de reintento y reglas de contacto deben confirmarse antes de la implementación. Este apartado define el comportamiento esperado, no una integración ya desplegada.



## Procedimiento seguro de importación de padrones históricos

1. **Cargar a staging:** importar Excel/CSV a un lote temporal identificado, sin escribir directamente en el padrón definitivo. Registrar quién carga el archivo, fecha/hora, nombre del archivo y un identificador de lote.
2. **Validar formato:** comprobar extensión/tamaño permitido, columnas esperadas, campos obligatorios, formato de DNI, codificación y filas vacías o mal formadas. Rechazar archivos no válidos sin alterar el padrón definitivo.
3. **Previsualizar y clasificar:** presentar conteos de filas válidas, inválidas, duplicadas, incompletas y pendientes de revisión; permitir descargar un reporte de errores sin exponer datos innecesarios.
4. **Resolver inconsistencias:** no corregir ni fusionar automáticamente personas por coincidencias aproximadas. Los casos dudosos se asignan a personal autorizado; el DNI se valida según las reglas de identidad y duplicidad del sistema.
5. **Aprobar y consolidar:** solo un usuario con permiso específico aprueba el lote. La consolidación debe ser transaccional: se aplican todas las filas aprobadas del lote o ninguna. Los errores deben dejar el lote en estado de revisión sin producir una carga parcial no controlada.
6. **Auditar y verificar:** guardar el archivo/lote de referencia según la política de retención aprobada, el usuario que aprobó, fecha/hora, resumen de filas, errores y resultado. Tras consolidar, mostrar un resumen verificable de altas, actualizaciones, rechazos y pendientes.
7. **Cancelar o revertir:** antes de consolidar, el lote puede cancelarse sin modificar el padrón. Después de consolidar, una reversión solo podrá ejecutarse mediante una operación autorizada y auditada que identifique los cambios del lote y compruebe que no sobrescriba cambios posteriores; si no es segura, se requiere corrección manual supervisada.

La importación debe respetar los permisos por comité y las reglas de duplicidad por programa. Los archivos con datos personales deberán tener acceso restringido y no se mantendrán indefinidamente en staging. El formato exacto, tamaño máximo, retención y responsables de aprobación deben validarse con la Municipalidad. Este flujo es una propuesta de diseño pendiente de implementación y pruebas.



## Objetivos propuestos de recuperación ante incidentes

Como punto de partida para revisión municipal, se proponen estos objetivos de servicio, sujetos a confirmar con la configuración contratada de Azure, los costos y las pruebas:

- **RTO propuesto: hasta 8 horas** para recuperar el servicio principal tras un incidente grave.
- **RPO propuesto: hasta 24 horas** de pérdida máxima de cambios confirmados, como objetivo inicial coherente con la propuesta de respaldo diario. La configuración real de Azure SQL Database puede permitir una recuperación a un punto en el tiempo más reciente; el RPO definitivo debe basarse en la configuración y en una prueba, no asumirse.
- **Respaldo:** al menos diario, con retención inicial propuesta de 30 días, pendiente de validación.
- **Prueba de restauración:** al menos trimestral y después de cambios importantes en la estrategia de respaldo o recuperación.

Procedimiento mínimo: identificar el incidente; avisar al responsable designado; determinar el punto de recuperación disponible; restaurar primero en un entorno controlado; comprobar integridad y consistencia de datos; documentar duración, punto recuperado y resultados; obtener aprobación antes de volver a poner el servicio en producción. Los respaldos y las credenciales de recuperación tendrán acceso restringido.

RTO y RPO son objetivos propuestos, no garantías de servicio ni resultados probados. La Municipalidad debe aprobarlos y asignar un responsable antes de la puesta en producción.



## Control de exportaciones

La exportación debe aplicar autorización en el servidor y no confiar únicamente en ocultar botones de la interfaz. Los permisos consideran el rol y el ámbito de comité. Se registran metadatos de la operación sin copiar el contenido del archivo a los logs; las exportaciones completas requieren permiso específico. Los reportes agregados se priorizan cuando cumplen la finalidad.


## Protección e integridad del historial de auditoría

- **Operaciones registradas:** altas, modificaciones, bajas lógicas, decisiones sobre duplicados, revalidaciones, importaciones, exportaciones, cambios de roles/permisos, asignación de dirigentes a comités y acciones relevantes sobre notificaciones.
- **Contenido mínimo:** registrar identificador del evento, usuario autenticado, fecha/hora del servidor, tipo de operación, entidad/registro afectado, resultado y motivo cuando corresponda. En cambios relevantes, guardar los campos modificados o una referencia segura a la versión anterior y nueva, evitando replicar DNI completos u otros datos personales innecesarios.
- **Historial de solo adición:** los usuarios ordinarios no podrán editar ni borrar eventos de auditoría. Una corrección se registrará como un evento nuevo que referencia el anterior; no se sobrescribirá la decisión original.
- **Separación de permisos:** separar el permiso para realizar operaciones de negocio del permiso para consultar auditoría. Solo roles expresamente autorizados podrán consultar el historial. Ningún usuario funcional tendrá permiso para alterar directamente los registros de auditoría.
- **Protección técnica:** aplicar permisos mínimos a la cuenta de aplicación y a las cuentas de base de datos; restringir acceso administrativo, proteger credenciales y centralizar los eventos relevantes. Evaluar controles de integridad, como encadenamiento de hashes o almacenamiento separado con permisos de solo escritura, según los recursos disponibles. No se afirmará que un mecanismo de inmutabilidad existe hasta implementarlo y probarlo.
- **Acceso administrativo y retención:** el acceso excepcional al historial debe estar limitado y registrado. La Municipalidad definirá el periodo de conservación, el responsable de supervisión y el procedimiento ante sospechas de alteración.
- **Datos sensibles:** no guardar contraseñas, tokens, contenido completo de archivos exportados ni datos personales innecesarios en eventos o logs técnicos.

Criterios de verificación: comprobar que un usuario sin permiso no pueda consultar auditoría; que un usuario de negocio no pueda editar ni eliminar eventos; que las correcciones generen un nuevo evento; y que las modificaciones administrativas relevantes queden registradas. Estas medidas están documentadas como requisitos pendientes de implementación y pruebas.



## Procedimiento propuesto para la revalidación semestral

Este flujo es una propuesta para revisión del área usuaria; los plazos, evidencias exigidas y autoridad final deben aprobarse formalmente por la Municipalidad antes de considerarse política vigente.

1. **Generación de tarea:** el sistema crea una tarea por cada inscripción cuando se aproxima la fecha semestral de revisión. La tarea identifica la inscripción, el programa, el comité responsable y la fecha límite aprobada.
2. **Aviso y verificación inicial:** se notifica al dirigente asignado y, si existe un canal válido, se envía un aviso al beneficiario sin incluir información personal innecesaria. El dirigente verifica los datos con los criterios y evidencias aprobados para ese programa.
3. **Estados de trabajo propuestos:** *Pendiente*, *En verificación*, *Requiere subsanación*, *Derivada a Sub Gerencia*, *Revalidada* y *No revalidada por decisión autorizada*. El estado final debe ser por inscripción, no global para la persona.
4. **Datos incompletos o dudosos:** el dirigente registra el motivo y deriva el caso a la Sub Gerencia. No puede confirmar por sí solo una no revalidación si el procedimiento municipal reserva esa decisión al personal autorizado.
5. **Sin respuesta:** el sistema mantiene la tarea pendiente, envía recordatorios en los plazos que se aprueben y la escala al responsable designado al vencer el plazo. El silencio o el vencimiento no desactivan automáticamente la inscripción ni afectan otras inscripciones de la persona.
6. **Decisión final:** el personal autorizado revisa la evidencia, registra el resultado, el motivo, la fecha y el responsable. Si se decide no revalidar, se aplica únicamente a esa inscripción y conforme al procedimiento formal aprobado.
7. **Auditoría y notificación:** se conserva el historial de estados, responsables, fechas, motivos y decisiones. Se registra por separado el estado de las notificaciones; un fallo de SMS/WhatsApp no cambia la decisión ni el estado de la inscripción.

**Criterios mínimos de aceptación:** la tarea se genera por inscripción; la falta de respuesta deja el caso pendiente o escalado; los cambios y decisiones quedan auditados; solo el rol autorizado registra la decisión final; y ninguna inscripción de otro programa se modifica automáticamente.



## Plan de medición y aceptación del rendimiento

Los valores de rendimiento documentados son objetivos iniciales que deben validarse con la Municipalidad y el equipo técnico; no representan resultados medidos. Como protocolo de aceptación propuesto:

1. **Datos de prueba:** preparar un conjunto sintético de al menos 15 000 personas e inscripciones, con distribución de programas y comités y casos de DNI existente/no existente. No usar datos personales reales en pruebas ordinarias.
2. **Entorno registrado:** documentar versión de .NET y EF Core, configuración de Azure App Service y Azure SQL Database, región, nivel de servicio, índices, tamaño de la base, red y versión del código. No comparar resultados de entornos con configuraciones distintas sin señalarlo.
3. **Preparación:** ejecutar una fase de calentamiento que no se incluya en las mediciones; verificar que el conjunto de datos y el estado de la base sean reproducibles.
4. **Validación de DNI (RNF-02):** medir desde la recepción de la solicitud en el servidor hasta la respuesta de validación, incluyendo consulta a la base de datos y reglas de duplicidad, pero excluyendo latencia de la red del cliente. Registrar p50, p95, p99, errores y número de solicitudes. Criterio propuesto: p95 menor de 500 ms.
5. **Consultas habituales (RNF-07):** definir previamente las consultas incluidas (búsqueda/listado paginado y filtros habituales; no exportaciones masivas ni mapas pesados). Ejecutar una prueba de carga con 20 usuarios virtuales concurrentes durante al menos 30 minutos, tras el calentamiento, y registrar p50/p95/p99, errores, CPU, memoria y conexiones a la base de datos. Criterio propuesto: p95 menor de 2 segundos.
6. **Repetición:** ejecutar cada escenario al menos tres veces en condiciones comparables. Guardar scripts, fecha, configuración, resultados y defectos encontrados. Se acepta el objetivo solo si las ejecuciones cumplen el umbral acordado y no se producen errores funcionales ni pérdida/inconsistencia de datos.
7. **Informe y decisión:** documentar resultados reales frente a objetivos; si no se cumplen, registrar la causa y la acción correctiva. Los umbrales definitivos y el perfil de carga deben ser aprobados antes de considerarlos requisitos contractuales.

Estas pruebas quedan pendientes hasta que exista una implementación ejecutable, base de datos de prueba y entorno de despliegue. No se afirma que el sistema cumpla actualmente los umbrales.


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
