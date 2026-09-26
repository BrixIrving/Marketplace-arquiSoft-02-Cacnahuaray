```mermaid
flowchart TD
    subgraph ACTORES["ACTORES"]
        Cliente["Cliente"]
        Seller["Seller"]
        Admin["Administrador"]
    end
 
    subgraph PRESENTACION["PRESENTACIÓN — Web / API REST"]
        Web["Aplicación Web"]
    end
 
    subgraph NEGOCIO["LÓGICA DE NEGOCIO"]
        Catalogo["Catálogo"]
        Carrito["Carrito"]
        Pedidos["Pedidos"]
        Sellers["Gestión de Sellers"]
        Notificaciones["Notificaciones"]
    end
 
    subgraph DATOS["DATOS"]
        BD["Base de datos"]
    end
 
    subgraph EXTERNOS["SISTEMAS EXTERNOS"]
        Pago["Pasarela de pago"]
        Envio["Servicio de envío"]
        Facturacion["Servicio de Facturación"]
        ERP["ERP"]
    end
 
    ACTORES --> PRESENTACION --> NEGOCIO --> DATOS
    Pedidos -.->|"cobro"| Pago
    Pedidos -.->|"despacho"| Envio
    Pedidos -.->|"comprobante"| Facturacion
    Catalogo -.->|"stock"| ERP
```
 
## Descripción
 
La capa de **Presentación** expone la aplicación web mediante API REST. La capa de **Lógica de negocio** concentra los módulos de catálogo, carrito, pedidos, gestión de sellers y notificaciones, cada uno con responsabilidades independientes. La capa de **Datos** centraliza la persistencia de toda la información. Los **sistemas externos** (pasarela de pago, servicio de envío, facturación y ERP) se integran únicamente desde los módulos de Pedidos y Catálogo, manteniendo el resto del sistema desacoplado de ellos.