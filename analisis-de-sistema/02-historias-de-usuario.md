@'
# Historias de usuario

Formato: **Como [actor], quiero [acción], para [beneficio].**

## Historias de la propuesta

| ID | Historia de usuario |
|----|---------------------|
| HU01 | Como dirigente de comité, quiero registrar beneficiarios con su DNI, para mantener actualizado el padrón de mi comité. |
| HU02 | Como dirigente de comité, quiero que el sistema me avise si un DNI ya está inscrito en otro comité, para evitar registrar a una persona duplicada. |
| HU03 | Como dirigente de comité, quiero marcar a un beneficiario como inactivo (fallecimiento, cambio de condición o inasistencia prolongada), para que el padrón solo contenga beneficiarios vigentes. |
| HU04 | Como personal de la Sub Gerencia, quiero recibir alertas de posibles duplicados y consultar el historial de cambios del padrón, para evaluarlos y depurar el padrón. |
| HU05 | Como personal de la Sub Gerencia, quiero que cada beneficiario se revalide periódicamente (cada 6 meses), para detectar a quienes ya no califican. |
| HU06 | Como beneficiario, quiero recibir un SMS/WhatsApp cuando mi inscripción sea aprobada o deba revalidar mis datos, para estar informado de mi situación. |
| HU07 | Como personal de la Sub Gerencia, quiero ver un mapa con los beneficiarios activos por zona, para identificar barrios sobre-atendidos o desatendidos. |
| HU08 | Como personal de la Sub Gerencia, quiero generar reportes de cobertura y exportar el padrón depurado en Excel/PDF, para sustentar la distribución de recursos entre comités. |
| HU09 | Como personal de la Sub Gerencia, quiero importar y depurar los padrones históricos en Excel, para consolidarlos en la base de datos principal sin arrastrar errores. |

## Historias adicionales propuestas por el equipo

| ID | Historia de usuario | Actor | Justificación |
|----|---------------------|-------|---------------|
| HU10 | Como dirigente de comité o personal de la Sub Gerencia, quiero iniciar sesión con mis credenciales según mi rol, para acceder solo a la información que me corresponde. | Dirigente / Personal de la Sub Gerencia | La propuesta incluye un módulo de Autenticación y exige accesos restringidos por rol para proteger datos sensibles. |
| HU11 | Como dirigente de comité, quiero registrar beneficiarios sin conexión a internet y que se sincronicen después, para trabajar en zonas con baja cobertura. | Dirigente de Comité | La propuesta exige una PWA con operación offline-first. |
'@ | Set-Content analisis-de-sistema/02-historias-de-usuario.md -Encoding utf8