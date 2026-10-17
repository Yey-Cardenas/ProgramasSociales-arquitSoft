# Restricciones

## Restricciones de la propuesta

| ID | Restricción | Descripción |
|----|-------------|-------------|
| RC01 | Arquitectura | La solución debe construirse como un **Monolito Modular**: un único sistema desplegable, organizado en módulos cohesionados, sin separar la lógica del negocio en microservicios. |
| RC02 | Aplicación web / PWA | La aplicación debe desarrollarse con **ASP.NET Core MVC 9** y vistas Razor (`.cshtml`). Debe ofrecer una interfaz web adaptable y capacidades PWA para apoyar el trabajo en zonas con conectividad limitada. |
| RC03 | Comunicación web e integraciones | La interacción principal entre el navegador y el servidor seguirá el flujo de peticiones HTTP de ASP.NET Core MVC. Cuando una integración lo requiera, el sistema podrá consumir o exponer endpoints HTTP/API, sin imponer una arquitectura de frontend independiente. |
| RC04 | Tecnología de backend | El backend debe desarrollarse en **C# con ASP.NET Core 9**, siguiendo el patrón Modelo-Vista-Controlador (MVC). El acceso a datos se realizará mediante **Entity Framework Core 9**. |
| RC05 | Base de datos | La información debe almacenarse en **Microsoft SQL Server**. El acceso y la persistencia se gestionarán mediante Entity Framework Core y un enfoque Code-First, según el diseño del sistema. |
| RC06 | Gateway de mensajería | El sistema debe integrarse con un gateway externo de SMS/WhatsApp para notificar a los beneficiarios. La integración se realizará desde la capa de servicios, sin acoplarla a las vistas. |
| RC07 | Infraestructura | El despliegue debe separar el servidor de aplicaciones del servidor de base de datos. La base de datos solo debe aceptar conexiones desde el servidor de aplicaciones. El entorno de despliegue deberá ser compatible con ASP.NET Core 9 y SQL Server. |
| RC08 | Dispositivos | La interfaz debe funcionar en navegadores web modernos y adaptarse a celulares, incluidos dispositivos Android 8 o superior, en la medida permitida por el navegador y las capacidades PWA utilizadas. |
| RC09 | Alcance de integraciones | En la primera versión, la validación del DNI se limita a cruzar los datos registrados en el propio sistema; las integraciones con RENIEC y SISFOH quedan para una segunda fase. |
| RC10 | Control de versiones | El código fuente y la documentación deben gestionarse con Git y mantenerse en un repositorio compartido en GitHub. |

## Restricciones adicionales propuestas por el equipo

| ID | Tipo | Restricción | Descripción |
|----|------|-------------|-------------|
| RC11 | Legal | Protección de datos personales | El sistema maneja datos personales, como DNI y dirección; su tratamiento debe respetar la normativa peruana de protección de datos personales (Ley N.° 29733). |
| RC12 | Organizacional | Presupuesto municipal limitado | La solución debe mantener bajos los costos de infraestructura y operación, evitando una arquitectura distribuida innecesariamente compleja. |
| RC13 | Organizacional | Usuarios con poca experiencia | Los dirigentes de comité tienen poca experiencia con tecnología, por lo que se requiere una interfaz simple, instrucciones claras y capacitación básica. |

## Criterio de consistencia tecnológica

Las decisiones tecnológicas de este documento deben mantenerse alineadas con el `README.md` del repositorio: **C#, ASP.NET Core 9 MVC, vistas Razor, Entity Framework Core 9 y Microsoft SQL Server**. No se consideran parte de la arquitectura aprobada React, Node.js, Express, Prisma ni PostgreSQL, salvo que el equipo acuerde formalmente un cambio y actualice toda la documentación relacionada.
