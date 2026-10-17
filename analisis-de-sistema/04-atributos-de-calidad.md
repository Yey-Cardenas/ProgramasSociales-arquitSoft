# Atributos de calidad

## Escenario general

Un dirigente de comité registra a varios beneficiarios en una zona con baja cobertura de internet. Al recuperar la conexión, su aplicativo sincroniza los datos y el sistema debe validar los DNI contra todo el padrón municipal, mientras el personal de la Sub Gerencia consulta el mapa de cobertura. Los datos son personales y sensibles, y el sistema opera durante todo el año.

## Escenarios de calidad

| ID | Atributo de calidad | Escenario de calidad | Origen |
|----|---------------------|----------------------|--------|
| AC01 | **Disponibilidad** | El sistema debe estar disponible al menos el 99% del tiempo y permitir registros sin conexión (offline-first), sincronizándolos al recuperar la red. | RNF-01 |
| AC02 | **Rendimiento** | La validación de duplicados por DNI debe completarse en menos de 0.5 segundos. | RNF-02 |
| AC03 | **Usabilidad** | El registro de un beneficiario debe completarse con un máximo de 5 campos obligatorios, pensando en dirigentes con poca experiencia tecnológica. | RNF-03 |
| AC04 | **Seguridad y privacidad** | Los datos personales (DNI, dirección) deben estar protegidos mediante cifrado y accesos restringidos por rol. | RNF-04 |
| AC05 | **Compatibilidad** | El aplicativo del dirigente debe funcionar en celulares de gama baja (Android 8+) y en navegador web, usando tecnologías PWA. | RNF-05 |
| AC06 | **Auditabilidad** | Toda modificación al padrón debe registrarse con usuario, fecha y motivo del cambio. | RNF-06 |
| AC07 | **Escalabilidad** | El sistema debe soportar al menos 15,000 beneficiarios sin degradar el rendimiento. | RNF-07 |
| AC08 | **Mantenibilidad** | La arquitectura debe permitir agregar nuevos tipos de programas sociales sin rediseñar el sistema. | RNF-08 |
| AC09 | **Respaldo de información** | El sistema debe generar copias de seguridad diarias de la base de datos. | RNF-09 |

## Escenarios con formato estímulo – respuesta – medida

### AC02 – Rendimiento

| Elemento | Descripción |
|----------|-------------|
| **Estímulo** | Un dirigente ingresa el DNI de un nuevo beneficiario para registrarlo. |
| **Respuesta** | El sistema busca el DNI en todo el padrón municipal e informa si ya existe en otro comité. |
| **Medida** | La respuesta se entrega en menos de 0.5 segundos con 15,000 beneficiarios registrados. |

### AC01 – Disponibilidad

| Elemento | Descripción |
|----------|-------------|
| **Estímulo** | Un dirigente registra beneficiarios en una zona sin conexión a internet. |
| **Respuesta** | La PWA guarda los registros en el dispositivo y los sincroniza automáticamente al recuperar la red. |
| **Medida** | No se pierde ningún registro y el sistema se mantiene disponible al menos el 99% del tiempo. |
