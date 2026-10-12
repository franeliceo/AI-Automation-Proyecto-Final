# README

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
> 

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

- [Diagrama de Arquitectura - PNG](https://github.com/franeliceo/AI-Automation-Proyecto-Final/blob/bac15fedbf22890cc56b63cb91b24c71bbe0af57/Diagrama%20de%20Arquitectura.png)

### 2. Manual Operativo de Estructuras de Datos

- [Manual Operativo de Datos](https://github.com/franeliceo/AI-Automation-Proyecto-Final/blob/bac15fedbf22890cc56b63cb91b24c71bbe0af57/1.%20Manual%20Operativo%20Estructuras%20Datos.pdf)

Incluye las tablas `Centros de Costos`, `Solicitudes`, `Errores` y `Dashboard`, sus relaciones, campos, formulas, estados y esquemas de transferencia de datos.

### 3. Estrategia de Optimizacion de Costos

- [Estrategia de Optimizacion de Costos](https://github.com/franeliceo/AI-Automation-Proyecto-Final/blob/bac15fedbf22890cc56b63cb91b24c71bbe0af57/3.%20Estrategia%20Optimizacion%20Costos%20Proyecto%20Final.pdf)

Incluye matriz de decision, simulacion para 500 solicitudes, Message Batches y Prompt Caching.

### 4. Seguridad, Privacidad y Resiliencia

- [Seguridad, Privacidad y Resiliencia](https://github.com/franeliceo/AI-Automation-Proyecto-Final/blob/bac15fedbf22890cc56b63cb91b24c71bbe0af57/4.%20Seguridad%20Privacidad%20Resiliencia%20Proyecto%20Final.pdf)

Incluye minimizacion de datos, retencion, Error Handlers, trazabilidad, anti-duplicados y Human-in-the-Loop.

### 5. Blueprints de Make de ambos escenarios

- [`Escenario_1_Gestion_Solicitudes.blueprint.json`](https://github.com/franeliceo/AI-Automation-Proyecto-Final/blob/bac15fedbf22890cc56b63cb91b24c71bbe0af57/Entrega%20Final%20-%20Curso%20AI%20Automation%20Parte%201.blueprint.json)
- [`Escenario_2_HITL_Comunicacion_Final.blueprint.json`](https://github.com/franeliceo/AI-Automation-Proyecto-Final/blob/bac15fedbf22890cc56b63cb91b24c71bbe0af57/Entrega%20Final%20-%20Validaci%C3%B3n%20Humana%20y%20Comunicaci%C3%B3n%20Final%20-%20Curso%20AI%20Automation%20Parte%202.blueprint.json)

### 6. Evidencias de pruebas

Se documentaron, entre otras, las siguientes pruebas:

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

https://github.com/franeliceo/AI-Automation-Proyecto-Final/blob/bac15fedbf22890cc56b63cb91b24c71bbe0af57/2.%20Capturas%20de%20evidencia%20de%20las%20ejecuciones.pdf

## Enlaces operativos

- **Base de Airtable en modo lectura:** [https://airtable.com/appGFtiSkddoctdnA/shrK5O16AGsGnvLAa](https://airtable.com/appGFtiSkddoctdnA/shrK5O16AGsGnvLAa)
- **Dashboard Ejecutivo - Shared View:** [https://airtable.com/appGFtiSkddoctdnA/shr6D2C1CildJfRdH/tblLvryvuFTJZfN0B](https://airtable.com/appGFtiSkddoctdnA/shr6D2C1CildJfRdH/tblLvryvuFTJZfN0B)
- **Video demo (~3 minutos):**

[https://youtu.be/kCkCNBwJqpg](https://youtu.be/kCkCNBwJqpg)

## Controles de calidad implementados

- Router para datos completos / incompletos.
- Cálculos financieros deterministas en Airtable.
- Estado `Revision Manual` cuando faltan datos.
- Error Handlers en modulos de IA.
- Registro persistente de errores en Airtable.
- Alertas operativas en Slack.
- Human-in-the-Loop previo a la comunicacion externa.
- Uso de Gmail `Thread ID` para conservar la conversacion original.
- Filtro `Notificado = false` para evitar comunicaciones duplicadas.
- Dashboard con tasa de aprobacion, volumen de salida y tasa de error.

## Pruebas

El flujo fue probado con caminos felices e infelices, incluyendo casos de información incompleta, rechazo humano, errores controlados de IA y ejecuciones repetidas para validar el mecanismo anti-duplicados.