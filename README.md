# Ecosistema de Agentes de IA Omnicanal
### Automatización con IA, Chatbots y Flujos de Trabajo Basados en Datos para Operaciones Empresariales

![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)
![Serverless](https://img.shields.io/badge/Serverless-FF4F00?style=for-the-badge&logo=serverless&logoColor=white)

---

## 🎯 Visión General: De Demo Funcional a Ecosistema Empresarial Escalable

Este repositorio implementa la base fundamental (**Demo / MVP**) de un **Ecosistema de Agentes de IA Omnicanal** diseñado para gestionar interacciones bilingües con clientes, generar resúmenes automatizados de proyectos mediante IA, capturar prospectos (*leads*) validados y sincronizar datos en tiempo real a través de computación *serverless* en el borde (*edge computing*).

> 💡 **Núcleo Modular y Escalable:** Si bien esta implementación opera como una demostración funcional autónoma (conectada a Cloudflare D1 y Google Sheets), su arquitectura basada en webhooks y API REST está construida como un motor extensible capaz de escalar hacia operaciones empresariales complejas: integración con **Chatwoot** para un soporte omnicanal real (WhatsApp, Instagram, Web, Telegram), orquestación avanzada de flujos de trabajo en **n8n**, **sistemas multiagente** especializados y sincronización con CRMs corporativos.

**Experiencia Interactiva:** Carga `index.html` para interactuar en vivo con **MARLA** (*Multi-lingual Agentic Responsive Lead Assistant* / Asistente Agéntica Multilingüe de Respuesta de Prospectos), realizar preguntas técnicas sobre la arquitectura y ejecutar una inserción automatizada directamente en nuestra base de datos en vivo de Google Sheets.

---

## Problema de Negocio

Muchas organizaciones enfrentan dificultades con:
- Flujos de trabajo manuales de atención al cliente y tiempos de respuesta lentos en múltiples canales.
- Requerimientos entrantes no estructurados provenientes de chats, aplicaciones de mensajería y formularios.
- Captura de prospectos fragmentada que carece de una estricta validación de control de calidad (QA) y persistencia automática en bases de datos.
- Desconexión entre asistentes de IA, bandejas de entrada de agentes humanos y herramientas internas de automatización.

---

## Solución Actual y Flujo de Trabajo (Núcleo Demo)

Un pipeline automatizado y *serverless* que gestiona el contexto conversacional y la ingesta de prospectos de principio a fin:

1. **Entrada del Usuario:** El usuario interactúa a través de la interfaz web (`index.html`) en español o inglés (con cambio dinámico mediante comandos u opciones de menú).
2. **Máquina de Estados en el Borde (Edge) y Control de Inactividad:** Cloudflare Workers orquesta estados conversacionales de múltiples pasos mediante Cloudflare KV (`CHAT_STATE_DEMO`), aplicando un reinicio estricto por inactividad de 5 minutos (300 s) y un tiempo de vida (TTL) máximo de 24 horas.
3. **Inferencia de IA y Resumen Automático:** Las consultas técnicas abiertas y el contexto del chat se procesan a través de Cloudflare Workers AI utilizando **Gemma 4 26B** (`@cf/google/gemma-4-26b-a4b-it`, compatible con salidas de razonamiento estilo OpenAI). Al activar el flujo de registro (`AGENDAR` / `SCHEDULE`), MARLA redacta automáticamente un resumen del proyecto en primera persona basado en el historial del chat.
4. **Validación Estricta de QA y Doble Verificación:** Una máquina de estados guiada captura y valida Nombre, Correo Electrónico (con doble confirmación), Teléfono/WhatsApp (con doble confirmación), Empresa, País y Caso de Uso del Proyecto.
5. **Doble Persistencia y Sincronización Asíncrona:** Tras la confirmación final, los prospectos se insertan directamente en Cloudflare D1 (`env.DB`) y se envían de forma asíncrona mediante `ctx.waitUntil()` a un Webhook de Google Apps Script para agregar filas en Google Sheets sin añadir latencia para el usuario.

---

## Arquitectura Actual y Ruta de Escalabilidad

```text
┌─────────────────────────┐          ┌─────────────────────────────────┐          ┌───────────────────────────┐
│  Demo Frontend (Web)    │  HTTPS   │    Cloudflare Workers API       │   AI API │    Cloudflare Workers AI  │
│  (O Canales Chatwoot)   │ ───────> │  (Enrutador, Estado KV y QA)    │ ───────> │  (Gemma 4 / Multiagente)  │
└─────────────────────────┘          └───────┬─────────────────┬───────┘          └───────────────────────────┘
                                             │                 │
                       Estado KV y TTL       │                 │ Inserción SQLite (`env.DB`)
                      (`CHAT_STATE_DEMO`)    ▼                 ▼
                                     ┌───────────────┐ ┌───────────────────────┐
                                     │ Cloudflare KV │ │ Base de Datos D1      │
                                     └───────────────┘ └───────────────────────┘
                                             │
                                             │ Webhooks Asíncronos (`ctx.waitUntil`)
                                             ├──────────────────────────────────────────┐
                                             ▼                                          ▼
                             ┌─────────────────────────────┐            ┌───────────────────────────────┐
                             │    Google Apps Script API   │            │  🚀 Escalabilidad Empresarial │
                             │ (Sincronización Sheets Vivo)│            │  • n8n (Flujos Automatizados) │
                             └─────────────────────────────┘            │  • Chatwoot (Transfer. Humana)│
                                                                        │  • CRMs (HubSpot / Salesforce)│
                                                                        └───────────────────────────────┘
```

---

## 🚀 Potencial de Escalabilidad y Evolución Empresarial

Este proyecto sienta las bases técnicas para evolucionar hacia una plataforma empresarial completa mediante integraciones desacopladas:

- 💬 **Soporte Omnicanal Real con Chatwoot:** Al conectar este Worker como un *Agent Bot* en Chatwoot, exactamente el mismo motor de IA puede atender simultáneamente WhatsApp Business, Instagram DM, Facebook Messenger, Telegram y Webchat, permitiendo al mismo tiempo una transferencia fluida (*human handoff*) a agentes en vivo dentro de una bandeja de entrada unificada.
- ⚡ **Orquestación Automatizada de Flujos con n8n:** El webhook asíncrono (`ctx.waitUntil`) permite reemplazar o complementar Google Apps Script conectándose directamente a **n8n** para desencadenar automatizaciones complejas: correos transaccionales, notificaciones en Slack/Teams, creación de tratos (*deals*) en CRMs (HubSpot, Pipedrive, Salesforce) y agendamiento automatizado en Google Calendar o Cal.com.
- 🧠 **Arquitectura Multiagente Especializada:** El enrutador del Worker puede expandirse para delegar tareas a distintos subagentes especializados según la intención del usuario:
  - **Agente de Calificación (SDR):** Enriquecimiento de datos de la empresa y puntuación de prospectos (*lead scoring*).
  - **Agente de Soporte Técnico (RAG):** Consulta de documentación interna mediante Cloudflare Vectorize.
  - **Agente de Operaciones / Cotizaciones:** Generación dinámica de propuestas comerciales y enlaces de pago.
- 📊 **Analítica Avanzada y Auditoría:** Aprovechamiento de Cloudflare D1 y Analytics Engine para medir tasas de conversión, tiempos de respuesta, intenciones frecuentes y rendimiento por canal.

---

## Estructura del Repositorio

```text
omnichannel-ai-agent-ecosystem/
├── index.html              # Interfaz de chat frontend interactiva (UI Demo)
├── package.json            # Dependencias y scripts del proyecto
├── README.md               # Documentación técnica y hoja de ruta
├── src/
│   └── workers/
│       └── index.js        # Cloudflare Worker (IA, Máquina de Estados, D1 y Webhooks)
└── wrangler.toml           # Configuración de enlaces (bindings) de Workers, IA y KV
```

---

## Características Principales (Implementación Actual)

- **Ejecución Serverless Nativa en el Borde (Edge):** Impulsado por Cloudflare Workers (`compatibility_date = "2026-06-01"`) para tiempos de respuesta globales de ultrabaja latencia.
- **Agente de IA Bilingüe de Última Generación (MARLA):** Impulsado por `@cf/google/gemma-4-26b-a4b-it` (intercambiable con Qwen 3.8 o DeepSeek V4.1) con barreras de dominio estrictas (*guardrails*) y eliminación automática de etiquetas `<think>`.
- **Resumen de Prospectos Asistido por IA:** Sintetiza automáticamente el historial de la conversación en una descripción concisa del proyecto en primera persona al iniciar la captura del prospecto, permitiendo al usuario aprobarla o escribirla manualmente.
- **Validación Estricta de Campos (QA) y Doble Verificación:** Aplica límites de longitud de caracteres, validación mediante expresiones regulares (*regex*) para correos electrónicos, conteo mínimo de dígitos numéricos reales para teléfonos y pasos interactivos de confirmación (`1️⃣ Sí` / `2️⃣ No`) en datos críticos de contacto.
- **Gestión Inteligente de Sesiones e Inactividad:** Rastrea marcas de tiempo en `CHAT_STATE_DEMO` (KV) para reiniciar sesiones inactivas después de 5 minutos, admitiendo comandos limpios de cancelación (`CANCELAR`, `CANCEL`, `MENU`).
- **Doble Persistencia en Base de Datos y Webhook:** Combina almacenamiento relacional en el borde (Cloudflare D1) con sincronización sin bloqueo hacia Google Sheets (o n8n) mediante `ctx.waitUntil()`.

---

## Configuración y Despliegue (`wrangler.toml`)

Asegúrate de configurar tus enlaces (*bindings*) y secretos en Cloudflare antes de desplegar:

```toml
name = "demo1-chatbot"
main = "src/workers/index.js"
compatibility_date = "2026-06-01"

[ai]
binding = "AI_DEMO"

[[kv_namespaces]]
binding = "CHAT_STATE_DEMO"
id = "TU_ID"

# Opcional: Vincula tu base de datos D1 para persistencia SQL
# [[d1_databases]]
# binding = "DB"
# database_name = "leads_db"
# database_id = "<TU_ID_DE_BASE_DE_DATOS_D1>"
```

### Variables de Entorno / Secretos

Configura la URL de tu webhook (Google Apps Script o webhook de n8n) como un secreto del Worker:

```bash
npx wrangler secret put GOOGLE_APPS_SCRIPT_WEBAPP_URL
```