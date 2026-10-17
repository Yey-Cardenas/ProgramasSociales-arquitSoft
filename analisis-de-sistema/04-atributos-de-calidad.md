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
| AC09 | **Respaldo de información** | Azure SQL Database tendrá respaldos diarios. Se propone inicialmente una retención de 30 días y pruebas trimestrales de restauración, pendientes de validación operativa. | RNF-09 |

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
