---
name: rag-piloto
description: Usar al diseñar, revisar o prometer un piloto o feature RAG (asistente sobre documentos, chat con citas, busqueda semantica) para cliente o para nosotros - antes de elegir stack, fijar metricas o decidir si el flujo necesita RAG. Senales de que estás a punto de fallar: golden set ausente antes de prometer, indexar con un modelo y consultar con otro, guardrails de PII/inyeccion ausentes, sin rerank, sin observabilidad, o "RAG en todo".
---

# Rag piloto — checklist antes de prometer

**Idea central:** RAG = recuperar contexto + generar con citas. Un piloto vendible no es el pipeline: es **pipeline + evaluacion antes de la promesa + guardrails + saber donde NO va**. *"RAG en dia 1 no es bueno — 2-3% de error, se mejora midiendo."*

## Los 8 criterios (todos con respuesta antes de la demo)

| # | Punto | Decision-base |
|---|---|---|
| 1 | Evaluacion | Golden set de 10-30 preguntas con respuesta conocida → recall@k + faithfulness ANTES de prometer. Metricas > "lo probe y funciona" |
| 2 | Chunking | 1000-1500 chars o por secciones markdown; overlap 10-15% (contexto compartido); header [doc · pagina] por chunk; el chunk mas cercano manda y se devuelve el doc madre |
| 3 | Mismo modelo | Indexar y consultar con EL MISMO embedding model y dimensiones exactas — mezclar = "peras con manzanas"; un indice con dims distintas es mala practica |
| 4 | Guardrails | PII + topic + output (Nemo Guardrails / Llama Guard). DEMO de inyeccion obligatoria: sin guardrails el bot acepta "olvida tus instrucciones" y cambia de identidad; off-topic gasta tokens. "Guardrails also fail with cheaper solutions" → probar el bypass |
| 5 | Donde NO va | Flujos deterministas (calculo de dinero, links de cobro, pagos): NO. Regla: **RAG informa, nunca calcula ni cobra**. Si acaso, asistente de docs al lado, modo lectura, con citas |
| 6 | Retrieval → rerank | Hibrido BM25+denso con RRF; fetch 15 candidatos → cross-encoder rerank (FlashRank local / Jina cloud) → top 5 en el prompt |
| 7 | Honestidad dia-1 | Prometer "responde citando y admite que no sabe", no "IA que responde de todo"; declarar el 2-3% de error inicial |
| 8 | Observabilidad | Spans/traces desde el dia 1 (Pydantic Logfire / Langfuse): "observability exists so you can see which span took the most time" |

## Reglas duras (referencia rapida)

- **Nunca >100k docs en un shot** — acotar con filtros de metadata a 10k-20k; a 1M docs: quantization + filtros.
- **Embeddings**: elegir modelo por leaderboard MTEB/MLEB; batch 50 con backoff exponencial (rate limits reales vistos en vivo); fallback local (MiniLM 768) solo como fallback, nunca dims mixtas en un indice.
- **Cache**: simple (exacta) vs semantica (cross-encoder); NO cachear consultas que cambian numeros ("15 años" ≠ "20 años"). Cache hit ≈ 0.02¢.
- **1 proveedor = punto único** → gateway (Portkey/LiteLLM): fallback de modelo, load balance 70/30, virtual keys (slugs) en vez de N API keys en .env.
- **Memoria local olvida a las 10-15 preguntas** (MemorySaver); produccion: Mem0/LangMem.
- **No "always retrieve"**: un planner decide si la query es tecnica antes de golpear la BD.
- **LLMs pequeños en produccion** (Haiku, GPT-5-mini, ~27B); fine-tuning no es la primera opcion.
- **Indexado incremental**: source_hash por documento → cambio = pending → reindexa solo (batch periodico) + panel de estado con retry.

## Errores comunes

1. Indexar con un modelo y consultar con otro (o cambiar dims sin re-ingesta) → la query no sabe a que dimension convertir.
2. Sin guardrails: la inyeccion destruye la identidad del bot; el off-topic cuesta dinero.
3. Sin observabilidad: cuando falla, no sabes que span tardo ni que se recupero.
4. Elegir herramientas por estrellas de GitHub en vez del stack del cliente.
5. Meter RAG en flujo determinista → riesgo financiero directo, no es un bug.

## Donde aplicarlo nosotros

- **Piloto 1 (agencia)**: baseline vendible del live = chunks 1500 → embeddings → Qdrant → 15 → FlashRank → top 5 → LLM con citas; pitch = "recall medido (X/20), no de oido" — mismo ADN que el gate de robustez.
- **Portal**: NO RAG en el flujo de pagos (determinista); si acaso, asistente FAQ de backoffice con guardrails PII en fase posterior.
- **Fondo**: NO en el loop de senales (rompe reproducibilidad); soporte interno sobre FUND-PIPELINE/papers si duela. Nunca "AI que gana".

Fuentes: live 8h "Enterprise-Grade RAG" (`AgentBiz/docs/notas-rag-live-2026-10-17.md`) y video ES embeddings (`notas-videos-2026-10-17-lote2.md` §1).