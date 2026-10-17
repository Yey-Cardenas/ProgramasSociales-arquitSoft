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
| **RNF-04** | Seguridad y privacidad | Las comunicaciones entre navegador y servidor usarán HTTPS. El acceso a funciones y datos estará restringido por roles autorizados. Las copias de seguridad y cualquier dato personal persistido localmente deberán contar con medidas de protección apropiadas; la PWA evitará almacenar innecesariamente datos personales sensibles. | Revisar configuración, autorización por rol, almacenamiento local y respaldos; ejecutar pruebas de acceso no autorizado. | AC04 |
| **RNF-05** | Compatibilidad | La interfaz web deberá probarse en navegadores compatibles con la PWA en Android 8 o superior y en un navegador de escritorio soportado. Las funciones offline solo se darán por cumplidas en los navegadores/dispositivos incluidos en la matriz de pruebas. | Mantener una matriz con dispositivo, versión de sistema operativo, navegador y resultado de las pruebas principales y offline. | AC05 |
| **RNF-06** | Auditabilidad | Cada alta, modificación, baja lógica o decisión de revisión que afecte al padrón deberá registrar como mínimo usuario responsable, fecha y hora, operación y motivo. El historial estará protegido contra modificación por usuarios ordinarios y será consultable solo por roles autorizados. | Ejecutar operaciones de prueba y comprobar la integridad y permisos de consulta del historial. | AC06 |
| **RNF-07** | Capacidad y escalabilidad | La solución deberá procesar un padrón de al menos **15 000 beneficiarios** sin superar los umbrales de rendimiento acordados. Como umbral inicial por validar, se propone que el percentil 95 de las consultas habituales del padrón no supere **2 segundos** con 20 usuarios concurrentes, excluyendo exportaciones y generación de mapas pesados. | Prueba de carga con volumen y concurrencia documentados; registrar tiempos, errores y recursos utilizados. | AC07 |
| **RNF-08** | Mantenibilidad y extensibilidad | Incorporar un nuevo programa social debe poder realizarse mediante un módulo cohesionado y reglas/configuración específicas, sin duplicar los módulos transversales de autenticación, auditoría y acceso a datos ni alterar los programas existentes. La necesidad de cambios en componentes compartidos deberá documentarse en la revisión de diseño. | Revisión arquitectónica y ejercicio de diseño para un programa adicional, con análisis de impacto documentado. | AC08 |
| **RNF-09** | Respaldo y recuperación | La base de datos deberá respaldarse al menos una vez al día. Como propuesta inicial por validar, conservar los respaldos diarios durante 30 días, almacenarlos en una ubicación con acceso restringido y realizar una prueba de restauración al menos trimestralmente. La persona responsable y los objetivos de recuperación deberán acordarse con la Municipalidad. | Revisar bitácoras de respaldo, permisos, retención y resultados de restauración. | AC09 |

## Decisiones pendientes de validación

Antes de aprobar estos requisitos, el equipo debe confirmar con la Municipalidad:

1. Si el objetivo de disponibilidad del 99 % se medirá mensualmente y qué mantenimiento se excluirá.
2. El entorno de prueba y la concurrencia realista para RNF-02 y RNF-07.
3. Si cinco campos obligatorios son suficientes para el registro sin omitir información exigida por cada programa.
4. Qué medidas de protección en reposo y en dispositivos son viables para los datos personales, además de HTTPS y el control de acceso.
5. La matriz oficial de dispositivos y navegadores soportados.
6. Los plazos de conservación de auditoría y respaldos, el responsable operativo y los objetivos de recuperación.

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
