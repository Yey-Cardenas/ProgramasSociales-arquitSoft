# Requisitos funcionales

## Requisitos de la propuesta

| ID | Módulo | Requisito funcional |
|----|--------|---------------------|
| RF-01 | Registro de beneficiarios | El sistema debe permitir a los dirigentes de comité registrar beneficiarios usando el DNI como dato obligatorio. |
| RF-02 | Registro de beneficiarios | El sistema debe identificar a la persona por su DNI y distinguirla de sus inscripciones. Debe impedir una inscripción activa duplicada de la misma persona en el mismo programa y comité. Si existe una inscripción activa en otro comité del mismo programa, debe generar una alerta y derivar el caso a la Sub Gerencia para revisión antes de confirmar la nueva inscripción. La existencia del DNI en otro programa no se considerará por sí sola una duplicidad: se validarán las reglas de elegibilidad de cada programa. |
| RF-03 | Gestión de inscripciones | El sistema debe permitir registrar una novedad que pueda afectar la elegibilidad y gestionar el estado de una inscripción específica, conservando la identidad de la persona y sus otras inscripciones. La baja o no revalidación debe seguir el procedimiento aprobado y quedar auditada; no se debe desactivar automáticamente a la persona en todos los programas. |
| RF-04 | Detección y resolución de duplicados | El sistema debe generar una alerta cuando una inscripción activa en otro comité del mismo programa pueda representar una duplicidad. Solo personal autorizado de la Sub Gerencia podrá resolverla, registrando uno de estos resultados: no es duplicado, duplicado confirmado, requiere información adicional o derivado para decisión superior. Mientras esté pendiente, no se confirmará la nueva inscripción en conflicto. |
| RF-05 | Auditoría del padrón y duplicados | El sistema debe mantener un historial de altas, modificaciones, bajas lógicas, decisiones sobre alertas, revalidaciones, importaciones, exportaciones y cambios de permisos, registrando usuario, fecha/hora del servidor, operación, registro afectado, resultado y motivo cuando corresponda. Los eventos no podrán editarse ni borrarse por usuarios ordinarios; las correcciones se registrarán como nuevos eventos vinculados al anterior. |
| RF-06 | Revalidación periódica | El sistema debe generar una tarea de revalidación cada 6 meses por inscripción, notificar al comité asignado y registrar estados, responsables, fechas, motivos y resultados. Los estados propuestos son pendiente, en verificación, requiere subsanación, derivada a Sub Gerencia, revalidada y no revalidada por decisión autorizada. La falta de respuesta o el vencimiento no causan una baja automática: la tarea permanece pendiente y se recuerda o escala según plazos aprobados por la Municipalidad. |
| RF-07 | Notificaciones | El sistema debe crear avisos de aprobación y revalidación cuando exista un contacto válido y el canal esté habilitado. Debe registrar canal, fecha/hora, estado del proveedor y su identificador de mensaje cuando exista; distinguir enviado de entregado; reintentar fallos temporales de forma limitada y marcar fallos permanentes para revisión. El mensaje no debe exponer datos personales innecesarios y el fallo del proveedor no debe revertir una inscripción confirmada. |
| RF-08 | Cobertura y reportes | El sistema debe mostrar un mapa con beneficiarios activos por zona o distrito, identificando la fuente y fecha de corte de la delimitación territorial utilizada. |
| RF-09 | Cobertura y reportes | El sistema debe generar reportes que presenten por separado los beneficiarios activos y la población de referencia por zona. Solo calculará porcentajes cuando la unidad territorial, el periodo y la definición del denominador sean comparables y estén aprobados; mostrará fuente, año de referencia y fecha de corte. |
| RF-10 | Exportación segura de padrones y reportes | El sistema debe permitir exportar reportes autorizados en Excel/PDF, validar permisos en el servidor según rol y ámbito de comité, limitar los campos al mínimo necesario y registrar usuario, fecha/hora, formato, filtros o ámbito, cantidad de registros y resultado. El DNI completo no se incluirá por defecto en reportes generales; exportaciones completas o excepcionales requerirán permiso específico. |
| RF-11 | Importación y depuración (Staging) | El sistema debe importar formatos autorizados Excel/CSV a un lote temporal sin modificar directamente el padrón definitivo; validar estructura, campos y límites; identificar errores, incompletos y posibles duplicados; y gestionar estados explícitos del lote. Solo un usuario autorizado puede aprobar y consolidar. La consolidación debe ser transaccional e idempotente, auditable y acompañada de un resumen de filas aceptadas, rechazadas y pendientes. Los reportes de error deben neutralizar valores que puedan interpretarse como fórmulas. |

### Criterio de identificación y detección de duplicados

El DNI identifica a la persona, no a una inscripción única para todos los programas. Una persona puede tener inscripciones en programas distintos si cumple las reglas de elegibilidad correspondientes. Para evitar doble asignación dentro de un mismo programa, una inscripción activa existente en otro comité debe generar una alerta y quedar sujeta a revisión de la Sub Gerencia; no se debe crear ni rechazar automáticamente esa nueva inscripción sin aplicar la decisión de revisión. La inscripción repetida en el mismo programa y comité debe bloquearse como duplicado.

## Requisitos adicionales propuestos por el equipo

| ID | Módulo | Requisito funcional |
|----|--------|---------------------|
| RF-12 | Autenticación | El sistema debe permitir iniciar sesión a dirigentes y personal municipal mediante credenciales, asignando permisos según su rol. |
| RF-13 | Autenticación y administración de usuarios | El sistema debe permitir únicamente al personal municipal expresamente autorizado administrar cuentas, activar/desactivar usuarios, asignar roles y vincular o reasignar dirigentes a comités. Cada cambio deberá registrar responsable, fecha/hora y detalle en la auditoría. |
| RF-14 | Registro y sincronización offline | El sistema debe permitir guardar registros pendientes en el dispositivo del dirigente cuando no haya conexión y sincronizarlos al recuperarla. Cada operación tendrá un identificador único para que los reintentos sean idempotentes. El servidor validará las reglas de negocio y los DNI antes de confirmar el alta; los duplicados o conflictos se marcarán para revisión sin sobrescribir datos existentes. La interfaz mostrará el estado de cada registro (pendiente, sincronizando, sincronizado o requiere revisión/error) y permitirá reintentar fallos temporales. |

## Relación entre historias de usuario y requisitos funcionales

| Historia de usuario | Requisitos funcionales relacionados |
|---------------------|-------------------------------------|
| HU01 Registrar beneficiarios | RF-01 |
| HU02 Validar duplicados al registrar | RF-02 |
| HU03 Inactivar beneficiarios | RF-03, RF-05 |
| HU04 Gestionar alertas de duplicados | RF-04, RF-05 |
| HU05 Revalidar periódicamente | RF-06 |
| HU06 Notificar al beneficiario | RF-07 |
| HU07 Consultar mapa de cobertura | RF-08 |
| HU08 Generar reportes y exportar | RF-09, RF-10 |
| HU09 Importar y depurar padrones | RF-11 |
| HU10 Iniciar sesión según rol | RF-12, RF-13 |
| HU11 Registrar sin conexión | RF-14 |


## Flujo de revalidación de inscripciones

La revalidación se aplica a cada inscripción por programa y no a la persona de forma global. El sistema crea una tarea semestral, notifica al dirigente asignado y registra la verificación. Los casos dudosos se derivan a la Sub Gerencia. Debe registrarse el resultado, la fecha, el responsable y el motivo. Si no hay respuesta, la inscripción queda pendiente y se recuerda o escala conforme a los plazos que apruebe la Municipalidad; no se da de baja automáticamente. La no revalidación requiere decisión de personal autorizado conforme al procedimiento municipal y no afecta automáticamente las inscripciones en otros programas. Los estados, evidencias y plazos deben validarse con el área usuaria.



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
