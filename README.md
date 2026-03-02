# Demo de Integración Arquitectura Empresarial

Este repositorio contiene la entrega de la primera tarea del curso, enfocada en un caso de E-commerce.

## Enlaces de Entrega
* **Video de la Demo:** [https://drive.google.com/file/d/1rjZYzm-eZkF5tuEL_xp6FvSgLAdu2P2o/view?usp=sharing]
* **Documentación Detallada:** [docs/brief.md](./docs/brief.md)

## Cómo ejecutar la demo
1. Tener Docker Desktop instalado y abierto.
2. Abrir una terminal en esta carpeta.
3. Ejecutar el comando:
   ```bash
   docker compose up

   ```markdown
## Flujo de la Demo Técnica
```mermaid
sequenceDiagram
    participant T as Tienda Online (Container)
    participant B as Redis Broker (Container)
    participant E as ERP Sistema (Container)

    Note over T, E: Flujo de Integración Asíncrona
    T->>B: Enviando Pedido "ORD-001"
    B-->>E: ERP extrae pedido del Broker
    E->>E: Procesando e Integrando...