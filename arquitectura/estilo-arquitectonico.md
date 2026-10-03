# 6. Estilo arquitectónico
 
## Estilo seleccionado
**Monolito modular + arquitectura en capas, con modelo cliente-servidor (SPA Angular + API REST).**
 
- *Monolito modular*: una sola aplicación backend (Node.js/Express), un solo proceso y un solo despliegue, dividida en módulos con límites claros.
- *Capas*: dentro de cada módulo, cada capa solo invoca a la inmediatamente inferior.
- *Cliente-servidor*: el navegador ejecuta la SPA Angular y consume la API REST.
- Capa = organización lógica; monolito = unidad de despliegue.

## Justificación
| Driver | Cómo lo atiende el estilo |
|--------|---------------------------|
| DA01 Escalabilidad | Backend *stateless*: se replican instancias detrás de un balanceador |
| DA02 Rendimiento | Llamadas en proceso (sin red entre módulos) + caché |
| DA03 Seguridad | Punto único de entrada: middleware JWT y roles |
| DA05 API REST | Frontend desacoplado vía contrato REST |
| DA06 Mantenibilidad | Módulos con límites claros; se comunican solo por su servicio |
| RC12 Alcance/plazo | Menor complejidad operativa que microservicios |
 
**Estilos descartados:** microservicios (costo operativo y consistencia distribuida innecesarios para el tamaño actual), SOA/ESB, event-driven y serverless (complejidad y curva de aprendizaje sin necesidad actual). Evolución futura: extraer Catálogo o Pedidos como servicios si la carga lo exige.
 
## Diagrama de arquitectura
 
```mermaid
flowchart TD
    subgraph ACT["ACTORES"]
        Cliente["Cliente"]
        Seller["Seller"]
        Admin["Administrador"]
    end
 
    Web["Cliente Web<br/>Angular 18 · TypeScript"]
 
    subgraph BACK["MONOLITO MODULAR · Node.js + Express · un proceso, un despliegue"]
        MW["Middlewares transversales<br/>CORS · JSON · Auth JWT · Validación · Errores · Logger"]
 
        subgraph PRES["1. CAPA DE PRESENTACIÓN (routes + controllers)"]
            direction LR
            cUsu["Usuarios"]
            cSel["Sellers"]
            cCat["Catálogo"]
            cCar["Carrito"]
            cPed["Pedidos"]
        end
 
        subgraph NEG["2. CAPA DE LÓGICA DE NEGOCIO (services / casos de uso)"]
            direction LR
            sUsu["Usuarios"]
            sSel["Sellers"]
            sCat["Catálogo"]
            sCar["Carrito"]
            sPed["Pedidos"]
            sPag["Pagos"]
            sEnv["Envíos"]
        end
 
        subgraph DAT["3. CAPA DE DATOS (repositories)"]
            direction LR
            rep["Repositorios por módulo<br/>+ acceso compartido (ORM / pool)"]
        end
    end
 
    Cache[("Caché<br/>Redis")]
    DB[("PostgreSQL<br/>marketplace_db")]
 
    subgraph EXT["SISTEMAS EXTERNOS"]
        Pago["Pasarela de pago"]
        Envio["Servicio de envío"]
        Fact["Servicio de facturación"]
        ERP["ERP"]
    end
 
    Cliente --> Web
    Seller --> Web
    Admin --> Web
    Web -->|"HTTPS · JSON · /api/v1"| MW
    MW --> PRES
    PRES --> NEG
    NEG --> DAT
    DAT -->|"SQL · TCP 5432"| DB
    sCat -.-> Cache
    sPag -->|"HTTPS / REST"| Pago
    sEnv -->|"HTTPS / REST"| Envio
    sPed -->|"HTTPS / REST"| Fact
    sCat -->|"sincroniza stock"| ERP
```
 
## Reglas de la arquitectura
1. Cada capa solo invoca a la capa inmediatamente inferior.
2. Un módulo no accede al repositorio ni a las tablas de otro módulo; se comunica con su *service*.
3. Toda integración externa se realiza desde la capa de negocio mediante un adaptador (ver Clean Architecture).
4. Todo se ejecuta en un único proceso Node.js con una única BD.
