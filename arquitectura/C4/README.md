# Modelo C4 y vista de despliegue

**Proyecto:** Sistema de Gestión de Programas Sociales – Huamanga  
**Stack objetivo documentado:** .NET 10 LTS, ASP.NET Core 10 MVC, Entity Framework Core 10 y Azure SQL Database.  
**Estado:** modelos de diseño propuestos; no equivalen a una aplicación implementada ni a una infraestructura desplegada.

## Archivos

- [Nivel 1 — Contexto del sistema](C4-Nivel-1-Contexto.puml)
- [Nivel 2 — Contenedores](C4-Nivel-2-Contenedores.puml)
- [Nivel 3 — Componentes](C4-Nivel-3-Componentes.puml)
- [Vista de despliegue propuesta](C4-Despliegue-Fisico.puml)

Los archivos están escritos en PlantUML y usan la biblioteca C4-PlantUML. Para renderizarlos, abre cada archivo con una herramienta compatible con PlantUML y habilita la carga de la biblioteca externa, o configura localmente una copia aprobada de C4-PlantUML.

## Criterios de interpretación

- Los usuarios directos son los dirigentes de comité y el personal municipal autorizado.
- Los beneficiarios son destinatarios de notificaciones en la primera versión; no se modelan como usuarios que inician sesión.
- El dirigente solo opera sobre su comité asignado. Las operaciones de supervisión, resolución de alertas y administración de cuentas se restringen al personal expresamente autorizado.
- La aplicación se plantea como monolito modular MVC; la PWA y el almacenamiento offline son capacidades de la misma aplicación web, no un frontend independiente.
- Azure App Service y Azure SQL Database son una propuesta de despliegue. Se prefiere conectividad privada y no se permite acceso público irrestricto a la base de datos.
- RENIEC/SISFOH aparecen solo como posible integración futura. El gateway SMS/WhatsApp y los padrones históricos son dependencias externas o fuentes de datos.

## Nota sobre los diagramas anteriores

Para respetar la instrucción de no modificar diagramas existentes, los diagramas que ya estaban en los documentos de arquitectura se han conservado sin cambios. Estos nuevos archivos C4 se agregan como vistas complementarias. Si un diagrama anterior conserva una etiqueta tecnológica más antigua, este índice y el texto vigente de la documentación establecen el stack objetivo recomendado; esa etiqueta no significa que se haya ejecutado una migración ni que exista una implementación comprobada.
