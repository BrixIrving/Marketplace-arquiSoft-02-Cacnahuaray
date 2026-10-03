# Drivers arquitectónicos
 
| ID | Driver arquitectónico | Origen | ¿Por qué influye en la arquitectura? |
|---|---|---|---|
| DA01 | Soportar picos de tráfico en campañas | AC03 – Escalabilidad | Exige una estrategia de escalamiento horizontal del catálogo y checkout. |
| DA02 | Checkout resiliente ante fallas externas | AC02 – Disponibilidad | Obliga a desacoplar el pedido de la confirmación de envío/pago. |
| DA03 | Protección de datos sensibles de pago | AC04 – Seguridad | Determina cómo se maneja la autenticación y el cifrado en la capa de negocio. |
| DA04 | Integración con pasarela y logística externas | RC04, RC05 | Condiciona el diseño de adaptadores/integraciones en la capa de negocio. |
| DA05 | Bajo acoplamiento entre módulos | AC05 – Mantenibilidad | Guía la separación de responsabilidades dentro de la capa de lógica de negocio. |
| DA06 | El sistema debe permitir modificar funcionalidades sin afectar innecesariamente otros módulos | AC05 – Mantenibilidad | Influye en la separación de responsabilidades, modularidad y dependencias internas. |

---