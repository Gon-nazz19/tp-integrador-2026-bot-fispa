# CLAUDE.md — Bot Fispa (Práctico Integrador 2026, Fase 1)

Asistente conversacional de repuestos Fispa por Telegram. Stack: Python 3.10+, FastAPI, Gemini (function calling),
Supabase (Postgres), Render. Idioma del proyecto: español (código, comentarios, docs y mensajes del bot).

**Antes de tocar nada, leé `docs/PLAN_DESARROLLO.md`.** Trabajá una fase por vez, en modo plan primero, y no pases
a la siguiente hasta que los criterios de aceptación de la actual se cumplan y los tests pasen.

## Reglas duras (heredadas de la Fase 0; no se negocian)

1. `Codigo_Interno`, `Marca` y `Categoria` son inmutables: se guardan tal cual vienen (ej. `10001 FISPA`, con el sufijo
   y los caracteres rotos como `OïRINGS`). Nunca normalizarlos ni "arreglarlos".
2. Solo existen los 2.184 SKU de `base_maestra.csv`. No cargar nada más del proveedor.
3. `Aplicacion_Completa` y `Cruces_OEM` se conservan intactos. Todo lo parseado va en tablas **derivadas** que apuntan al texto original.
4. **`Costo_Reposicion` y `EAN13` no se cargan en la base del bot ni se muestran jamás.** Al cliente solo se le muestra `Precio` (mayorista).
5. Cero estimaciones: ante datos ambiguos se marca "no confirmable" y el bot deriva a un asesor. Nunca se completa ni se adivina.
6. Compatibilidad = coincidencia estricta contra el texto de `Aplicacion_Completa`. Gemini **nunca** decide compatibilidad.
7. Código, precio y foto de cada resultado los arma el código a partir de la base, nunca el LLM.
8. No hardcodear "FISPA": la marca sale de la columna `Marca`.
9. Prohibido commitear `.env`, claves, tokens o `data/`. Usar `.env.example` sin valores.

## Convenciones

- Tests con `pytest` en `tests/`; todo cambio de lógica de datos o de búsqueda lleva test. Correr `pytest` antes de cada commit.
- Fixtures de test: textos reales de `Aplicacion_Completa` (ver sección 3 del plan), no inventados.
- Scripts reproducibles y deterministas (misma entrada, misma salida). Sin llamadas a LLM en el parseo de datos.
- Commits chicos, en español, uno por tarea del plan.
- El System Prompt vive en `docs/system_prompt.md` y la app lo lee de ahí: el documento entregable y lo que corre son el mismo archivo.

## Comandos (Windows)

```
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
pytest
```

Los datos de entrada: copiar `salida/base_maestra.csv` de la Fase 0 a `data/base_maestra.csv` (CSV UTF-8 con BOM, usar `encoding="utf-8-sig"`).
