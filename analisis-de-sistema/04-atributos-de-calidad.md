# Atributos de calidad

## Escenario general

Un dirigente de comité registra a varios beneficiarios en una zona con baja cobertura de internet. Al recuperar la conexión, la PWA sincroniza los registros pendientes y el servidor valida el DNI y las reglas de negocio. El personal de la Sub Gerencia consulta la cobertura. Los datos personales deben protegerse durante el uso, la sincronización, la auditoría y los respaldos.

Los criterios cuantitativos y su método de verificación se detallan en [07-requisitos-no-funcionales.md](07-requisitos-no-funcionales.md). Los valores que aún requieren acuerdo municipal se consideran objetivos propuestos, no resultados ya demostrados.

## Escenarios de calidad

| ID | Atributo de calidad | Escenario de calidad | Origen |
|---|---|---|---|
| AC01 | **Disponibilidad y operación offline** | La aplicación en servidor tendrá una disponibilidad mensual objetivo de al menos 99 %. Sin conexión, la PWA conservará los registros pendientes y mostrará su estado. Solo se considerarán confirmados cuando el servidor los acepte; los reintentos no deben generar altas duplicadas y los conflictos se enviarán a revisión sin sobrescritura automática. | RNF-01 |
| AC02 | **Rendimiento** | Con 15 000 personas en el conjunto de prueba, la validación de DNI deberá responder en menos de 500 ms en el percentil 95, bajo condiciones de prueba documentadas. | RNF-02 |
| AC03 | **Usabilidad** | El registro tendrá como máximo cinco campos obligatorios si el equipo municipal confirma que estos permiten cumplir las reglas de cada programa. Los errores deben orientar la corrección sin borrar la información ingresada. | RNF-03 |
| AC04 | **Seguridad y privacidad** | Las comunicaciones usarán HTTPS; las funciones y datos estarán restringidos por roles y ámbito de comité. Los dirigentes solo podrán acceder a su comité asignado; la administración de usuarios y asignaciones estará reservada a personal designado. La autorización se validará en el servidor y al sincronizar operaciones offline. Azure SQL Database no tendrá acceso público irrestricto: se preferirá conectividad privada y, si no es viable, reglas de firewall restrictivas. Cuando sea compatible, se usará identidad administrada y permisos mínimos. Los secretos permanecerán fuera de GitHub. Los datos personales locales y los respaldos tendrán medidas de protección apropiadas y se evitará el almacenamiento local innecesario. | RNF-04 |
| AC05 | **Compatibilidad** | La aplicación se verificará en una matriz acordada de dispositivos Android 8 o superior, navegadores móviles y navegador de escritorio. Las capacidades offline se probarán explícitamente en los entornos soportados. | RNF-05 |
| AC06 | **Auditabilidad** | Cada alta, modificación, baja lógica o decisión de revisión del padrón registrará como mínimo usuario, fecha y hora, operación y motivo. El historial estará protegido y limitado a roles autorizados. | RNF-06 |
| AC07 | **Capacidad y escalabilidad** | La solución soportará al menos 15 000 beneficiarios. Como objetivo inicial pendiente de validación, las consultas habituales tendrán percentil 95 inferior a 2 segundos con 20 usuarios concurrentes, excluyendo exportaciones y mapas pesados. | RNF-07 |
| AC08 | **Mantenibilidad** | Incorporar un programa social nuevo no debe requerir duplicar módulos transversales ni rediseñar la arquitectura; el impacto sobre componentes compartidos deberá documentarse. | RNF-08 |
| AC09 | **Respaldo de información** | Azure SQL Database tendrá respaldos diarios. Se propone inicialmente una retención de 30 días y pruebas trimestrales de restauración. Como objetivos iniciales por validar se plantean RTO de hasta 8 horas y RPO de hasta 24 horas; deben verificarse con la configuración contratada y pruebas de recuperación, sin considerarse garantías. | RNF-09 |

## Escenarios con formato estímulo – respuesta – medida

### AC02 – Rendimiento

| Elemento | Descripción |
|---|---|
| **Estímulo** | Un usuario ingresa el DNI de una persona para iniciar una inscripción. |
| **Respuesta** | El servidor valida el DNI y aplica las reglas de inscripción por programa y comité, consultando el padrón pertinente. |
| **Medida** | Con 15 000 personas y condiciones de carga documentadas, la respuesta de validación será inferior a 500 ms en el percentil 95. Se registrarán hardware, concurrencia, volumen y herramienta de medición. |

### AC01 – Disponibilidad y operación offline

| Elemento | Descripción |
|---|---|
| **Estímulo** | Un dirigente registra beneficiarios en una zona sin conexión a internet. |
| **Respuesta** | La PWA conserva cada registro pendiente con un identificador único de operación. Al recuperar conexión, lo envía al servidor, que valida DNI y reglas de negocio. Los reintentos son idempotentes; los conflictos quedan marcados para revisión y no reemplazan datos existentes. |
| **Medida** | En pruebas de desconexión, reconexión y reintento, ningún registro se mostrará como confirmado antes de la aceptación del servidor, no se crearán altas repetidas y los conflictos permanecerán visibles. La disponibilidad mensual del servicio se medirá por separado, con objetivo de al menos 99 %. |

## Criterio de interpretación

Estos escenarios definen objetivos que deben verificarse mediante pruebas o revisión arquitectónica. Los números propuestos —disponibilidad, tiempos de respuesta, concurrencia y retención— deben confirmarse con la Municipalidad y el equipo técnico antes de considerarse compromisos definitivos. No existe evidencia de cumplimiento mientras no se disponga de implementación y resultados de prueba.


## Verificación de la persistencia

La solución propuesta utiliza Azure SQL Database con Entity Framework Core 10 y el proveedor Microsoft.EntityFrameworkCore.SqlServer. La compatibilidad se validará mediante pruebas de migraciones, integridad y rendimiento en un entorno de Azure SQL Database. Esta comprobación queda pendiente hasta contar con código y migraciones; no se considera un resultado ya demostrado.


## Verificación de seguridad de red

Antes de producción se revisarán las reglas de acceso a Azure SQL Database, los permisos de la aplicación, el uso seguro de secretos y la eliminación de accesos temporales de desarrollo. La exposición pública irrestricta no es aceptable. La comprobación queda pendiente hasta que exista una infraestructura desplegada.


## Seguridad y trazabilidad del proceso de revalidación

La revalidación se gestionará por inscripción y no como un estado global de la persona. El proceso registrará tarea, estado, fecha, responsable y motivo de la decisión. La falta de respuesta no generará una baja automática: el caso seguirá pendiente y se recordará o escalará conforme a los plazos que apruebe la Municipalidad. Las decisiones que afecten la continuidad del apoyo deben ser tomadas por personal autorizado según el procedimiento municipal. Estos criterios deberán validarse y probarse cuando exista la aplicación.


## Confiabilidad de notificaciones externas

Las notificaciones se tratarán como tareas independientes de la confirmación de una inscripción. Se registrarán estados diferenciados (pendiente, aceptado por proveedor, entregado, fallido o desconocido), con reintentos limitados para fallos temporales y revisión de errores permanentes. El sistema no debe afirmar que un mensaje fue entregado si el proveedor no confirma ese estado. La falla de SMS/WhatsApp no debe revertir una operación de padrón confirmada. El mensaje minimizará datos personales y los resultados quedarán disponibles para personal autorizado. Estos criterios requieren pruebas cuando exista la integración.


## Objetivos propuestos de recuperación ante incidentes

Como punto de partida para revisión municipal, se proponen estos objetivos de servicio, sujetos a confirmar con la configuración contratada de Azure, los costos y las pruebas:

- **RTO propuesto: hasta 8 horas** para recuperar el servicio principal tras un incidente grave.
- **RPO propuesto: hasta 24 horas** de pérdida máxima de cambios confirmados, como objetivo inicial coherente con la propuesta de respaldo diario. La configuración real de Azure SQL Database puede permitir una recuperación a un punto en el tiempo más reciente; el RPO definitivo debe basarse en la configuración y en una prueba, no asumirse.
- **Respaldo:** al menos diario, con retención inicial propuesta de 30 días, pendiente de validación.
- **Prueba de restauración:** al menos trimestral y después de cambios importantes en la estrategia de respaldo o recuperación.

Procedimiento mínimo: identificar el incidente; avisar al responsable designado; determinar el punto de recuperación disponible; restaurar primero en un entorno controlado; comprobar integridad y consistencia de datos; documentar duración, punto recuperado y resultados; obtener aprobación antes de volver a poner el servicio en producción. Los respaldos y las credenciales de recuperación tendrán acceso restringido.

RTO y RPO son objetivos propuestos, no garantías de servicio ni resultados probados. La Municipalidad debe aprobarlos y asignar un responsable antes de la puesta en producción.



## Seguridad y auditabilidad de exportaciones

Las exportaciones deben aplicar autorización por rol y ámbito en el servidor, minimizar los campos personales y auditar usuario, fecha/hora, formato, filtros/ámbito, cantidad de registros y resultado, sin registrar el contenido del archivo ni DNI completos en logs. Consultar el padrón no concede por sí solo permiso para exportarlo completo. Las pruebas deben comprobar que solicitudes no autorizadas no generan archivos.


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
