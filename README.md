# Proyecto Final - IA Automation
## Ecosistema Inteligente de Gestion y Aprobacion de Solicitudes Presupuestarias

Este repositorio documenta e implementa un ecosistema de automatizacion para procesar solicitudes presupuestarias recibidas por email, estructurar los datos con IA, consultar disponibilidad presupuestaria en Airtable, calcular la brecha, generar una justificacion, incorporar validacion humana (HITL) y comunicar el resultado final dentro del mismo hilo de Gmail.

## Stack

- **Orquestacion:** Make
- **Entrada y salida:** Gmail
- **Base de datos:** Airtable
- **Procesamiento IA del prototipo:** Cohere
- **Notificaciones internas:** Slack
- **Control ejecutivo:** Airtable Shared View

> **Nota sobre el proveedor de IA:** el prototipo utiliza Cohere por una restriccion presupuestaria del entorno academico. La consigna final menciona OpenAI o Anthropic Claude como proveedores esperados. La arquitectura fue disenada para que el proveedor pueda sustituirse sin modificar la logica central de Gmail, JSON, Airtable, HITL, Slack ni el escenario de comunicacion final.

## Flujo funcional

1. Gmail recibe una solicitud presupuestaria.
2. La IA extrae centro de costos, monto, motivo y valida si los datos estan completos.
3. Make estructura la respuesta mediante JSON y enruta el caso.
4. Si los datos estan completos, Airtable consulta el centro de costos y registra la solicitud.
5. Airtable calcula la brecha y la recomendacion deterministica `ASIGNAR`, `NO_ASIGNAR` o `REVISAR`.
6. La IA genera una justificacion breve a partir de los datos calculados.
7. Slack notifica al responsable y la solicitud queda en `Pendiente de aprobacion`.
8. Un humano cambia el estado a `Aprobado por Humano` o `Rechazado por Humano`.
9. El segundo escenario de Make detecta esa decision, responde por Gmail en el mismo hilo y marca `Notificado = true`.
10. Errores y KPIs quedan centralizados en Airtable.

## Entregables

### 1. Arquitectura del sistema

- [Diagrama de Arquitectura - PNG](01_Arquitectura/Diagrama_Arquitectura.png)
- [Version editable - PowerPoint](01_Arquitectura/Diagrama_Arquitectura_Editable.pptx)

### 2. Manual Operativo de Estructuras de Datos

- [Manual Operativo de Datos](02_Manual_Datos/Manual_Operativo_Estructuras_Datos.pdf)

Incluye las tablas `Centros de Costos`, `Solicitudes`, `Errores` y `Dashboard`, sus relaciones, campos, formulas, estados y esquemas de transferencia de datos.

### 3. Estrategia de Optimizacion de Costos

- [Estrategia de Optimizacion de Costos](03_Costos/Estrategia_Optimizacion_Costos.pdf)

Incluye matriz de decision, simulacion para 500 solicitudes, Message Batches y Prompt Caching.

### 4. Seguridad, Privacidad y Resiliencia

- [Seguridad, Privacidad y Resiliencia](04_Seguridad_Resiliencia/Seguridad_Privacidad_Resiliencia.pdf)

Incluye minimizacion de datos, retencion, Error Handlers, trazabilidad, anti-duplicados y Human-in-the-Loop.

### 5. Blueprints de Make

Los dos archivos exportados desde Make deben colocarse en [`05_Blueprints/`](05_Blueprints/):

- `Escenario_1_Gestion_Solicitudes.blueprint.json`
- `Escenario_2_HITL_Comunicacion_Final.blueprint.json`

### 6. Evidencias de pruebas

Las capturas de ejecucion deben colocarse en [`06_Evidencias/`](06_Evidencias/). Se documentaron, entre otras, las siguientes pruebas:

- Fondos suficientes.
- Fondos insuficientes.
- Rechazo humano.
- Datos incompletos / Revision Manual.
- Error controlado de IA con Error Handler.
- Registro del incidente en Airtable.
- Alerta de error en Slack.
- Respuesta Gmail dentro del Thread ID original.
- Dashboard ejecutivo.
- Proteccion contra duplicados con `Notificado = true`.

### 7. Esquemas JSON

- [Esquemas JSON principales](07_JSON/esquemas_json.md)

## Enlaces operativos

> Completar estos enlaces antes de entregar.

- **Base de Airtable en modo lectura:** `PENDIENTE_AGREGAR_URL`
- **Dashboard Ejecutivo - Shared View:** `PENDIENTE_AGREGAR_URL`
- **Video demo (~3 minutos):** `PENDIENTE_AGREGAR_URL`

## Controles de calidad implementados

- Router para datos completos / incompletos.
- Calculos financieros deterministas en Airtable.
- Estado `Revision Manual` cuando faltan datos.
- Error Handlers en modulos de IA.
- Registro persistente de errores en Airtable.
- Alertas operativas en Slack.
- Human-in-the-Loop previo a la comunicacion externa.
- Uso de Gmail `Thread ID` para conservar la conversacion original.
- Filtro `Notificado = false` para evitar comunicaciones duplicadas.
- Dashboard con tasa de aprobacion, volumen de salida y tasa de error.

## Pruebas

El flujo fue probado con caminos felices e infelices, incluyendo casos de informacion incompleta, rechazo humano, errores controlados de IA y ejecuciones repetidas para validar el mecanismo anti-duplicados.

## Estado del proyecto

Implementacion tecnica finalizada. La documentacion principal, diagramas, pruebas y dashboard se encuentran preparados para la entrega final.
