### 03 Service Layer (Application Services)

1. ServicioCotizacion
* Metodo: SolicitarCotizacion(datosInmueble, extras)
* Orquestacion: Coordina la recepcion de datos del cliente, instancia una SolicitudDeCotizacion y la guarda en el repositorio de datos.
* Metodo: AplicarDescuento(folioCotizacion, porcentaje)
* Orquestacion: Recupera la CotizacionFormal de la base de datos, llama al metodo interno de la entidad para aplicar el descuento y persiste el nuevo estado.
* Metodo: AceptarCotizacion(folioCotizacion)
* Orquestacion: Recupera la cotizacion, registra la aceptacion y emite el evento de dominio que desencadena la creacion de la OrdenDeServicio.

2. ServicioProgramacion
* Metodo: ProgramarServicio(folioOrden, fecha, hora)
* Orquestacion: Obtiene la OrdenDeServicio, valida la disponibilidad consultando el calendario y guarda la nueva programacion.
* Metodo: AsignarCuadrilla(folioOrden, idCuadrilla)
* Orquestacion: Recupera la orden y la cuadrilla del repositorio, enlaza la cuadrilla a la orden y guarda los cambios.

3. ServicioEjecucion
* Metodo: RegistrarInicio(folioOrden, horaInicio)
* Orquestacion: Recupera la orden, delega el registro de tiempo al ActaDigitalServicio y actualiza la base de datos.
* Metodo: DocumentarIncidencia(folioOrden, tipo, fotos)
* Orquestacion: Sube las imagenes al almacenamiento, crea el registro de la incidencia ligado a la orden y persiste la informacion.
* Metodo: CerrarServicio(folioOrden, firma)
* Orquestacion: Obtiene la orden, registra la firma del cliente y emite el evento de ServicioCerrado para que administracion facture.

4. ServicioExcepciones
* Metodo: ReportarAusencia(folioOrden, idIntegrante)
* Orquestacion: Recupera la cuadrilla, actualiza su estado, genera el registro de nomina y emite una notificacion al coordinador.
* Metodo: AjustarSuperficie(folioOrden, nuevaSuperficie, fotos)
* Orquestacion: Guarda la evidencia, instruye a la OrdenDeServicio a recalcular su precio y notifica al cliente para su autorizacion.
