# Restricciones

## Restricciones de la propuesta

| ID | Restricción | Descripción |
|----|-------------|-------------|
| RC01 | Arquitectura | La solución debe construirse como un **Monolito Modular**: un único backend desplegable con módulos cohesionados, no como servicios distribuidos en red. |
| RC02 | Aplicación web / PWA | El cliente debe ser una aplicación web accesible desde navegador, instalable como PWA con soporte offline-first (React + Service Worker). |
| RC03 | API REST | La comunicación entre el frontend y el backend debe realizarse mediante una API REST. |
| RC04 | Tecnología de backend | El backend debe desarrollarse con Node.js 20 LTS y Express, usando Prisma como ORM. |
| RC05 | Base de datos | La información debe almacenarse en PostgreSQL 15 con la extensión PostGIS para la cobertura geoespacial. |
| RC06 | Gateway de mensajería | El sistema debe integrarse con un gateway externo de SMS/WhatsApp para notificar a los beneficiarios. |
| RC07 | Infraestructura | El despliegue se realizará en dos servidores (aplicaciones y base de datos) con Ubuntu Server 22.04 LTS; la base de datos solo será accesible desde el servidor de aplicaciones. |
| RC08 | Dispositivos | Debe funcionar en celulares Android 8 o superior y en navegadores web modernos. |
| RC09 | Alcance de integraciones | En la primera versión, la validación del DNI se limita a cruzar los datos registrados en el propio sistema; RENIEC y SISFOH quedan para una segunda fase. |
| RC10 | Control de versiones | El código fuente debe gestionarse con Git y mantenerse en un repositorio compartido en GitHub. |

## Restricciones adicionales propuestas por el equipo

| ID | Tipo | Restricción | Descripción |
|----|------|-------------|-------------|
| RC11 | Legal | Protección de datos personales | El sistema maneja datos personales (DNI, dirección); su tratamiento debe respetar la normativa peruana de protección de datos personales (Ley N.° 29733). |
| RC12 | Organizacional | Presupuesto municipal limitado | La solución debe ser de bajo costo de infraestructura y operación, por eso se limita a dos servidores. |
| RC13 | Organizacional | Usuarios con poca experiencia | Los dirigentes de comité tienen poca experiencia con tecnología, por lo que se requiere capacitación y una interfaz simple. |
