# Requisitos funcionales

## Requisitos de la propuesta

| ID | Módulo | Requisito funcional |
|----|--------|---------------------|
| RF-01 | Registro de beneficiarios | El sistema debe permitir a los dirigentes de comité registrar beneficiarios usando el DNI como dato obligatorio. |
| RF-02 | Registro de beneficiarios | El sistema debe identificar a la persona por su DNI y distinguirla de sus inscripciones. Debe impedir una inscripción activa duplicada de la misma persona en el mismo programa y comité. Si existe una inscripción activa en otro comité del mismo programa, debe generar una alerta y derivar el caso a la Sub Gerencia para revisión antes de confirmar la nueva inscripción. La existencia del DNI en otro programa no se considerará por sí sola una duplicidad: se validarán las reglas de elegibilidad de cada programa. |
| RF-03 | Registro de beneficiarios | El sistema debe permitir marcar a un beneficiario como inactivo (fallecimiento, cambio de condición o inasistencia prolongada). |
| RF-04 | Detección de duplicados | El sistema debe alertar al personal de la Sub Gerencia cuando se detecte un posible duplicado, para su evaluación. |
| RF-05 | Detección de duplicados | El sistema debe mantener un historial de cambios del padrón (altas, bajas, modificaciones) con fecha y usuario responsable. |
| RF-06 | Revalidación periódica | El sistema debe solicitar la revalidación de cada beneficiario cada 6 meses, notificando al comité cuando corresponda. |
| RF-07 | Revalidación periódica | El sistema debe enviar una notificación por SMS/WhatsApp al beneficiario cuando su inscripción sea aprobada o deba revalidar sus datos. |
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
