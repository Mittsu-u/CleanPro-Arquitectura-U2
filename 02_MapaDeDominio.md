### 02 Mapa de Dominio y Lenguaje Ubicuo

1. Identificación de Elementos DDD

*   Aggregate Roots (Raíces de Agregado)
    *   `CotizacionFormal`: Entidad principal en la fase de venta. Protege la regla de negocio de que ningún descuento mayor al 10% puede aplicarse sin una autorización de gerencia.
    *   `OrdenDeServicio`: Entidad principal en la fase operativa. Orquesta y protege a las entidades internas durante la ejecución, como la cuadrilla, las incidencias y el acta final.

*   Entities (Entidades)
    *   `Cuadrilla`: Tiene una identidad única y un estado que cambia en el tiempo como disponible o asignada.
    *   `Cliente`: Identificado de manera única, agrupa el historial de servicios y direcciones.

*   Value Objects (Objetos de Valor)
    *   `Dinero` / `Monto`: Representa los costos, totales y penalizaciones. Su regla interna impide que su valor sea negativo.
    *   `Superficie`: Representa los metros cuadrados a limpiar. Su regla dicta que siempre debe ser un valor numérico mayor a cero.
    *   `Descuento`: Valor porcentual que restringe internamente su límite al 10% para aprobaciones automáticas por parte del coordinador.

*   Domain Events (Eventos de Dominio)
    *   `CotizacionAceptada`: Evento en pasado que dispara la creación automática de una nueva Orden de Servicio.
    *   `AusenciaReportada`: Evento que notifica a Administración y exige la reasignación de personal en la Cuadrilla.
    *   `AccesoDenegadoRegistrado`: Evento que detona la generación de una penalización del 100% al cliente.
    *   `ServicioCerrado`: Evento que finaliza el flujo operativo y detona la vinculación con la factura fiscal.

2. Lenguaje Ubicuo (Glosario del Dominio)

*   Prospecto: Usuario que solicita una cotización inicial sin requerir historial previo en el sistema.
*   Cotización Formal: Documento generado con el desglose de costos y horas estimadas. Su estado define si se puede programar el servicio.
*   Autorización de Gerencia: Aprobación explícita requerida para excepciones como descuentos mayores al 10%, inmuebles mayores a 300 m² o cobros mayores a $8,000.
*   Incidencia: Evento imprevisto en sitio, como daño o falta de acceso, que requiere obligatoriamente evidencia fotográfica para registrarse en la plataforma.
*   Servicio Extra: Trabajo adicional solicitado en el domicilio que exige recálculo y aprobación del cliente antes de ser ejecutado por la cuadrilla.
*   Penalización: Cargo económico aplicado estrictamente por cancelación tardía del 50% o por falta de acceso al inmueble del 100%.
*   Acta Digital de Servicio: Registro inmutable que almacena las marcas de tiempo de inicio y fin, evidencias y la firma de conformidad o inconformidad del cliente.
