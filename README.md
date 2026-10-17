# 🏛️ Sistema de Gestión de Programas Sociales - Huamanga

![.NET](https://img.shields.io/badge/.NET-9.0-512BD4?style=flat-square&logo=dotnet)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-MVC-blue?style=flat-square&logo=windows)
![SQL Server](https://img.shields.io/badge/SQL_Server-AuditoriaDB-CC292B?style=flat-square&logo=microsoftsqlserver)
![Arquitectura](https://img.shields.io/badge/Arquitectura-Monolito_Modular-success?style=flat-square)

Sistema web centralizado diseñado para digitalizar y gestionar el padrón de beneficiarios del **Programa del Vaso de Leche** y los **Comedores Populares** de la Municipalidad Provincial de Huamanga.

Este proyecto busca eliminar la duplicidad de beneficiarios, depurar los padrones históricos y ofrecer una visibilidad real de la cobertura mediante mapas y reportes, asegurando que los recursos lleguen a quienes realmente los necesitan.

---
## Curso
Arquitectura de Software (IS-488) – Semestre 2026-II

## Integrantes
Cárdenas García, Yery May  
Chipana Nuñez, Ever Neils


## 🎯 Objetivos del Sistema
* **Padrón Único:** Evitar que una misma persona cobre doble ración en distintos comités.
* **Depuración (Staging):** Limpiar los datos históricos provenientes de Excel/papel antes de consolidarlos.
* **Cobertura Real:** Mostrar mapas y reportes de beneficiarios activos por zonas.
* **Accesibilidad:** Proveer una interfaz (PWA) para dirigentes de zonas rurales con baja conectividad.

## ⚙️ Arquitectura y Patrones
El proyecto está diseñado bajo un enfoque de **Monolito Modular** utilizando el patrón **MVC (Modelo-Vista-Controlador)**.

* **Patrones de Diseño:** Unit of Work, Repository Pattern, Inyección de Dependencias.
* **Metodología:** Spec-Driven Development (SDD).
* **Acceso a Datos:** Entity Framework Core (Code-First / Transaccional).

## 💻 Stack Tecnológico
* **Backend:** C# con ASP.NET Core 9
* **Frontend:** Vistas Razor (`.cshtml`), HTML5, CSS3 (Bootstrap), capacidades PWA (Offline-first).
* **Base de Datos:** Microsoft SQL Server.
* **ORM:** Entity Framework Core 9.

## 📂 Estructura del Repositorio

El repositorio actualmente contiene la documentación de diseño arquitectónico y levantamiento de requisitos:

```text
📦 ProgramasSociales-arquitSoft
 ┣ 📂 analisis-de-sistema/      # Requisitos, Historias de Usuario, Actores y Restricciones
 ┣ 📂 arquitectura/             # Definición del Estilo, Enfoque MVC y Diagramas C4
 ┣ 📜 README.md                 # Documentación principal
 ┗ 📜 .gitignore                # Archivos ignorados por Git


