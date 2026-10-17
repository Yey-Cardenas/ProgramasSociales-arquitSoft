# Requisitos no funcionales (RNF)

## Propósito y alcance

Este documento consolida los requisitos no funcionales del Sistema de Gestión de Programas Sociales de Huamanga. Los criterios descritos son **propuestas verificables de diseño** y deben validarse con la Municipalidad y el equipo del proyecto antes de considerarse compromisos definitivos. No afirman que exista una implementación o que las pruebas ya se hayan ejecutado.

Los RNF complementan los escenarios de atributos de calidad de [04-atributos-de-calidad.md](04-atributos-de-calidad.md). Los identificadores RNF se conservan para mantener trazabilidad con los documentos existentes.

## Requisitos

| ID | Categoría | Requisito y criterio de aceptación propuesto | Verificación | Atributo relacionado |
|---|---|---|---|---|
| **RNF-01** | Disponibilidad y operación offline | La aplicación en servidor debe alcanzar al menos **99 % de disponibilidad mensual**, excluyendo únicamente las ventanas de mantenimiento planificado que hayan sido comunicadas y acordadas. Sin conectividad, la PWA debe conservar los registros pendientes y mostrar su estado. Un registro no se considerará confirmado hasta que el servidor responda afirmativamente. | Calcular disponibilidad con registros de monitoreo del mes; probar desconexión, reconexión, reintentos y confirmación del servidor. | AC01 |
| **RNF-02** | Rendimiento | Con un padrón de prueba de **15 000 personas**, la validación de DNI en el servidor debe responder en menos de **500 ms** en condiciones de prueba documentadas. La medición debe indicar hardware, volumen de datos, concurrencia y percentil utilizado; se propone usar el percentil 95. | Prueba de rendimiento automatizada y registro de tiempos de respuesta. | AC02 |
| **RNF-03** | Usabilidad | El flujo principal de registro de beneficiario tendrá como máximo **cinco campos obligatorios**, siempre que la Municipalidad confirme que son suficientes para las reglas del programa. Los errores de validación deben indicar qué corregir sin borrar los datos ya ingresados. | Inspección del formulario y prueba de tareas con representantes de los usuarios previstos. | AC03 |
| **RNF-04** | Seguridad y privacidad | Las comunicaciones entre navegador y aplicación usarán HTTPS. El acceso a funciones y datos estará restringido por roles y ámbito de comité. Los dirigentes solo accederán a su comité asignado; las funciones de administración de usuarios y asignaciones se reservarán al personal expresamente designado. La autorización se validará en el servidor en cada solicitud y al sincronizar operaciones offline. En el despliegue recomendado, la aplicación se alojará en Azure App Service y la base de datos será Azure SQL Database en un recurso separado. No se habilitará acceso público irrestricto. Se preferirá conectividad privada; si no es viable, se aplicarán reglas de firewall restrictivas y documentadas. Cuando sea compatible, se usará identidad administrada y permisos mínimos. Los secretos se mantendrán fuera de GitHub y las reglas temporales de desarrollo se retirarán al terminar las pruebas. Las copias de seguridad y cualquier dato personal persistido localmente deberán contar con medidas de protección apropiadas; la PWA evitará almacenar innecesariamente datos personales sensibles. | Revisar HTTPS, autorización por rol, reglas de red, identidad/permisos de la base de datos, almacenamiento local y respaldos; probar accesos no autorizados y confirmar que no exista exposición pública irrestricta. | AC04 |
| **RNF-05** | Compatibilidad | La interfaz web deberá probarse en navegadores compatibles con la PWA en Android 8 o superior y en un navegador de escritorio soportado. Las funciones offline solo se darán por cumplidas en los navegadores/dispositivos incluidos en la matriz de pruebas. | Mantener una matriz con dispositivo, versión de sistema operativo, navegador y resultado de las pruebas principales y offline. | AC05 |
| **RNF-06** | Auditabilidad | Cada alta, modificación, baja lógica o decisión de revisión que afecte al padrón, así como las importaciones, exportaciones y cambios de permisos, deberá registrar usuario responsable, fecha/hora del servidor, operación, registro afectado, resultado y motivo cuando corresponda. Los usuarios ordinarios no podrán modificar ni eliminar eventos; las correcciones se registrarán como nuevos eventos. El historial será consultable solo por roles autorizados. | Ejecutar operaciones de prueba y comprobar la integridad y permisos de consulta del historial. | AC06 |
| **RNF-07** | Capacidad y escalabilidad | La solución deberá procesar un padrón de al menos **15 000 beneficiarios** sin superar los umbrales de rendimiento acordados. Como umbral inicial por validar, se propone que el percentil 95 de las consultas habituales del padrón no supere **2 segundos** con 20 usuarios concurrentes, excluyendo exportaciones y generación de mapas pesados. | Prueba de carga con volumen y concurrencia documentados; registrar tiempos, errores y recursos utilizados. | AC07 |
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

