# 🏛️ Sistema de Gestión de Programas Sociales - Huamanga

![.NET](https://img.shields.io/badge/.NET-9.0-512BD4?style=flat-square&logo=dotnet)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-MVC-blue?style=flat-square&logo=windows)
![SQL Server](https://img.shields.io/badge/SQL_Server-Microsoft_SQL_Server-CC292B?style=flat-square&logo=microsoftsqlserver)
![Arquitectura](https://img.shields.io/badge/Arquitectura-Monolito_Modular-success?style=flat-square)

Sistema web centralizado diseñado para digitalizar y gestionar el padrón de beneficiarios del **Programa del Vaso de Leche** y los **Comedores Populares** de la Municipalidad Provincial de Huamanga.

Este proyecto busca prevenir duplicidades de inscripción dentro de cada programa social, depurar los padrones históricos y ofrecer visibilidad de la cobertura mediante mapas y reportes. El DNI identifica a la persona, mientras que cada inscripción se evalúa según el programa y comité correspondientes; una coincidencia en otro programa no implica automáticamente una duplicidad.

---
## Curso
Arquitectura de Software (IS-488) – Semestre 2026-II

## Integrantes
Cárdenas García, Yery May  
Chipana Nuñez, Ever Neils

## 🎯 Objetivos del Sistema
- **Padrón depurado:** Detectar posibles inscripciones duplicadas dentro de un mismo programa, revisar los casos entre comités y permitir inscripciones en programas distintos cuando las reglas de elegibilidad lo permitan.
- **Depuración (staging):** Limpiar los datos históricos provenientes de Excel/papel antes de consolidarlos.
- **Cobertura real:** Mostrar mapas y reportes de beneficiarios activos por zonas.
- **Accesibilidad:** Proveer una interfaz PWA para dirigentes de zonas rurales con baja conectividad.

## ⚙️ Arquitectura y patrones
El proyecto está diseñado bajo un enfoque de **monolito modular** utilizando el patrón **MVC (Modelo-Vista-Controlador)**.

- **Patrones de diseño previstos:** Unit of Work, Repository e inyección de dependencias.
- **Metodología:** Spec-Driven Development (SDD).
- **Acceso a datos:** Entity Framework Core, enfoque Code-First.

## 💻 Stack tecnológico
- **Backend:** C# con ASP.NET Core 9.
- **Presentación:** Vistas Razor (`.cshtml`), HTML5, CSS3 y Bootstrap; capacidades PWA.
- **Base de datos:** Microsoft SQL Server.
- **ORM:** Entity Framework Core 9.

## ☁️ Despliegue recomendado

Se propone la opción A: **Azure App Service** para alojar la aplicación ASP.NET Core 9 MVC y un servicio de base de datos administrado separado. **Azure SQL Database** es la primera alternativa por evaluar, previa verificación de compatibilidad con las consultas y migraciones de Entity Framework Core 9. La base de datos debe restringirse para que solo la aplicación pueda conectarse, preferentemente mediante conectividad privada. Los secretos no deben guardarse en el repositorio. Antes de desplegar, se deben estimar los costos mensuales y probar respaldos y restauración. Esta recomendación es de diseño; el repositorio todavía no contiene una aplicación desplegada.

## 📚 Documentación del repositorio

El repositorio contiene actualmente documentación de análisis y diseño. La existencia de un documento describe una decisión o requisito previsto; no implica que la funcionalidad esté implementada o probada.

### Análisis del sistema
- [Actores](analisis-de-sistema/01-actores.md)
- [Historias de usuario](analisis-de-sistema/02-historias-de-usuario.md)
- [Requisitos funcionales](analisis-de-sistema/03-requisitos-funcionales.md)
- [Atributos de calidad](analisis-de-sistema/04-atributos-de-calidad.md)
- [Restricciones](analisis-de-sistema/05-restricciones.md)
- [Drivers arquitectónicos](analisis-de-sistema/06-drivers-arquitectonicos.md)
- [Requisitos no funcionales](analisis-de-sistema/07-requisitos-no-funcionales.md)

### Arquitectura
- [Arquitectura inicial](arquitectura/arquitectura-inicial.md)
- [Enfoque arquitectónico](arquitectura/enfoque-arquitectonico.md)
- [Estilo arquitectónico](arquitectura/estilo-arquitectonico.md)

## Estado del proyecto
El repositorio está enfocado en análisis de requisitos y documentación arquitectónica; todavía no contiene la implementación de la aplicación. Los requisitos cuantitativos y las decisiones marcadas como pendientes deben validarse con el equipo y la Municipalidad antes de darse por aprobados.
