# C4 — Nivel 1: Contexto del sistema

**Sistema:** Sistema de Gestión de Programas Sociales – Huamanga.

```mermaid
flowchart LR
    dirigente["Dirigente de comité<br/>Gestiona registros de su comité"]
    personal["Personal municipal autorizado<br/>Supervisa y resuelve alertas"]
    sgps(["Sistema de Gestión de Programas Sociales<br/>Inscripciones, revalidación, importación,<br/>reportes y auditoría"])
    gateway["Proveedor SMS / WhatsApp<br/>Servicio externo de notificaciones"]
    padrones[("Padrones históricos<br/>Excel / CSV / papel")]
    reniec["RENIEC / SISFOH<br/>Integración futura"]

    dirigente -->|"Registra y revisa datos"| sgps
    personal -->|"Supervisa y autoriza operaciones"| sgps
    sgps -->|"Solicita envío de avisos"| gateway
    padrones -->|"Datos revisados para importar"| sgps
    sgps -.->|"Posible integración futura"| reniec
```

## Alcance y supuestos

- Los usuarios directos son el dirigente de comité y el personal municipal autorizado.
- El dirigente solo puede operar sobre el comité que tiene asignado.
- Los beneficiarios reciben notificaciones, pero no inician sesión en esta versión.
- RENIEC y SISFOH quedan fuera del alcance inicial.
