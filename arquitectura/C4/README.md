# Modelo C4 y vista de despliegue

**Proyecto:** Sistema de Gestión de Programas Sociales – Huamanga  
**Stack objetivo:** .NET 10 LTS, ASP.NET Core 10 MVC, Entity Framework Core 10 y Azure SQL Database.  
**Estado:** propuestas de diseño; no equivalen a una aplicación implementada ni a una infraestructura desplegada.

## Diagramas Markdown con Mermaid

- [Nivel 1 — Contexto del sistema](C4-Nivel-1-Contexto.md)
- [Nivel 2 — Contenedores](C4-Nivel-2-Contenedores.md)
- [Nivel 3 — Componentes](C4-Nivel-3-Componentes.md)
- [Nivel 4 — Código (vista complementaria)](C4-Nivel-4-Codigo.md)
- [Vista de despliegue propuesta](C4-Despliegue-Fisico.md)

Los niveles 1, 2 y 3 describen contexto, contenedores y componentes. El nivel 4 es una vista de código complementaria y selectiva: no es obligatorio en todas las presentaciones C4 y se centra en clases y un flujo representativo. La vista de despliegue es una perspectiva física adicional.

Los diagramas están escritos en Markdown con Mermaid. GitHub puede renderizar los bloques Mermaid directamente dentro de los archivos, lo que permite visualizarlos y editarlos como texto. Los diagramas preexistentes en otros documentos del repositorio se conservan sin cambios.


## Diagramas visuales editables estilo Draw.io

Se agregan versiones gráficas complementarias con un estilo visual similar al de la documentación de referencia. Los archivos `.svg` se pueden visualizar directamente desde GitHub y los archivos `.drawio` se pueden abrir y editar en [diagrams.net](https://app.diagrams.net/).

| Vista | Diagrama visual | Fuente editable |
|---|---|---|
| Nivel 1 — Contexto | [Ver SVG](visual/C4-Nivel-1-Contexto.svg) | [Editar Draw.io](visual/C4-Nivel-1-Contexto.drawio) |
| Nivel 2 — Contenedores | [Ver SVG](visual/C4-Nivel-2-Contenedores.svg) | [Editar Draw.io](visual/C4-Nivel-2-Contenedores.drawio) |
| Nivel 3 — Componentes | [Ver SVG](visual/C4-Nivel-3-Componentes.svg) | [Editar Draw.io](visual/C4-Nivel-3-Componentes.drawio) |
| Nivel 4 — Código complementario | [Ver SVG](visual/C4-Nivel-4-Codigo.svg) | [Editar Draw.io](visual/C4-Nivel-4-Codigo.drawio) |
| Despliegue físico | [Ver SVG](visual/C4-Despliegue-Fisico.svg) | [Editar Draw.io](visual/C4-Despliegue-Fisico.drawio) |

Estas versiones visuales se agregan como archivos nuevos. No se sustituyen ni se editan los bloques Mermaid existentes; así se conserva la versión original y se ofrece una alternativa gráfica editable.

## Criterios de interpretación

- Los usuarios directos son los dirigentes de comité y el personal municipal autorizado.
- Los beneficiarios son destinatarios de notificaciones en la primera versión; no se modelan como usuarios que inician sesión.
- El dirigente solo opera sobre su comité asignado. Las operaciones de supervisión, resolución de alertas y administración de cuentas se restringen al personal expresamente autorizado.
- La aplicación se plantea como monolito modular MVC; la PWA y el almacenamiento offline son capacidades de la misma aplicación web, no un frontend independiente.
- Azure App Service y Azure SQL Database son una propuesta de despliegue. Se prefiere conectividad privada y no se permite acceso público irrestricto a la base de datos.
- RENIEC/SISFOH aparecen solo como posible integración futura. El gateway SMS/WhatsApp y los padrones históricos son dependencias externas o fuentes de datos.

## Nota sobre diagramas existentes

Para respetar la instrucción de no modificar diagramas existentes, los diagramas que ya estaban en los documentos de arquitectura se han conservado sin cambios. Estos archivos Markdown son vistas nuevas y complementarias. Los archivos PlantUML anteriores se mantienen como respaldo y no se consideran el formato principal para editar los diagramas C4.
