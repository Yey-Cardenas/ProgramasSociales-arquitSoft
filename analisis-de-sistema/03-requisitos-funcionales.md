# Requisitos funcionales

## Requisitos de la propuesta

| ID | Módulo | Requisito funcional |
|----|--------|---------------------|
| RF-01 | Registro de beneficiarios | El sistema debe permitir a los dirigentes de comité registrar beneficiarios usando el DNI como dato obligatorio. |
| RF-02 | Registro de beneficiarios | El sistema debe identificar a la persona por su DNI y distinguirla de sus inscripciones. Debe impedir una inscripción activa duplicada de la misma persona en el mismo programa y comité. Si existe una inscripción activa en otro comité del mismo programa, debe generar una alerta y derivar el caso a la Sub Gerencia para revisión antes de confirmar la nueva inscripción. La existencia del DNI en otro programa no se considerará por sí sola una duplicidad: se validarán las reglas de elegibilidad de cada programa. |
| RF-03 | Gestión de inscripciones | El sistema debe permitir registrar una novedad que pueda afectar la elegibilidad y gestionar el estado de una inscripción específica, conservando la identidad de la persona y sus otras inscripciones. La baja o no revalidación debe seguir el procedimiento aprobado y quedar auditada; no se debe desactivar automáticamente a la persona en todos los programas. |
| RF-04 | Detección y resolución de duplicados | El sistema debe generar una alerta cuando una inscripción activa en otro comité del mismo programa pueda representar una duplicidad. Solo personal autorizado de la Sub Gerencia podrá resolverla, registrando uno de estos resultados: no es duplicado, duplicado confirmado, requiere información adicional o derivado para decisión superior. Mientras esté pendiente, no se confirmará la nueva inscripción en conflicto. |
| RF-05 | Auditoría del padrón y duplicados | El sistema debe mantener un historial de altas, modificaciones, bajas lógicas y decisiones sobre alertas, registrando identificador de alerta, inscripciones y comités implicados, resultado, motivo/evidencia, usuario responsable y fecha/hora. Las correcciones posteriores se registrarán como nuevas operaciones, sin borrar la decisión anterior. |
| RF-06 | Revalidación periódica | El sistema debe generar una tarea de revalidación cada 6 meses por inscripción, notificar al comité asignado y registrar estados y resultados. La falta de respuesta o el vencimiento no deben causar una baja automática; los casos pendientes se recordarán o escalarán según plazos aprobados por la Municipalidad. |
| RF-07 | Notificaciones | El sistema debe enviar una notificación por SMS/WhatsApp cuando una inscripción sea aprobada o requiera revalidación, si existe un contacto válido y el canal está disponible, y registrar el resultado del envío sin incluir datos personales innecesarios en el mensaje. |
| RF-08 | Cobertura y reportes | El sistema debe mostrar un mapa con la cantidad de beneficiarios activos por zona o distrito. |
| RF-09 | Cobertura y reportes | El sistema debe generar reportes de cobertura, comparando beneficiarios registrados frente a la población estimada por zona. |
| RF-10 | Cobertura y reportes | El sistema debe permitir exportar el padrón depurado en Excel/PDF. |
| RF-11 | Depuración de datos (Staging) | El sistema debe contar con un módulo temporal de importación para validar, limpiar y estructurar los datos históricos de Excel antes de consolidarlos en la base de datos principal. |

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

