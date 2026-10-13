### 04 Architecture Decision Record (ADR) y Diagramas UML

#### ADR 001: Uso de Domain Model para Reglas de Negocio en CleanPro

**Contexto:**
El sistema CleanPro necesita gestionar cotizaciones, asignación de cuadrillas y ejecución de servicios. Existen reglas de negocio estrictas, como penalizaciones del 50% por cancelación tardía, cargos del 100% por falta de acceso, y autorizaciones de gerencia obligatorias para descuentos mayores al 10%. 

**Decisión:**
Se decide utilizar el patrón de diseño **Domain Model** (Modelo de Dominio) con un enfoque de Domain-Driven Design (DDD), aislando la lógica de negocio en Entidades y Objetos de Valor (como `CotizacionFormal` y `OrdenDeServicio`). Se utilizará un **Service Layer** exclusivamente para orquestar los casos de uso, sin contener lógica de negocio.

**Consecuencias:**
- Positivas: Se garantiza que el sistema no procese estados inválidos. La lógica está centralizada y es fácil de mantener si cambian las políticas de la empresa.
- Negativas: Requiere un mayor esfuerzo de diseño inicial en comparación con un script secuencial directo.

---

#### Diagramas UML

**1. Diagrama de Clases (Dominio)**

```mermaid
classDiagram
    class Cliente {
        +String id
        +String nombre
        +String datosContacto
    }
    class CotizacionFormal {
        +String folio
        +Monto total
        +Descuento porcentaje
        +String estado
        +aplicarDescuento(porcentaje)
        +solicitarAutorizacion()
        +aceptarCotizacion()
    }
    class OrdenDeServicio {
        +String folio
        +Superficie metrosCuadrados
        +String estado
        +asignarCuadrilla(Cuadrilla)
        +registrarIncidencia(foto)
        +recalcularPrecio(nuevaSuperficie)
        +cerrarServicio(firma)
    }
    class Cuadrilla {
        +String id
        +String estado
        +marcarAusencia(idIntegrante)
    }
    
    Cliente "1" -- "0..*" CotizacionFormal : solicita
    CotizacionFormal "1" -- "0..1" OrdenDeServicio : genera
    OrdenDeServicio "1" -- "1" Cuadrilla : asignada a

sequenceDiagram
    actor Coordinador
    participant ServicioCotizacion
    participant CotizacionFormal
    actor Gerencia

    Coordinador->>ServicioCotizacion: AplicarDescuento(folio, 15)
    ServicioCotizacion->>CotizacionFormal: aplicarDescuento(15)
    CotizacionFormal-->>ServicioCotizacion: Error Requiere Autorizacion
    ServicioCotizacion->>CotizacionFormal: cambiarEstado(Pendiente)
    ServicioCotizacion->>Gerencia: Notificar Solicitud
    Gerencia->>ServicioCotizacion: AutorizarDescuento(folio)
    ServicioCotizacion->>CotizacionFormal: confirmarDescuento(15)
    CotizacionFormal-->>ServicioCotizacion: Descuento Aplicado

sequenceDiagram
    actor Supervisor
    participant ServicioExcepciones
    participant OrdenDeServicio
    actor Cliente

    Supervisor->>ServicioExcepciones: AjustarSuperficie(folio, 200m2, fotos)
    ServicioExcepciones->>OrdenDeServicio: recalcularPrecio(200m2)
    OrdenDeServicio-->>ServicioExcepciones: Nuevo Precio Calculado
    ServicioExcepciones->>Cliente: Solicitar Autorizacion
    Cliente->>ServicioExcepciones: Autorizar Ajuste
    ServicioExcepciones->>OrdenDeServicio: actualizarAlcance()
    OrdenDeServicio-->>ServicioExcepciones: Orden Actualizada
