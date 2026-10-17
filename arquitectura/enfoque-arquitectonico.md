# Enfoque Arquitectónico: Patrón MVC

**Proyecto:** Sistema de Gestión de Programas Sociales – Huamanga
**Curso:** Arquitectura de Software (IS-488) – Semestre 2026-II

## 1. Definición del enfoque
El sistema utilizará el patrón **MVC (Modelo-Vista-Controlador)** implementado sobre **ASP.NET Core MVC (.NET 9)**. Este patrón separa la aplicación en tres grupos de componentes principales, facilitando el desarrollo rápido y el mantenimiento.

## 2. Componentes del Patrón

*   **Modelo (Model):** Representa los datos y la lógica de negocio. Incluye las Entidades (Beneficiario, Comité), la configuración de la base de datos mediante **Entity Framework Core**, y la gestión de transacciones utilizando los patrones **Unit of Work y Repository**.
*   **Vista (View):** Es la interfaz de usuario. Se utilizarán **Vistas Razor (`.cshtml`)** para generar HTML dinámico, optimizado para funcionar en navegadores y adaptarse como PWA (Progressive Web App).
*   **Controlador (Controller):** Actúa como orquestador. Recibe las peticiones HTTP del usuario (ej. guardar un beneficiario), interactúa con el Modelo para validar los datos o aplicar lógica, y selecciona la Vista adecuada para mostrar el resultado.

## 3. Flujo de trabajo (Ejemplo: Registrar Beneficiario)

```mermaid
sequenceDiagram
    actor Usuario
    participant V as Vista (Razor .cshtml)
    participant C as Controlador (ASP.NET)
    participant M as Modelo (Unit of Work / EF Core)
    participant BD as SQL Server (AuditoriaDB)

    Usuario->>V: Llena formulario y envía
    V->>C: POST /Beneficiario/Crear
    C->>M: Validar datos y duplicados
    M->>BD: Inserción transaccional
    BD-->>M: Confirmación
    M-->>C: Resultado exitoso
    C-->>V: Redirige a lista actualizada
    V-->>Usuario: Muestra confirmación