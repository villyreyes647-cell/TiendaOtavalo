# Sistema de Gestión para la Tienda de Artesanías de Otavalo

Sistema desarrollado en **Java** bajo una arquitectura modular orientada a objetos, diseñado para gestionar artesanos, inventario de artesanías en consignación (textiles y maderas), control de ventas, devoluciones por antigüedad (>90 días) y liquidaciones, con persistencia basada en archivos **CSV**.

---

## Características Principales
* **Gestión de Artesanos:** Registro y validación de datos de artesanos (cédula, nombre, comunidad, teléfono).
* **Control de Inventario y Consignación:** Registro de nuevas piezas, reabastecimiento de stock y control de plazos de devolución (90 días).
* **Reglas de Negocio Automatizadas:**
  * Comisión del **20%** para productos tipo `TEXTIL`.
  * Comisión del **25%** para productos tipo `MADERA`.
* **Procesamiento de Ventas y Auditoría:** Descuento automático de stock y registro cronológico de movimientos.
* **Reportes Gerenciales:** Liquidación de artesanos, alertas de devoluciones pendientes, piezas más vendidas, ventas por comunidad y auditoría de stock.
* **Persistencia en Archivos CSV:** Almacenamiento local estructurado (`artesanos.csv`, `artesanias.csv`, `movimientos.csv`).

---

## Estructura del Proyecto
El código fuente está organizado en los siguientes paquetes:
```text
src/
│
├── modelo/          # Clases de entidad (Artesano, Artesania, Movimiento)
├── negocio/         # Lógica del sistema y reglas de negocio (TiendaOtavalo)
├── datos/           # Manejador de persistencia de archivos CSV (ArchivoManager)
└── Main.java        # Menú interactivo en consola y punto de entrada

## Diagrama de Clases

```mermaid
classDiagram
    class Artesano {
        -String id
        -String nombre
        -String comunidad
        -String telefono
        +getId() String
        +getNombre() String
        +getComunidad() String
        +getTelefono() String
        +aCsv() String
        +desdeCsv(linea) Artesano
    }

    class Artesania {
        -String codigo
        -String nombre
        -String tipo
        -double precioVenta
        -int stockActual
        -LocalDate fechaRecepcion
        -String artesanoId
        +calcularComisionUnitaria() double
        +debeDevolverse() boolean
        +setStockActual(stock)
        +setStock(stock)
        +getCodigo() String
        +getNombre() String
        +getTipo() String
        +getPrecioVenta() double
        +getStockActual() int
        +getFechaRecepcion() LocalDate
        +getArtesanoId() String
        +aCsv() String
        +desdeCsv(linea) Artesania
    }

    class Movimiento {
        -String idMovimiento
        -String codigoArtesania
        -String tipoMovimiento
        -int cantidad
        -LocalDate fecha
        +getIdMovimiento() String
        +getCodigoArtesania() String
        +getTipoMovimiento() String
        +getCantidad() int
        +getFecha() LocalDate
        +aCsv() String
        +desdeCsv(linea) Movimiento
    }

    class TiendaOtavalo {
        -List~Artesano~ artesanos
        -List~Artesania~ artesanias
        -List~Movimiento~ movimientos
        +cargarDatos()
        +guardarDatos()
        +registrarArtesano(a) boolean
        +eliminarArtesano(id) boolean
        +registrarRecepcion(a) boolean
        +reabastecerArtesania(codigo, cantidad) boolean
        +registrarVenta(codigo, cantidad) boolean
        +registrarDevolucion(codigo, cantidad, artesanoId) boolean
        +reporteLiquidacionArtesano(artesanoId)
        +reporteDevolucionesPendientes()
        +reporteArtesaniasMasVendidas()
        +reporteComunidadesConMasVentas()
        +reporteAuditoriaStock()
    }

    class ArchivoManager {
        +inicializarDirectorio()
        +leerLista(archivo) List~String~
        +guardarLista(archivo, lineas)
    }

    class Main {
        +main(args)
    }

    Artesano "1" <-- "*" Artesania : pertenece a
    Artesania "1" <-- "*" Movimiento : registra
    TiendaOtavalo "1" o-- "*" Artesano
    TiendaOtavalo "1" o-- "*" Artesania
    TiendaOtavalo "1" o-- "*" Movimiento
    TiendaOtavalo ..> ArchivoManager : usa
    Main ..> TiendaOtavalo : interactúa