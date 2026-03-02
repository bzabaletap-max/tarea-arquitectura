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

    ## A2) Gobierno de TI (COBIT — mínimo viable)

### Roles y Responsabilidades
* **Dirección (Board/CEO):** Responsable de alinear la estrategia de expansión del E-commerce con las inversiones en tecnología.
* **Comité de TI (CTO/Arquitectura):** Define los estándares de integración entre la tienda (Shopify) y el núcleo (SAP ERP).
* **Oficial de Seguridad (CISO):** Define las políticas de protección de datos de clientes y cumplimiento de pagos.
* **Dueño de Proceso (Logística/Ventas):** Responsable de la integridad de los datos de inventario y pedidos.
* **Operaciones de TI:** Encargados de mantener el uptime de los servicios de Docker y el Broker de eventos.

### 6 Decisiones Gobernadas
1.  **Fuente de verdad del Cliente:** Se establece que el CRM es la única fuente de verdad para perfiles de usuario, prohibiendo duplicados manuales en el ERP.
2.  **Arquitectura de Integración:** Toda nueva conexión entre sistemas satélites debe usar el Broker de eventos (Event-driven) para evitar acoplamiento punto a punto.
3.  **Gestión de Cambios en Producción:** Ningún despliegue en el sistema de pagos se realiza sin pruebas previas en el entorno de staging.
4.  **Presupuesto Cloud/SaaS:** Las renovaciones de licencias de Shopify y Salesforce deben ser aprobadas trimestralmente según métricas de uso.
5.  **Niveles de Acceso:** El acceso a la base de datos de producción está restringido exclusivamente al equipo de DevOps mediante tickets justificados.
6.  **Política de Backups:** Se decide que los respaldos del ERP deben ser inmutables y almacenarse en una región geográfica distinta.

### 5 Políticas Mínimas
1.  **Accesos e identidades:** Es obligatorio el uso de MFA (Autenticación de Múltiples Factores) para todos los administradores y acceso basado en roles (RBAC).
2.  **Cambios y despliegues:** Todo cambio en el código debe pasar por una revisión de pares (Code Review) y pruebas automatizadas antes de su integración.
3.  **Backups y restauración:** Se realizarán copias de seguridad diarias y se ejecutará una prueba de restauración completa cada semestre para garantizar la continuidad.
4.  **Gestión de incidentes:** En caso de caída del servicio, el equipo de TI tiene un máximo de 15 minutos para iniciar la comunicación de crisis y escalar al proveedor correspondiente.
5.  **Gestión de proveedores/SaaS:** Todo proveedor externo debe cumplir con certificaciones mínimas de seguridad (ej. SOC2 o ISO 27001) para manejar datos de la organización.

## A3) Riesgo y seguridad (NIST CSF 2.0)

### Perfil Actual vs Perfil Objetivo
Basado en las 6 funciones principales de NIST CSF 2.0, este es el estado de nuestra organización:

| Función | Perfil Actual (T1) | Perfil Objetivo (T2) |
| :--- | :--- | :--- |
| **Govern** | Políticas básicas sin roles claros. | Gobernanza centralizada con COBIT. |
| **Identify** | Inventario manual de activos. | Inventario automatizado de activos y datos. |
| **Protect** | Uso de contraseñas simples. | MFA obligatorio y segmentación de red. |
| **Detect** | Revisión manual de logs. | Alertas automatizadas en tiempo real. |
| **Respond** | Respuesta reactiva ante fallos. | Plan de incidentes formal y probado. |
| **Recover** | Backups sin pruebas de integridad. | Recuperación ante desastres (DRP) en la nube. |

### 6 Controles Priorizados
1. **Autenticación Multi-factor (MFA):** Bloquea el 99% de ataques de identidad. (Impacto: Alto / Viabilidad: Alta).
2. **Segmentación de Red en Docker:** Aislar el contenedor del ERP de la tienda pública. (Impacto: Alto / Viabilidad: Alta).
3. **Escaneo de Vulnerabilidades en Imágenes:** Detectar fallos en el software antes de subirlo. (Impacto: Medio / Viabilidad: Alta).
4. **Cifrado de Datos (AES-256):** Protección de la base de datos de clientes en reposo. (Impacto: Alto / Viabilidad: Media).
5. **Monitoreo de Logs Centralizado:** Trazabilidad de quién hizo qué y cuándo. (Impacto: Alto / Viabilidad: Media).
6. **Pruebas de Penetración (Pentesting):** Simular ataques para hallar brechas. (Impacto: Medio / Viabilidad: Baja).

### Plan de Respuesta a Incidentes (NIST IR)
1. **Detección y Análisis:** Identificar anomalías en los logs del Broker de eventos (ej. picos de tráfico inusuales).
2. **Contención y Erradicación:** Desconectar el servicio afectado, limpiar la imagen de Docker y parchar la vulnerabilidad.
3. **Recuperación y Post-incidente:** Restaurar servicios desde el último backup sano y documentar las "Lecciones Aprendidas".