# CleanPro-Arquitectura-U2

### 01 Elegir Patrones de Lógica de Negocio

Justificación del Patrón Seleccionado
Para el desarrollo del sistema de gestión de CleanPro, se elige implementar el patrón Domain Model (Modelo de Dominio) en lugar de un Transaction Script. 

La razón principal es que CleanPro maneja reglas de negocio complejas e invariantes estrictas. Por ejemplo, el sistema debe bloquear descuentos mayores al 10% a menos que tengan autorización de gerencia (RN-01), aplicar penalizaciones exactas por cancelaciones tardías (RN-04) o accesos denegados (RN-05), y la orden de servicio sigue una máquina de estados estricta (desde cotización hasta servicio facturable). 

Utilizar un Transaction Script provocaría que esta lógica quede suelta en los controladores, generando código repetido y el riesgo de procesar estados inválidos. El Domain Model resuelve esto encapsulando estas reglas dentro de las entidades (como `CotizacionFormal` y `OrdenDeServicio`), garantizando que el sistema siempre se mantenga válido antes de tocar la base de datos.

Tabla Comparativa

| Característica | Transaction Script | Domain Model | ¿Por qué es ideal para CleanPro? |
| :--- | :--- | :--- | :--- |
| Estructura | Lógica en un solo flujo de acciones (ej. script de "GenerarCotizacion"). | Lógica distribuida en objetos que representan conceptos reales (Agregados, Entidades). | Modela fielmente los conceptos descubiertos (ej. `Cuadrilla`, `Incidencia`). |
| Manejo de reglas | Validaciones con `if/else` en la capa de aplicación o interfaz. | Validaciones (invariantes) viven dentro de los propios objetos. | Evita errores como aprobar un descuento del 15% sin que pase por gerencia. |
| Máquina de Estados | Estados cambian sobrescribiendo datos directamente en la BD. | El comportamiento del objeto controla en qué etapa está y a cuál puede pasar. | Asegura que un servicio no pase a "Terminado" si no hay firma de conformidad. |
| Mantenibilidad | Propenso a errores al agregar nuevas reglas (Código Espagueti). | Si una política cambia, solo se modifica la entidad responsable. | Facilita agregar futuras reglas, como límites de reprogramaciones. |
