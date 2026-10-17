# Historias de usuario

Formato: **Como [actor], quiero [acción], para [beneficio].**

## Historias de la propuesta

| ID | Historia de usuario |
|----|---------------------|
| HU01 | Como dirigente de comité, quiero registrar beneficiarios con su DNI, para mantener actualizado el padrón de mi comité. |
| HU02 | Como dirigente de comité, quiero que el sistema detecte inscripciones existentes por DNI y me avise cuando una inscripción en otro comité del mismo programa requiera revisión, para evitar duplicados sin impedir automáticamente una inscripción válida en otro programa. |
| HU03 | Como dirigente de comité, quiero registrar una novedad que pueda afectar una inscripción y derivarla según el procedimiento establecido, para mantener actualizado el padrón sin afectar automáticamente las inscripciones de la persona en otros programas. |
| HU04 | Como personal autorizado de la Sub Gerencia, quiero resolver alertas de posible duplicado con un resultado y motivo auditables, para evitar altas duplicadas sin eliminar registros válidos por error. |
| HU05 | Como personal de la Sub Gerencia, quiero gestionar la revalidación semestral de cada inscripción, registrar el resultado y derivar los casos dudosos, para revisar la continuidad del apoyo sin dar de baja automáticamente por falta de respuesta. |
| HU06 | Como beneficiario, quiero recibir avisos por un canal válido cuando mi inscripción sea aprobada o deba revalidar mis datos, y que el sistema registre si el aviso fue entregado o falló, para estar informado sin exponer innecesariamente mis datos personales. |
| HU07 | Como personal de la Sub Gerencia, quiero ver un mapa con los beneficiarios activos por zona, para identificar barrios sobre-atendidos o desatendidos. |
| HU08 | Como personal autorizado, quiero generar reportes y exportar únicamente los datos que puedo consultar según mi rol y ámbito, para sustentar decisiones sin exponer información personal innecesaria. |
| HU09 | Como personal autorizado de la Sub Gerencia, quiero importar padrones históricos a un lote temporal, revisar errores y duplicados y aprobar su consolidación, para incorporar únicamente datos validados y poder auditar el resultado. |

## Historias adicionales propuestas por el equipo

| ID | Historia de usuario | Actor | Justificación |
|----|---------------------|-------|---------------|
| HU10 | Como dirigente de comité o personal autorizado de la Sub Gerencia, quiero iniciar sesión con permisos según mi rol y ámbito asignado, para acceder solo a la información que me corresponde. | Dirigente / Personal de la Sub Gerencia | La propuesta incluye un módulo de Autenticación y exige accesos restringidos por rol para proteger datos sensibles. |
| HU11 | Como dirigente de comité, quiero registrar beneficiarios sin conexión y consultar el estado de su sincronización, para trabajar en zonas con baja cobertura sin perder información ni generar altas duplicadas. | Dirigente de Comité | La PWA debe conservar los registros pendientes, sincronizarlos al recuperar la conexión, mostrar fallos o conflictos para revisión y permitir reintentos sin duplicar operaciones. |

## Criterios de aceptación de autorización

- Un dirigente inicia sesión y solo puede ver o modificar los registros del comité que tiene asignado.
- Si un dirigente intenta abrir directamente una ruta o enviar una solicitud para acceder a otro comité, el servidor la rechaza aunque la interfaz oculte esos datos.
- El personal autorizado de la Sub Gerencia puede consultar información consolidada y resolver alertas dentro del ámbito municipal que le corresponda.
- Solo el personal designado puede crear, desactivar o reasignar usuarios, roles y comités.
- Las decisiones sobre posibles duplicados entre comités registran usuario responsable, fecha y hora, resultado y motivo.
- Las operaciones pendientes creadas offline vuelven a validarse contra los permisos vigentes al sincronizarse; no se aceptan automáticamente si la cuenta fue desactivada o perdió acceso al comité.

Los criterios deberán validarse con la Municipalidad y convertirse en pruebas de aceptación durante la implementación.


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



## Política de exportación segura de padrones y reportes

- **Autorización por ámbito:** cada solicitud de exportación debe validar permisos en el servidor. El dirigente solo podrá exportar información de su comité asignado; el personal de la Sub Gerencia solo podrá exportar dentro del ámbito municipal autorizado. El permiso para consultar datos no concede automáticamente permiso para exportar el padrón completo.
- **Finalidad y campos mínimos:** ofrecer reportes agregados cuando sean suficientes. Incluir datos personales solo cuando sean necesarios para la finalidad autorizada. El DNI completo no debe incluirse por defecto en reportes generales; si resulta imprescindible, debe existir un permiso específico y una justificación.
- **Auditoría:** registrar usuario, fecha/hora, tipo de exportación, formato, filtros o ámbito solicitado, cantidad de registros y resultado de la operación. No guardar el contenido del archivo, DNI completos ni otros datos sensibles en los logs.
- **Descarga protegida:** generar el archivo únicamente después de autorizar la solicitud; informar que contiene datos personales cuando corresponda y evitar que las URL temporales de descarga queden reutilizables o expuestas en registros. No generar archivos para solicitudes denegadas.
- **Uso y conservación:** indicar al usuario que debe almacenar y compartir el archivo solo por canales autorizados y eliminar copias cuando dejen de ser necesarias, de acuerdo con la política municipal. El acceso a exportaciones completas debe limitarse y revisarse periódicamente.
- **Excepciones:** cualquier exportación fuera del alcance habitual debe ser aprobada por la persona que la Municipalidad designe y quedar auditada.

Los permisos exactos, campos exportables, retención de archivos y responsables de autorizar excepciones deben aprobarse con la Municipalidad. Estas reglas describen el comportamiento esperado y están pendientes de implementación y pruebas.



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
