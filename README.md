# 🏛️ Sistema de Gestión de Programas Sociales - Huamanga

![.NET](https://img.shields.io/badge/.NET-10.0-512BD4?style=flat-square&logo=dotnet)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-MVC-blue?style=flat-square&logo=windows)
![Azure SQL Database](https://img.shields.io/badge/Azure_SQL_Database-Azure-0078D4?style=flat-square&logo=microsoftazure)
![Arquitectura](https://img.shields.io/badge/Arquitectura-Monolito_Modular-success?style=flat-square)

Sistema web centralizado diseñado para digitalizar y gestionar el padrón de beneficiarios del **Programa del Vaso de Leche** y los **Comedores Populares** de la Municipalidad Provincial de Huamanga.

Este proyecto busca prevenir duplicidades de inscripción dentro de cada programa social, depurar los padrones históricos y ofrecer visibilidad de la cobertura mediante mapas y reportes. El DNI identifica a la persona, mientras que cada inscripción se evalúa según el programa y comité correspondientes; una coincidencia en otro programa no implica automáticamente una duplicidad.

---
## Curso
Arquitectura de Software (IS-488) – Semestre 2026-II

## Integrantes
Cárdenas García, Yery May  
Chipana Nuñez, Ever Neils

## 🎯 Objetivos del Sistema
- **Padrón depurado:** Detectar posibles inscripciones duplicadas dentro de un mismo programa, revisar los casos entre comités y permitir inscripciones en programas distintos cuando las reglas de elegibilidad lo permitan.
- **Depuración (staging):** Limpiar los datos históricos provenientes de Excel/papel antes de consolidarlos.
- **Cobertura real:** Mostrar mapas y reportes de beneficiarios activos por zonas.
- **Accesibilidad:** Proveer una interfaz PWA para dirigentes de zonas rurales con baja conectividad.

## ⚙️ Arquitectura y patrones
El proyecto está diseñado bajo un enfoque de **monolito modular** utilizando el patrón **MVC (Modelo-Vista-Controlador)**.

- **Patrones de diseño previstos:** Unit of Work, Repository e inyección de dependencias.
- **Metodología:** Spec-Driven Development (SDD).
- **Acceso a datos:** Entity Framework Core, enfoque Code-First.

## 💻 Stack tecnológico
- **Backend:** C# con ASP.NET Core 10.
- **Presentación:** Vistas Razor (`.cshtml`), HTML5, CSS3 y Bootstrap; capacidades PWA.
- **Base de datos:** Azure SQL Database (servicio administrado relacional compatible con el ecosistema SQL Server).
- **ORM:** Entity Framework Core 10.

**Estado de la versión:** .NET 10 LTS, ASP.NET Core 10 y Entity Framework Core 10 son el objetivo tecnológico recomendado para la implementación. Como el repositorio contiene documentación y no el código de la aplicación, esta actualización aún no constituye una migración ejecutada ni probada.

## ☁️ Despliegue recomendado

Se define la opción A como despliegue recomendado: **Azure App Service** para alojar la aplicación ASP.NET Core 10 MVC y **Azure SQL Database** como servicio administrado de base de datos separado. Antes de una implementación real se comprobará la compatibilidad de las consultas, tipos de datos, migraciones de Entity Framework Core 10 y demás funcionalidades necesarias. El acceso a la base de datos se restringirá a la aplicación, preferentemente mediante conectividad privada. Los secretos no deben guardarse en el repositorio. Antes de desplegar, se estimarán los costos mensuales y se probarán los respaldos y la restauración. Esta es una decisión de diseño documentada; el repositorio todavía no contiene una aplicación desplegada.

## Política de autorización por rol y comité

- **Dirigente de comité:** solo puede consultar y gestionar los beneficiarios, inscripciones y operaciones de su comité asignado. No puede consultar ni modificar los registros detallados de otros comités.
- **Personal autorizado de la Sub Gerencia:** puede consultar el padrón consolidado y gestionar las tareas de supervisión, reportes y revisión de posibles duplicados dentro del ámbito municipal que tenga autorizado.
- **Administrador designado de usuarios:** función asignada únicamente al personal municipal autorizado por la Municipalidad. Puede crear, actualizar o desactivar cuentas, asignar roles y vincular dirigentes con sus comités. La asignación o reasignación debe quedar registrada en la auditoría.
- **Decisiones sobre duplicados:** corresponde al personal autorizado de la Sub Gerencia revisar y registrar el resultado, el motivo y el responsable; el dirigente no puede aprobar por sí solo un caso que involucre a otro comité.
- **Aplicación de permisos:** cada solicitud debe validarse en el servidor, no solo ocultando botones o pantallas. Los intentos de acceso fuera del ámbito autorizado se rechazarán y registrarán cuando corresponda.
- **Cambios de asignación:** cuando se reasigne o desactive una cuenta, los permisos nuevos deben aplicarse de inmediato en las siguientes solicitudes. Las operaciones offline se volverán a autorizar en el servidor al sincronizarse.
- **Principio de mínimo privilegio:** los roles se asignan por necesidad funcional; no se presume que todo usuario municipal sea administrador.

Esta política es la propuesta de diseño para documentar y validar con la Municipalidad antes de implementar. No afirma que la autorización ya esté programada o probada.

### Política de seguridad de red y credenciales

En producción, Azure SQL Database no tendrá acceso público irrestricto. Se preferirá conectividad privada desde Azure App Service; si no es viable, se deberán aplicar reglas de firewall restrictivas y documentadas. Cuando sea compatible con la configuración, se preferirá la identidad administrada de App Service y permisos mínimos. Las cadenas de conexión y secretos se configurarán fuera de GitHub. Los accesos públicos temporales para desarrollo se limitarán a las IP necesarias y se retirarán al terminar las pruebas. La configuración real deberá verificarse antes del despliegue.

### Criterios de compatibilidad con Azure SQL Database

La aplicación usará el proveedor de Entity Framework Core para SQL Server (Microsoft.EntityFrameworkCore.SqlServer) con una cadena de conexión específica para Azure SQL Database. Esta elección es compatible a nivel de familia de motor, pero no significa que toda consulta o característica de SQL Server esté disponible en Azure SQL Database sin ajustes.

Antes de implementar o desplegar se deberá:
- Mantener versiones compatibles de .NET 10, EF Core 10 y el proveedor de SQL Server de EF Core.
- Probar las migraciones Code-First contra una base de datos de prueba de Azure SQL Database, no solo contra una instancia local.
- Revisar tipos de datos, índices, restricciones, transacciones, consultas LINQ, funciones SQL, procedimientos almacenados y cualquier característica específica de SQL Server que se decida usar.
- Configurar la cadena de conexión y los secretos fuera del repositorio; aplicar cifrado en tránsito y acceso mínimo necesario.
- Ejecutar pruebas de integración y verificar el rendimiento de las consultas con datos de prueba representativos.

Estos pasos son criterios pendientes de verificación; no se afirma que la compatibilidad haya sido probada porque todavía no hay código fuente ni migraciones en el repositorio.

## 📚 Documentación del repositorio

El repositorio contiene actualmente documentación de análisis y diseño. La existencia de un documento describe una decisión o requisito previsto; no implica que la funcionalidad esté implementada o probada.

### Análisis del sistema
- [Actores](analisis-de-sistema/01-actores.md)
- [Historias de usuario](analisis-de-sistema/02-historias-de-usuario.md)
- [Requisitos funcionales](analisis-de-sistema/03-requisitos-funcionales.md)
- [Atributos de calidad](analisis-de-sistema/04-atributos-de-calidad.md)
- [Restricciones](analisis-de-sistema/05-restricciones.md)
- [Drivers arquitectónicos](analisis-de-sistema/06-drivers-arquitectonicos.md)
- [Requisitos no funcionales](analisis-de-sistema/07-requisitos-no-funcionales.md)

### Arquitectura
- [Modelo C4 y vista de despliegue](arquitectura/C4/README.md)
- [Arquitectura inicial](arquitectura/arquitectura-inicial.md)
- [Enfoque arquitectónico](arquitectura/enfoque-arquitectonico.md)
- [Estilo arquitectónico](arquitectura/estilo-arquitectonico.md)

## Estado del proyecto
El repositorio está enfocado en análisis de requisitos y documentación arquitectónica; todavía no contiene la implementación de la aplicación. Los requisitos cuantitativos y las decisiones marcadas como pendientes deben validarse con el equipo y la Municipalidad antes de darse por aprobados.


## Flujo de revalidación de inscripciones

1. El sistema identifica las inscripciones que se aproximan a su fecha de revalidación semestral y crea una tarea pendiente.
2. Se notifica al dirigente del comité asignado y, cuando corresponda y exista un canal válido, al beneficiario. La notificación no debe exponer datos personales innecesarios.
3. El dirigente verifica la información conforme a los criterios aprobados para el programa y registra la evidencia o el resultado de la revisión.
4. Los casos dudosos, disputados o que puedan afectar la continuidad del apoyo se derivan a personal autorizado de la Sub Gerencia.
5. El resultado se registra sobre la inscripción específica: pendiente, en revisión, revalidada o no revalidada, junto con fecha, responsable y motivo. Los nombres definitivos de estados deben validarse con el área usuaria.
6. Si no hay respuesta, la inscripción permanece pendiente y se genera un recordatorio o escalamiento según los plazos que apruebe la Municipalidad. El vencimiento por sí solo no produce una baja automática.
7. Una decisión de no revalidación requiere revisión y registro de la causa por personal autorizado, siguiendo el procedimiento municipal. No debe desactivar a la persona ni afectar sus inscripciones en otros programas de forma automática.

La periodicidad semestral es la regla propuesta actualmente. Los responsables, evidencias aceptadas, plazos de recordatorio, decisión final y tratamiento de casos sin respuesta deben ser confirmados por la Municipalidad antes de implementar. Este flujo es una propuesta documentada, no una funcionalidad ya implementada.



## Procedimiento para resolver alertas de posible duplicado

La decisión final corresponde al personal de la Sub Gerencia expresamente autorizado para revisar padrones; el dirigente puede aportar información, pero no resolver por sí solo una alerta que involucre a otro comité. Cada alerta tendrá uno de estos resultados propuestos:

- **No es duplicado:** se documenta la verificación y se permite continuar el trámite de la nueva inscripción, si cumple los demás requisitos.
- **Duplicado confirmado:** no se confirma la nueva inscripción duplicada en el mismo programa. El revisor registra la inscripción existente y la justificación; cualquier corrección o baja de una inscripción previa debe seguir el procedimiento municipal y no se ejecuta automáticamente.
- **Requiere información adicional:** el caso queda pendiente, se registra qué información falta y se asigna seguimiento.
- **Derivado para decisión superior:** se remite a la persona responsable que designe la Municipalidad cuando el caso no pueda resolverse con los criterios ordinarios.

La resolución debe registrar como mínimo: identificador de alerta, persona/inscripciones implicadas, programa y comités involucrados, resultado, motivo o evidencia revisada, usuario que decide, fecha/hora y cualquier cambio posterior realizado. El historial no debe borrarse ni sobrescribirse; una corrección posterior se registra como una nueva operación auditada. Mientras la alerta esté pendiente, no se confirma la nueva inscripción en conflicto. La coincidencia de DNI por sí sola no autoriza a eliminar registros ni a desactivar a la persona en otros programas.

Estos resultados y la autoridad responsable son una propuesta de diseño que la Municipalidad debe aprobar antes de implementar.



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



## Aclaración del alcance de los actores

En el alcance actual, los usuarios que interactúan directamente con las vistas son el dirigente de comité y el personal municipal autorizado. El beneficiario se identifica como destinatario de notificaciones SMS/WhatsApp y no requiere iniciar sesión ni utilizar una interfaz en esta versión. Si una versión futura incorpora un portal o aplicación para beneficiarios, deberá documentarse como un cambio de alcance y actualizarse entonces el modelo de actores correspondiente.


## Objetivos propuestos de recuperación ante incidentes

Como punto de partida para revisión municipal, se proponen estos objetivos de servicio, sujetos a confirmar con la configuración contratada de Azure, los costos y las pruebas:

- **RTO propuesto: hasta 8 horas** para recuperar el servicio principal tras un incidente grave.
- **RPO propuesto: hasta 24 horas** de pérdida máxima de cambios confirmados, como objetivo inicial coherente con la propuesta de respaldo diario. La configuración real de Azure SQL Database puede permitir una recuperación a un punto en el tiempo más reciente; el RPO definitivo debe basarse en la configuración y en una prueba, no asumirse.
- **Respaldo:** al menos diario, con retención inicial propuesta de 30 días, pendiente de validación.
- **Prueba de restauración:** al menos trimestral y después de cambios importantes en la estrategia de respaldo o recuperación.

Procedimiento mínimo: identificar el incidente; avisar al responsable designado; determinar el punto de recuperación disponible; restaurar primero en un entorno controlado; comprobar integridad y consistencia de datos; documentar duración, punto recuperado y resultados; obtener aprobación antes de volver a poner el servicio en producción. Los respaldos y las credenciales de recuperación tendrán acceso restringido.

RTO y RPO son objetivos propuestos, no garantías de servicio ni resultados probados. La Municipalidad debe aprobarlos y asignar un responsable antes de la puesta en producción.



## Política de exportación segura de padrones y reportes

- **Autorización por ámbito:** cada solicitud de exportación debe validar permisos en el servidor. El dirigente solo podrá exportar información de su comité asignado; el personal de la Sub Gerencia solo podrá exportar dentro del ámbito municipal autorizado. El permiso para consultar datos no concede automáticamente permiso para exportar el padrón completo.
- **Finalidad y campos mínimos:** ofrecer reportes agregados cuando sean suficientes. Incluir datos personales solo cuando sean necesarios para la finalidad autorizada. El DNI completo no debe incluirse por defecto en reportes generales; si resulta imprescindible, debe existir un permiso específico y una justificación.
- **Auditoría:** registrar usuario, fecha/hora, tipo de exportación, formato, filtros o ámbito solicitado, cantidad de registros y resultado de la operación. No guardar el contenido del archivo, DNI completos ni otros datos sensibles en los logs.
- **Descarga protegida:** generar el archivo únicamente después de autorizar la solicitud; informar que contiene datos personales cuando corresponda y evitar que las URL temporales de descarga queden reutilizables o expuestas en registros. No generar archivos para solicitudes denegadas.
- **Uso y conservación:** indicar al usuario que debe almacenar y compartir el archivo solo por canales autorizados y eliminar copias cuando dejen de ser necesarias, de acuerdo con la política municipal. El acceso a exportaciones completas debe limitarse y revisarse periódicamente.
- **Excepciones:** cualquier exportación fuera del alcance habitual debe ser aprobada por la persona que la Municipalidad designe y quedar auditada.

Los permisos exactos, campos exportables, retención de archivos y responsables de autorizar excepciones deben aprobarse con la Municipalidad. Estas reglas describen el comportamiento esperado y están pendientes de implementación y pruebas.



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


## Fuente, actualización y validación de datos de cobertura geográfica

### Fuentes de datos propuestas
- **Límites y códigos territoriales:** priorizar cartografía y códigos oficiales facilitados o validados por la Municipalidad Provincial de Huamanga y la entidad pública competente. Registrar la fuente, versión y fecha de vigencia.
- **Población de referencia:** utilizar publicaciones oficiales del INEI (censo, estimaciones o proyecciones disponibles para la unidad territorial y periodo que correspondan), siempre que la desagregación sea compatible con el reporte. La cifra debe identificarse como población de referencia/estimada, no como número de beneficiarios elegibles.
- **Datos municipales:** si se emplean padrones o delimitaciones municipales, registrar el área responsable, fecha de corte y metodología. No mezclar cifras de años o unidades territoriales distintos sin advertirlo.

### Metadatos y actualización
Cada conjunto importado debe conservar: institución fuente, nombre del conjunto, fecha de publicación o versión, fecha de descarga/recepción, año de referencia, unidad geográfica, responsable de validación y observaciones. La Sub Gerencia o el área municipal que designe la Municipalidad será responsable de aprobar su uso. Como propuesta operativa, revisar la disponibilidad de nuevos datos al menos una vez al año y actualizar antes si la fuente oficial publica una versión pertinente; la periodicidad definitiva debe aprobarse municipalmente.

### Cálculo y presentación
- Mostrar por separado la cantidad de beneficiarios activos del sistema y la población de referencia, indicando el periodo de cada cifra.
- Calcular cobertura porcentual solo cuando numerador y denominador correspondan a la misma zona y exista una definición municipal aprobada de la población de referencia. Mostrar fórmula, fecha de corte y fuente.
- Si faltan datos, la unidad territorial no coincide o la fuente está desactualizada, mostrar “sin dato comparable” y no inventar ni estimar silenciosamente un porcentaje.
- No interpretar población total como población elegible para un programa. Si se requiere cobertura de población objetivo, la fuente y definición de población objetivo deben aprobarse por la Municipalidad.
- Las correcciones y actualizaciones deben conservar el historial de la fuente y del valor reemplazado.

### Criterios de aceptación
1. Cada mapa/reporte identifica fuente, año de referencia y fecha de corte de los datos usados.
2. Las zonas usan códigos y límites de una fuente aprobada; no se mezclan límites incompatibles.
3. El porcentaje se calcula solo con unidades y periodos comparables y con una definición de denominador aprobada.
4. La falta de información se presenta como tal, sin crear cifras.
5. La actualización deja trazabilidad de quién la validó, cuándo y qué versión se incorporó.
6. Los cambios en límites o códigos territoriales se revisan antes de asociarles padrones históricos.

La selección final de conjuntos de datos, denominador de cobertura, responsable y periodicidad requiere aprobación municipal. Estas reglas son requisitos documentados pendientes de implementar y probar.


## Protección de datos locales y dispositivos compartidos

- **Minimización:** almacenar localmente solo los campos imprescindibles para las operaciones pendientes. No guardar padrones completos ni datos sensibles en la caché de recursos estáticos. La cola offline se separará de la caché de la interfaz.
- **Aislamiento por cuenta:** cada operación pendiente quedará vinculada al identificador del usuario y al comité con el que se creó. Una cuenta diferente no podrá ver, enviar ni modificar la cola de otro usuario.
- **Cierre de sesión:** si existen operaciones pendientes, advertir claramente que aún no están confirmadas y ofrecer volver para sincronizarlas cuando haya conexión o descartarlas mediante una confirmación explícita. No borrar silenciosamente datos pendientes ni permitir que la siguiente cuenta los herede. Hasta resolverlos, la cola permanecerá bloqueada para otras cuentas en ese dispositivo.
- **Limpieza al salir:** al cerrar sesión, invalidar la sesión local y limpiar tokens, datos de vistas y cachés que no sean necesarios. Los registros pendientes solo podrán conservarse en un área local aislada de la cuenta propietaria, inaccesible para otros usuarios y visible al propietario tras volver a autenticarse. El borrado explícito debe advertir que los registros no sincronizados se perderán.
- **Protección del dispositivo:** recomendar bloqueo de pantalla, sistema operativo actualizado y no compartir la cuenta. Usar HTTPS y las protecciones de almacenamiento disponibles en la plataforma. No considerar IndexedDB cifrado automáticamente ni prometer protección contra alguien que controle el dispositivo.
- **Pérdida o robo:** un administrador autorizado podrá desactivar la cuenta y revocar sesiones del lado servidor; la sincronización posterior volverá a validar permisos. Una aplicación web no puede garantizar el borrado remoto de datos locales de un dispositivo perdido, por lo que la minimización y el bloqueo del dispositivo son esenciales.
- **Caché y expiración:** almacenar en caché solo recursos estáticos necesarios para abrir la interfaz offline. No cachear respuestas de padrón, informes, exportaciones ni páginas con datos personales mediante caché compartida. Definir y probar la limpieza de caché y almacenamiento al cerrar sesión, al cambiar de usuario y al actualizar la aplicación.
- **Límites de almacenamiento:** informar que el navegador puede eliminar datos locales y que el almacenamiento offline no sustituye una copia confirmada en el servidor. Los registros solo se consideran guardados en el sistema central después de recibir confirmación del servidor.

### Pruebas de aceptación
1. Una segunda cuenta en el mismo dispositivo no puede ver ni sincronizar operaciones pendientes de la primera.
2. Cerrar sesión con registros pendientes muestra una advertencia y no los borra sin confirmación.
3. Al descartar registros pendientes, se exige confirmación explícita y se muestra cuántas operaciones se perderán.
4. La caché no contiene padrones completos, exportaciones ni respuestas con datos personales.
5. Tras revocar una cuenta, el servidor rechaza su sincronización posterior y el cliente muestra el resultado sin confirmar los registros.
6. El usuario recibe una advertencia sobre las limitaciones del almacenamiento local y el riesgo de pérdida o robo del dispositivo.

Estas reglas describen el diseño esperado y quedan pendientes de implementación y pruebas. La Municipalidad debe aprobar los periodos de conservación local y las reglas para descartar operaciones pendientes.


## Procedimiento operativo de respaldo y recuperación

Los objetivos de esta sección son metas de diseño iniciales, no compromisos de disponibilidad hasta que la Municipalidad los apruebe y se validen en el servicio contratado.

### Objetivos iniciales
- **RTO (tiempo objetivo de recuperación):** hasta 8 horas desde la declaración del incidente hasta que el servicio esencial vuelva a estar disponible y haya superado las verificaciones funcionales.
- **RPO (punto objetivo de recuperación):** hasta 24 horas de cambios confirmados en el servidor, como máximo, que podrían perderse en el escenario de recuperación acordado. El RPO real depende de la configuración de respaldo/recuperación de Azure SQL Database y debe medirse.
- **Frecuencia y retención:** comprobar que la configuración de Azure SQL Database cubra las necesidades de recuperación a un punto en el tiempo y conservar una retención inicial objetivo de 30 días, pendiente de validar por costo, configuración y obligaciones de conservación. No asumir que una política manual de “respaldo diario” describe por sí sola todos los respaldos automáticos del servicio administrado.

### Responsables y procedimiento
1. La Municipalidad designará un responsable de incidentes y un suplente con permisos restringidos para iniciar la recuperación.
2. Registrar hora de detección, alcance, sistemas afectados, último punto de recuperación disponible y decisión tomada.
3. Restaurar primero en un entorno aislado, nunca sobrescribir la base de producción como primera acción.
4. Verificar integridad de tablas, relaciones, conteos de referencia, auditoría, usuarios/roles y consistencia de inscripciones; revisar también la configuración y los secretos por canales seguros.
5. Medir el tiempo transcurrido y la antigüedad del último cambio recuperado. Si se exceden RTO/RPO, registrar la desviación y aprobar medidas correctivas.
6. Autorizar el retorno a producción únicamente tras la validación del responsable designado; registrar hora de reapertura y comunicar el estado a los usuarios.
7. Ejecutar pruebas de restauración al menos trimestrales y después de cambios relevantes en la estrategia de respaldo. Conservar evidencias sin incluir datos personales innecesarios.

### Alcance y límites
Los respaldos de la base de datos no recuperan automáticamente operaciones que solo existían en el almacenamiento local de un dispositivo y nunca se sincronizaron con el servidor. La interfaz debe advertir que los registros offline pendientes no son una copia de seguridad central. La recuperación de la aplicación, configuración, archivos de importación y secretos debe documentarse por separado, sin guardar secretos en GitHub.

### Criterios de aceptación
- Existe un responsable titular y un suplente identificados por la Municipalidad.
- La configuración contratada de Azure SQL Database y su retención están documentadas.
- Una restauración de prueba puede completarse en un entorno aislado y su resultado queda registrado.
- Se verifica integridad y se mide el RTO desde la declaración del incidente hasta la validación del servicio.
- Se calcula el RPO con la antigüedad del último cambio confirmado recuperado.
- Se documentan las limitaciones de recuperación de operaciones exclusivamente locales y no sincronizadas.
- Cualquier incumplimiento de objetivos produce un registro de desviación y una acción correctiva.

Los plazos, responsables y objetivos finales requieren aprobación municipal. Los valores propuestos no deben presentarse como garantías ni como resultados probados hasta completar las pruebas.
