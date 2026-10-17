# Modelo C4 y vista de despliegue

**Proyecto:** Sistema de Gestión de Programas Sociales – Huamanga  
**Stack objetivo:** .NET 10 LTS, ASP.NET Core 10 MVC, Entity Framework Core 10 y Azure SQL Database.  
**Estado:** propuestas de diseño; no equivalen a una aplicación implementada ni a una infraestructura desplegada.

## Diagramas Markdown con Mermaid

- [Nivel 1 — Contexto del sistema](C4-Nivel-1-Contexto.md)
- [Nivel 2 — Contenedores](C4-Nivel-2-Contenedores.md)
- [Nivel 3 — Componentes](C4-Nivel-3-Componentes.md)
- [Vista de despliegue propuesta](C4-Despliegue-Fisico.md)

Cada archivo contiene un bloque Mermaid `flowchart`. GitHub puede renderizar los diagramas Mermaid directamente dentro de los archivos Markdown, lo que permite visualizarlos y editarlos como texto.

## Criterios de interpretación

- Los usuarios directos son los dirigentes de comité y el personal municipal autorizado.
- Los beneficiarios son destinatarios de notificaciones en la primera versión; no se modelan como usuarios que inician sesión.
- El dirigente solo opera sobre su comité asignado. Las operaciones de supervisión, resolución de alertas y administración de cuentas se restringen al personal expresamente autorizado.
- La aplicación se plantea como monolito modular MVC; la PWA y el almacenamiento offline son capacidades de la misma aplicación web, no un frontend independiente.
- Azure App Service y Azure SQL Database son una propuesta de despliegue. Se prefiere conectividad privada y no se permite acceso público irrestricto a la base de datos.
- RENIEC/SISFOH aparecen solo como posible integración futura. El gateway SMS/WhatsApp y los padrones históricos son dependencias externas o fuentes de datos.

## Nota sobre diagramas existentes

Para respetar la instrucción de no modificar diagramas existentes, los diagramas que ya estaban en los documentos de arquitectura se han conservado sin cambios. Estos archivos Markdown son vistas nuevas y complementarias. Los archivos PlantUML anteriores se mantienen como respaldo y no se consideran el formato principal para editar los diagramas C4.
