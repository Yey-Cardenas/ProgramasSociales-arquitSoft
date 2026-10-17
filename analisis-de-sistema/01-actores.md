# Actores del sistema

## Contexto

La Municipalidad de Huamanga administra el Programa del Vaso de Leche y los Comedores Populares mediante comités independientes por barrio o zona, cada uno con su propio padrón en listas físicas u hojas de cálculo. Esto genera duplicidad de beneficiarios, personas que ya no califican, fallecidos que siguen en las listas y ninguna visión consolidada de la cobertura.

El **Sistema de Gestión de Programas Sociales** reemplaza esos padrones dispersos por un padrón único y digital. Permite detectar duplicados por DNI entre comités, revalidar periódicamente a los beneficiarios y mostrar la cobertura real por zona. De acuerdo con el diagrama de estilo arquitectónico, los usuarios que interactúan directamente con la aplicación son los **dirigentes de comité** y el **personal municipal de la Sub Gerencia de Programas Sociales**.

## Actores humanos

| Actor | Rol y responsabilidades en el sistema |
|-------|----------------------------------------|
| **Dirigente de comité** | Registrar y actualizar beneficiarios del comité que tiene asignado, iniciar la revisión de alertas de duplicidad y apoyar la revalidación. No podrá consultar ni modificar los padrones detallados de otros comités. Cuando la PWA y la conectividad lo permitan, podrá trabajar con las capacidades offline definidas para la aplicación. |
| **Personal municipal autorizado de la Sub Gerencia de Programas Sociales** | Supervisar el padrón consolidado dentro de su ámbito autorizado, revisar y resolver alertas de duplicados conforme al procedimiento y resultados definidos, consultar cobertura, generar reportes y exportar padrones según sus permisos. Solo el personal expresamente designado podrá administrar usuarios, roles y asignaciones de comité. |
| **Beneficiario (destinatario de notificaciones)** | Recibir avisos sobre la aprobación de su inscripción y la necesidad de revalidar sus datos. En esta versión se considera destinatario de notificaciones, no un usuario que interactúa directamente con las vistas de la aplicación. |

Los dos primeros actores corresponden a los **“dirigentes y personal municipal”** representados como usuarios en el diagrama de estilo arquitectónico. El beneficiario se mantiene identificado por su participación en el proceso de notificaciones, pero no se representa como usuario directo de la interfaz en dicho diagrama.

## Sistemas externos

| Sistema externo | Interacción con el sistema |
|-----------------|----------------------------|
| **Gateway SMS / WhatsApp** | Servicio externo al que la aplicación envía solicitudes de notificación para que los avisos lleguen a los beneficiarios. La integración se encapsula en servicios de la aplicación, sin conexión directa desde las vistas. |
| **Padrón actual de cada comité (Excel / papel)** | Fuente de datos históricos para la carga inicial y el proceso de migración mediante el módulo de staging. Los documentos en papel requerirán digitalización antes de su procesamiento. |

## Actores y sistemas fuera del alcance de esta versión

RENIEC y SISFOH no se consideran actores ni integraciones disponibles en esta primera versión. La validación del DNI se limita a cruzar los datos registrados en el propio sistema municipal. Una posible integración con dichos sistemas queda planteada para una fase futura.



## Procedimiento para resolver alertas de posible duplicado

La decisión final corresponde al personal de la Sub Gerencia expresamente autorizado para revisar padrones; el dirigente puede aportar información, pero no resolver por sí solo una alerta que involucre a otro comité. Cada alerta tendrá uno de estos resultados propuestos:

- **No es duplicado:** se documenta la verificación y se permite continuar el trámite de la nueva inscripción, si cumple los demás requisitos.
- **Duplicado confirmado:** no se confirma la nueva inscripción duplicada en el mismo programa. El revisor registra la inscripción existente y la justificación; cualquier corrección o baja de una inscripción previa debe seguir el procedimiento municipal y no se ejecuta automáticamente.
- **Requiere información adicional:** el caso queda pendiente, se registra qué información falta y se asigna seguimiento.
- **Derivado para decisión superior:** se remite a la persona responsable que designe la Municipalidad cuando el caso no pueda resolverse con los criterios ordinarios.

La resolución debe registrar como mínimo: identificador de alerta, persona/inscripciones implicadas, programa y comités involucrados, resultado, motivo o evidencia revisada, usuario que decide, fecha/hora y cualquier cambio posterior realizado. El historial no debe borrarse ni sobrescribirse; una corrección posterior se registra como una nueva operación auditada. Mientras la alerta esté pendiente, no se confirma la nueva inscripción en conflicto. La coincidencia de DNI por sí sola no autoriza a eliminar registros ni a desactivar a la persona en otros programas.

Estos resultados y la autoridad responsable son una propuesta de diseño que la Municipalidad debe aprobar antes de implementar.

