# Brief Ejecutivo-Técnico: Caso E-commerce

## A1) Arquitectura Aplicada
* **System of Record (Núcleo):** SAP ERP (Inventario y Finanzas).
* **Sistemas Satélite:** Shopify (Tienda) y Salesforce (CRM).
* **Flujo BI:** Integración mediante eventos hacia Google BigQuery para reportes de ventas.

### Diagrama de Arquitectura
```mermaid
graph LR
    Shopify[Tienda Online] -- "Pedidos (Eventos)" --> Redis((Broker))
    Redis --> ERP[ERP Central]
    ERP --> CRM[Salesforce]
    ERP --> BI[Data Warehouse]