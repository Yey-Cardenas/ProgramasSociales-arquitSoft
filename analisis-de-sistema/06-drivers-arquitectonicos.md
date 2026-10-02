@'
# Drivers arquitectónicos

## Drivers identificados

| ID | Driver arquitectónico | Origen | ¿Por qué influye en la arquitectura? |
|----|-----------------------|--------|--------------------------------------|
| DA01 | El sistema debe permitir registrar beneficiarios sin conexión y sincronizar después. | AC01 – Disponibilidad; RC02 – PWA | Define el uso de Service Worker, almacenamiento local en el cliente y una estrategia de sincronización asíncrona. |
| DA02 | La validación de duplicados por DNI debe responder en menos de 0.5 segundos. | AC02 – Rendimiento; RF-02 | Condiciona el modelo de datos (clave única por DNI), la base de datos relacional y dónde se ejecuta la validación. |
| DA03 | El sistema debe proteger los datos personales y las copias de seguridad. | AC04 – Seguridad; AC09 – Respaldo | Influye en la autenticación (JWT), la autorización por rol, el cifrado y el aislamiento del servidor de base de datos. |
| DA04 | Debe poder incorporar nuevos programas sociales sin rediseñar el sistema. | AC08 – Mantenibilidad | Exige módulos cohesionados y de bajo acoplamiento, con límites claros dentro del monolito. |
| DA05 | La solución debe ser de bajo costo y baja complejidad operativa. | RC01 – Monolito Modular; AC07 – Escalabilidad | Determina un solo proceso de backend y dos servidores en lugar de servicios distribuidos. |
| DA06 | El sistema debe notificar mediante un gateway SMS/WhatsApp externo. | RC06 – Gateway; RF-07 | Condiciona la integración con un servicio externo; conviene un módulo de notificaciones con cola que aísle sus fallos. |
| DA07 | Los padrones históricos en Excel/papel deben depurarse antes de consolidarse. | RF-11 – Staging | Obliga a un módulo de staging separado de las tablas de producción. |
| DA08 | La cobertura debe visualizarse geográficamente por zona. | RF-08, RF-09; RC05 – PostGIS | Condiciona la elección de PostgreSQL con PostGIS y el modelado de zonas. |

## Evaluación de otros elementos

| Elemento | ¿Puede ser driver? | Justificación |
|----------|--------------------|---------------|
| RNF-03 (Usabilidad: máximo 5 campos) | No | Afecta el diseño de la interfaz y no la estructura del sistema ni la organización de sus módulos. |
| RNF-06 (Auditabilidad) | Sí | Exige registrar usuario, fecha y motivo de cada cambio. Eso afecta el modelo de datos y atraviesa todos los módulos que modifican el padrón. |
| RF-10 (Exportar padrón en Excel/PDF) | No | Es una funcionalidad que se resuelve dentro del módulo de Cobertura y Reportes, sin decisiones estructurales importantes. |
| RC10 (Git y GitHub) | No | Es una restricción del proceso de desarrollo; no limita la estructura ni el funcionamiento del sistema. |
'@ | Set-Content analisis-de-sistema/06-drivers-arquitectonicos.md -Encoding utf8