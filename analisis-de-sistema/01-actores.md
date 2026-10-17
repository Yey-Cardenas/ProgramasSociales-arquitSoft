@'
# Actores del sistema

## Contexto

La Municipalidad de Huamanga administra el Programa del Vaso de Leche y los Comedores Populares mediante comités independientes por barrio o zona, cada uno con su propio padrón en listas físicas u hojas de cálculo. Esto genera duplicidad de beneficiarios, personas que ya no califican, fallecidos que siguen en las listas y ninguna visión consolidada de la cobertura.

El **Sistema de Gestión de Programas Sociales** reemplaza esos padrones dispersos por un padrón único y digital. Permite detectar duplicados por DNI entre comités, revalidar periódicamente a los beneficiarios y mostrar la cobertura real por zona. Lo usan los dirigentes de comité y el personal de la Sub Gerencia de Programas Sociales, para que el presupuesto social llegue a quien realmente lo necesita.

## Actores humanos

| Actor | ¿Qué necesita realizar? |
|-------|-------------------------|
| **Dirigente de Comité** | Registrar beneficiarios con su DNI, validar si ya están inscritos en otro comité, marcar beneficiarios como inactivos, actualizar el padrón y revalidar datos, incluso sin conexión. |
| **Personal de la Sub Gerencia de Programas Sociales** | Supervisar el padrón, revisar alertas de duplicados, consultar el mapa de cobertura, generar reportes y exportar el padrón depurado. |
| **Beneficiario** | Recibir notificaciones sobre la aprobación de su inscripción y sobre la necesidad de revalidar sus datos. |

## Sistemas externos

| Sistema externo | ¿Qué necesita realizar? |
|-----------------|-------------------------|
| **Gateway SMS / WhatsApp** | Entregar a los beneficiarios las notificaciones enviadas por el sistema. |
| **Padrón actual de cada comité (Excel / papel)** | Proporcionar los datos históricos que alimentan el módulo de staging en la migración inicial. |

## Actores que no se consideran en esta versión

RENIEC y SISFOH no se consideran actores en esta primera versión: la validación del DNI se limita a cruzar los datos ya registrados en el propio sistema municipal. Su integración queda planteada como una segunda fase del proyecto.
'@ | Set-Content analisis-de-sistema/01-actores.md -Encoding utf8