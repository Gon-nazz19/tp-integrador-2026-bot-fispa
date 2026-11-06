# Asistente conversacional Fispa — Fase 1 (Telegram + Gemini + Supabase)

Práctico Integrador 2026 · Sistemas de Gestión · UTN FRSF
Comercio conversacional y asistencia técnica con IA (arquitectura RAG).

Consume la **Base Maestra Normalizada (Golden Record)** de la Fase 0
([tp-integrador-2026-mdm-fispa](https://github.com/Osva-Barcos/tp-integrador-2026-mdm-fispa)).
El canal es Telegram (autorizado por la cátedra); el informe documenta además el costo de implementar
WhatsApp Business Cloud API + Meta Commerce.

## Arquitectura

Telegram → webhook → **Render** (Python/FastAPI) → **Gemini** (function calling) → **Supabase** (Postgres).

Principio anti-alucinación: Gemini solo interpreta el mensaje y redacta. Toda compatibilidad se resuelve
con consultas exactas sobre tablas; código, precio y foto los arma el código, no el modelo. Sin resultado
confirmado, el bot deriva a un asesor.

## Reglas heredadas de la Fase 0 (no negociables)

- `Codigo_Interno`, `Marca` y `Categoria` son inmutables: se guardan exactamente como vienen del ERP.
- Solo existen los 2.184 SKU activos. No se cargan artículos del proveedor que la empresa no vende.
- `Aplicacion_Completa` se conserva intacta; la tabla de compatibilidades es **derivada** y apunta al texto original.
- `Cruces_OEM` no se separa por código: se compara contra cada segmento separado por ` | `, sin coincidencia parcial.
- `Costo_Reposicion` es dato interno: no se carga en lo que consulta el bot ni se muestra jamás. Al cliente solo se le muestra `Precio`.
- Sin estimaciones: lo ambiguo se marca como no confirmable y se deriva.

## Estructura

| Carpeta | Contenido |
|---|---|
| `app/` | Webhook de Telegram, cliente Gemini y herramientas de búsqueda (fases siguientes) |
| `db/` | Esquema SQL de Supabase |
| `scripts/` | Parseo de compatibilidades y carga de la base |
| `tests/` | Pruebas automáticas, incluidos los 4 casos de la demo |
| `docs/` | Arquitectura, System Prompt, SOP y costos de WhatsApp |
| `data/` | Copia local de `base_maestra.csv` (ignorada por git) |

## Estado

- [ ] Fase 1 — parseo de `Aplicacion_Completa` a tabla de compatibilidades
- [ ] Fase 2 — motor de búsqueda determinístico
- [ ] Fase 3 — bot de Telegram + Gemini
- [ ] Fase 4 — cierre y pedido a ventas
- [ ] Fase 5 — documentación (arquitectura, System Prompt, SOP, costos de WhatsApp)
