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
| HU06 | Como beneficiario, quiero recibir un SMS/WhatsApp cuando mi inscripción sea aprobada o deba revalidar mis datos, para estar informado de mi situación. |
| HU07 | Como personal de la Sub Gerencia, quiero ver un mapa con los beneficiarios activos por zona, para identificar barrios sobre-atendidos o desatendidos. |
| HU08 | Como personal de la Sub Gerencia, quiero generar reportes de cobertura y exportar el padrón depurado en Excel/PDF, para sustentar la distribución de recursos entre comités. |
| HU09 | Como personal de la Sub Gerencia, quiero importar y depurar los padrones históricos en Excel, para consolidarlos en la base de datos principal sin arrastrar errores. |

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

