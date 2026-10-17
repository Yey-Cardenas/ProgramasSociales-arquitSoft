# Requisitos no funcionales (RNF)

## Propósito y alcance

Este documento consolida los requisitos no funcionales del Sistema de Gestión de Programas Sociales de Huamanga. Los criterios descritos son **propuestas verificables de diseño** y deben validarse con la Municipalidad y el equipo del proyecto antes de considerarse compromisos definitivos. No afirman que exista una implementación o que las pruebas ya se hayan ejecutado.

Los RNF complementan los escenarios de atributos de calidad de [04-atributos-de-calidad.md](04-atributos-de-calidad.md). Los identificadores RNF se conservan para mantener trazabilidad con los documentos existentes.

## Requisitos

| ID | Categoría | Requisito y criterio de aceptación propuesto | Verificación | Atributo relacionado |
|---|---|---|---|---|
| **RNF-01** | Disponibilidad y operación offline | La aplicación en servidor debe alcanzar al menos **99 % de disponibilidad mensual**, excluyendo únicamente las ventanas de mantenimiento planificado que hayan sido comunicadas y acordadas. Sin conectividad, la PWA debe conservar los registros pendientes y mostrar su estado. Un registro no se considerará confirmado hasta que el servidor responda afirmativamente. | Calcular disponibilidad con registros de monitoreo del mes; probar desconexión, reconexión, reintentos y confirmación del servidor. | AC01 |
| **RNF-02** | Rendimiento | Con un padrón de prueba de **15 000 personas**, la validación de DNI en el servidor debe responder en menos de **500 ms** en condiciones de prueba documentadas. La medición debe indicar configuración del entorno, volumen de datos, concurrencia, percentiles p50/p95/p99, errores y herramienta utilizada; el protocolo de aceptación se describe en el plan de medición. | Prueba de rendimiento automatizada y registro de tiempos de respuesta. | AC02 |
| **RNF-03** | Usabilidad | El flujo principal de registro de beneficiario tendrá como máximo **cinco campos obligatorios**, siempre que la Municipalidad confirme que son suficientes para las reglas del programa. Los errores de validación deben indicar qué corregir sin borrar los datos ya ingresados. | Inspección del formulario y prueba de tareas con representantes de los usuarios previstos. | AC03 |
| **RNF-04** | Seguridad y privacidad | Las comunicaciones entre navegador y aplicación usarán HTTPS. El acceso a funciones y datos estará restringido por roles y ámbito de comité. Los dirigentes solo accederán a su comité asignado; las funciones de administración de usuarios y asignaciones se reservarán al personal expresamente designado. La autorización se validará en el servidor en cada solicitud y al sincronizar operaciones offline. En el despliegue recomendado, la aplicación se alojará en Azure App Service y la base de datos será Azure SQL Database en un recurso separado. No se habilitará acceso público irrestricto. Se preferirá conectividad privada; si no es viable, se aplicarán reglas de firewall restrictivas y documentadas. Cuando sea compatible, se usará identidad administrada y permisos mínimos. Los secretos se mantendrán fuera de GitHub y las reglas temporales de desarrollo se retirarán al terminar las pruebas. Las copias de seguridad y cualquier dato personal persistido localmente deberán contar con medidas de protección apropiadas; la PWA minimizará los datos locales, aislará la cola offline por usuario/comité, evitará datos personales en la caché de recursos estáticos y advertirá antes de descartar registros pendientes. | Revisar HTTPS, autorización por rol, reglas de red, identidad/permisos de la base de datos, almacenamiento local y respaldos; probar accesos no autorizados y confirmar que no exista exposición pública irrestricta. | AC04 |
| **RNF-05** | Compatibilidad | La interfaz web deberá probarse con la matriz inicial de navegadores y dispositivos definida en este documento: Chrome para Android actualizado en dispositivos Android 8+ que soporten las APIs requeridas, Edge para Android como alternativa sujeta a pruebas, y Chrome/Edge estables en Windows. Las funciones offline solo se darán por cumplidas en las combinaciones incluidas en la matriz con resultados de prueba documentados. | Mantener una matriz con dispositivo, versión de sistema operativo, navegador y resultado de las pruebas principales y offline. | AC05 |
| **RNF-06** | Auditabilidad | Cada alta, modificación, baja lógica o decisión de revisión que afecte al padrón, así como las importaciones, exportaciones y cambios de permisos, deberá registrar usuario responsable, fecha/hora del servidor, operación, registro afectado, resultado y motivo cuando corresponda. Los usuarios ordinarios no podrán modificar ni eliminar eventos; las correcciones se registrarán como nuevos eventos. El historial será consultable solo por roles autorizados. | Ejecutar operaciones de prueba y comprobar la integridad y permisos de consulta del historial. | AC06 |
| **RNF-07** | Capacidad y escalabilidad | La solución deberá procesar un padrón de al menos **15 000 beneficiarios** sin superar los umbrales de rendimiento acordados. Como umbral inicial por validar, se propone que el percentil 95 de las consultas habituales del padrón no supere **2 segundos** con 20 usuarios concurrentes, excluyendo exportaciones y generación de mapas pesados. | Prueba de carga con 20 usuarios virtuales durante al menos 30 minutos después del calentamiento, repetida tres veces en condiciones comparables; registrar percentiles, errores y recursos utilizados. Objetivo inicial sujeto a aprobación. | AC07 |
| **RNF-08** | Mantenibilidad y extensibilidad | Incorporar un nuevo programa social debe poder realizarse mediante un módulo cohesionado y reglas/configuración específicas, sin duplicar los módulos transversales de autenticación, auditoría y acceso a datos ni alterar los programas existentes. La necesidad de cambios en componentes compartidos deberá documentarse en la revisión de diseño. | Revisión arquitectónica y ejercicio de diseño para un programa adicional, con análisis de impacto documentado. | AC08 |
| **RNF-09** | Respaldo y recuperación | La base de datos deberá respaldarse al menos una vez al día. Como propuesta inicial por validar, conservar los respaldos durante 30 días y realizar pruebas de restauración al menos trimestrales. Se proponen RTO de hasta 8 horas y RPO de hasta 24 horas, pendientes de aprobación municipal y verificación con la configuración contratada de Azure SQL Database. Estos objetivos no constituyen garantías ni se consideran probados hasta realizar ejercicios de recuperación. | Revisar bitácoras de respaldo, permisos, retención, resultados de restauración y evidencia medida de RTO/RPO en pruebas. | AC09 |

## Decisiones pendientes de validación

Antes de aprobar estos requisitos, el equipo debe confirmar con la Municipalidad:

1. Si el objetivo de disponibilidad del 99 % se medirá mensualmente y qué mantenimiento se excluirá.
2. El entorno de prueba y la concurrencia realista para RNF-02 y RNF-07.
3. Si cinco campos obligatorios son suficientes para el registro sin omitir información exigida por cada programa.
4. Qué medidas de protección en reposo y en dispositivos son viables para los datos personales, además de HTTPS y el control de acceso.
5. La matriz oficial de dispositivos y navegadores soportados.
6. Los plazos de conservación de auditoría y respaldos, el responsable operativo y los objetivos de recuperación.
7. El costo mensual de App Service, base de datos, almacenamiento, monitoreo y conectividad privada, antes de contratar los recursos.
8. La compatibilidad de Azure SQL Database con las consultas, tipos de datos, migraciones de EF Core 10 y demás funcionalidades requeridas por la aplicación.

## Criterios de verificación de Azure SQL Database

Antes de considerar lista la persistencia, el equipo deberá verificar en un entorno de prueba de Azure SQL Database que:
- Las migraciones Code-First de EF Core 10 se generan y aplican correctamente.
- Las consultas y operaciones del padrón producen resultados correctos y respetan las reglas de integridad.
- Los índices, restricciones y transacciones requeridos son compatibles con el servicio.
- Las consultas críticas cumplen los objetivos de rendimiento propuestos en RNF-02 y RNF-07 bajo condiciones de prueba documentadas.
- Las credenciales se mantienen fuera del repositorio y el acceso de red a la base de datos está restringido.

Estos criterios están pendientes de ejecución porque el repositorio aún no incluye el código de la aplicación ni migraciones.

## Trazabilidad

| Requisito no funcional | Escenario de calidad | Driver arquitectónico relacionado |
|---|---|---|
| RNF-01 | AC01 | DA01 |
| RNF-02 | AC02 | DA02 |
| RNF-03 | AC03 | Usabilidad de interfaz |
| RNF-04 | AC04 | DA03 |
| RNF-05 | AC05 | PWA y presentación web |
| RNF-06 | AC06 | DA03 y auditoría transversal |
| RNF-07 | AC07 | DA05 |
| RNF-08 | AC08 | DA04 |
| RNF-09 | AC09 | DA03 |

## Estado de cumplimiento

Los criterios de este documento son requisitos propuestos para diseño y validación. No deben marcarse como implementados o probados hasta que exista código, pruebas y evidencia verificable.


## Criterios de verificación de seguridad de red

Antes de producción, comprobar que Azure SQL Database no acepte conexiones públicas irrestrictas, que las reglas de firewall permitan solo los orígenes necesarios, que las credenciales no estén versionadas y que la aplicación tenga permisos mínimos. Si se habilitaron reglas temporales para desarrollo, confirmar su eliminación. El resultado debe quedar registrado en una lista de verificación de despliegue.


## Pruebas de autorización por rol y ámbito

Antes de aceptar la implementación se probará, como mínimo, que: (1) un dirigente solo consulta y modifica su propio comité; (2) el acceso directo por URL o petición a otro comité es rechazado por el servidor; (3) un usuario municipal sin privilegios administrativos no puede gestionar cuentas ni asignaciones; (4) las decisiones de revisión de duplicados quedan auditadas; y (5) la sincronización offline rechaza operaciones de cuentas desactivadas o sin permiso vigente. Estas pruebas están pendientes porque el repositorio aún no contiene la aplicación.


## Criterios de aceptación de revalidación

- La tarea de revalidación se genera para cada inscripción al cumplirse el periodo semestral definido.
- La falta de respuesta o el vencimiento no causa una baja automática.
- El sistema conserva el historial de estados, responsable, fecha, motivo y decisión final.
- Solo el rol autorizado puede registrar una decisión final de no revalidación.
- Los recordatorios y el escalamiento respetan plazos aprobados por la Municipalidad.
- El sistema registra estado, fecha, responsable y motivo del resultado.
- Un caso sin respuesta permanece pendiente; no se da de baja automáticamente por el solo vencimiento.
- Solo personal autorizado puede registrar la decisión final de no revalidación cuando así lo determine el procedimiento municipal.
- Cambiar el estado de una inscripción no modifica automáticamente otras inscripciones de la misma persona.
- Los recordatorios y escalamiento usan plazos aprobados por la Municipalidad.

Los plazos, estados definitivos, evidencia aceptable y autoridad final deben ser aprobados por el área usuaria. Estas pruebas están pendientes de la implementación.


## Pruebas de resolución de duplicados

- Una alerta pendiente impide confirmar la nueva inscripción en conflicto.
- Un dirigente no puede resolver una alerta que involucre a otro comité.
- Solo personal autorizado de la Sub Gerencia puede registrar la resolución.
- Cada resolución registra resultado, motivo/evidencia, responsable y fecha/hora.
- Una coincidencia de DNI no elimina registros ni desactiva a la persona automáticamente.
- Las correcciones posteriores conservan la decisión previa y quedan registradas como una nueva operación.

Los roles responsables y el procedimiento final deben aprobarse con la Municipalidad. Estas pruebas están pendientes de la implementación.


## Criterios de aceptación de notificaciones

- El sistema distingue el estado aceptado por el proveedor del estado de entrega confirmada.
- Los fallos temporales se reintentan con espera progresiva y límite configurado; los fallos permanentes se marcan para corrección o revisión.
- Cada aviso tiene un identificador interno para detectar reintentos y registrar resultados inciertos sin asegurar falsamente que no se envió.
- El fallo del proveedor no revierte una inscripción confirmada ni bloquea el registro principal.
- Los mensajes minimizan los datos personales y se envían solo a un contacto válido según las reglas aprobadas.
- El personal autorizado puede revisar el historial de avisos y sus errores.

La selección del proveedor, consentimiento, límites de reintento y pruebas de entrega deben definirse antes de implementar. Estos criterios aún no han sido probados.


## Criterios de aceptación de importación de padrones

- Un archivo inválido no modifica el padrón definitivo.
- Cada importación genera un lote identificable con usuario y fecha/hora.
- La vista previa informa filas válidas, inválidas, duplicadas y pendientes de revisión.
- Ningún lote se consolida sin aprobación de un usuario autorizado.
- La consolidación se ejecuta de forma transaccional y no deja una carga parcial no controlada si ocurre un error.
- Los resultados y errores quedan auditados; cancelar un lote no consolidado no modifica el padrón.
- Una reversión posterior identifica los cambios del lote y no sobrescribe cambios posteriores sin revisión.
- Los estados del lote y las transiciones quedan auditados; solo lotes validados y aprobados pueden consolidarse.
- Los límites de tamaño y filas se aplican en el servidor.
- Los reportes CSV de errores neutralizan valores que puedan interpretarse como fórmulas.
- Los reintentos de una misma importación no crean duplicados y una falla transaccional no deja cargas parciales.

Los formatos, tamaño máximo, retención y procedimiento final de aprobación deben validarse con la Municipalidad. Estas pruebas están pendientes de la implementación.


## Objetivos propuestos de recuperación ante incidentes

Como punto de partida para revisión municipal, se proponen estos objetivos de servicio, sujetos a confirmar con la configuración contratada de Azure, los costos y las pruebas:

- **RTO propuesto: hasta 8 horas** para recuperar el servicio principal tras un incidente grave.
- **RPO propuesto: hasta 24 horas** de pérdida máxima de cambios confirmados, como objetivo inicial coherente con la propuesta de respaldo diario. La configuración real de Azure SQL Database puede permitir una recuperación a un punto en el tiempo más reciente; el RPO definitivo debe basarse en la configuración y en una prueba, no asumirse.
- **Respaldo:** al menos diario, con retención inicial propuesta de 30 días, pendiente de validación.
- **Prueba de restauración:** al menos trimestral y después de cambios importantes en la estrategia de respaldo o recuperación.

Procedimiento mínimo: identificar el incidente; avisar al responsable designado; determinar el punto de recuperación disponible; restaurar primero en un entorno controlado; comprobar integridad y consistencia de datos; documentar duración, punto recuperado y resultados; obtener aprobación antes de volver a poner el servicio en producción. Los respaldos y las credenciales de recuperación tendrán acceso restringido.

RTO y RPO son objetivos propuestos, no garantías de servicio ni resultados probados. La Municipalidad debe aprobarlos y asignar un responsable antes de la puesta en producción.



## Criterios de aceptación de exportaciones seguras

- Un dirigente no puede exportar datos de otro comité modificando parámetros o URL.
- Una solicitud no autorizada no genera archivo descargable.
- Las exportaciones se limitan a campos y registros autorizados; el DNI completo no aparece por defecto en reportes generales.
- Cada exportación registra usuario, fecha/hora, formato, ámbito/filtros, cantidad de registros y resultado.
- Los logs no contienen el archivo exportado, DNI completos ni datos personales innecesarios.
- Las exportaciones completas o excepcionales requieren permiso específico y quedan auditadas.
- Se priorizan informes agregados cuando permiten cumplir la finalidad solicitada.

Los campos permitidos y las autorizaciones excepcionales requieren aprobación municipal. Estas pruebas están pendientes de la implementación.


## Criterios de aceptación de integridad de auditoría

- Una cuenta sin permiso no puede consultar el historial de auditoría.
- Un usuario de negocio no puede editar ni eliminar eventos existentes.
- Una corrección queda registrada como nuevo evento relacionado con el anterior.
- Las altas, modificaciones, bajas lógicas, decisiones sobre duplicados, importaciones, exportaciones y cambios de permisos producen eventos auditables.
- Los eventos contienen usuario, fecha/hora del servidor, operación, referencia del registro, resultado y motivo cuando corresponda.
- Los logs no contienen contraseñas, tokens ni datos personales innecesarios.
- Los accesos administrativos y las acciones de supervisión se limitan y registran.

La protección técnica de integridad, la retención y los roles responsables deben verificarse durante la implementación. No se considera probada la inmutabilidad hasta ejecutar estas pruebas.


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
