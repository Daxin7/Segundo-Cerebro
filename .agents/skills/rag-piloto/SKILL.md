---
name: rag-piloto
description: Usar al diseñar, revisar o prometer un piloto o feature RAG (asistente sobre documentos, chat con citas, busqueda semantica) para cliente o para nosotros - antes de elegir stack, fijar metricas o decidir si el flujo necesita RAG. Tambien al diagnosticar un RAG ya montado que responde mal: devuelve NO ENCONTRADO, recupera el chunk equivocado, alucina sobre el contexto o falla solo tras deploy. Senales de que estás a punto de fallar: golden set ausente antes de prometer, indexar con un modelo y consultar con otro, guardrails de PII/inyeccion ausentes, sin rerank, sin observabilidad, o "RAG en todo".
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

## Si ya esta montado y falla: ficha de diagnostico

Rellenar la ficha ANTES de tocar codigo. Campo vacio = no hay diagnostico, hay opinion.

| # | Campo | Como se llena |
|---|---|---|
| 0 | Dato en la fuente | La respuesta esperada — tal cual o con sinonimo declarado — existe en el documento: si/no + linea |
| 1 | Rank del pasaje | Posicion del chunk correcto por similitud sobre TODOS los chunks, no solo el top-k |
| 2 | Etapa rota | Una sola, de la tabla de abajo |
| 3 | Evidencia | chunk id + score, o linea de log. Sin esto se descarta |
| 4 | Fix minimo | Un cambio en la etapa 2. "Mas contexto", "mejor modelo" o "subir k" sin medir no cuenta |
| 5 | Metrica | Misma query antes/despues; el fix solo vale si mueve el numero |

### Etapas y como se verifican

| Etapa | Sintoma | Verificacion | Metrica |
|---|---|---|---|
| Fuente | Dice NO ENCONTRADO y el dato no esta, o esta con otra forma | grep del dato y sus sinonimos en el documento | cobertura del golden set |
| Golden set / grader | Falla 1 de N y la respuesta "parece" correcta pero no coincide | Leer la respuesta a mano; aceptar formatos ("12-nov" = "2026-11-12") | falsos negativos del grader |
| Chunking | El dato existe pero llega partido o truncado | Abrir el chunk recuperado y comparar con la fuente | recall@k del pasaje |
| Embedding | Similitudes parejas (~0.6-0.7) y el pasaje correcto cae en rank 7-14 | Revisar prefijos del modelo y que el modelo tenga capacidad de embedding | rank del pasaje correcto |
| Indice | El documento cambio y la respuesta no | source_hash indexado vs actual | docs pending |
| Retrieval | El pasaje esta en rank 5-15 | Subir k y/o BM25 + rerank | recall@15 vs recall@5 tras rerank |
| Prompt | El pasaje esta en top-k y aun falla | Pasar SOLO el chunk correcto | faithfulness con contexto aislado |
| Generacion | Con contexto aislado acierta, en cadena no | Historial, truncado, temperatura | misma query turno 1 vs turno 10 |
| Operacion | Falla solo tras deploy o en otro entorno | Diff de env vars; logs de los primeros 5 min | error rate post-deploy |

### Orden de triage (barato → caro)

0. ¿El dato existe en la fuente, en la forma que el grader acepta? **No** → es el golden set, no el RAG.
1. ¿El pasaje aparece en top-k? **No** → chunking → embedding → indice → retrieval, en ese orden.
2. ¿Llega al prompt? **No** → presupuesto de contexto; no es el LLM.
3. ¿Lo usa y lo cita? **No** → prompt o generacion.
4. ¿Falla solo en un entorno o tras deploy? → operacion.

### Hechos medidos (RAG local, doc 8 KB, 2026-10-19)

- **Un modelo de chat no sirve como embedding**: `qwen2.5:3b` en Ollama solo declara completion/tools y el servidor responde 501 al pedir embeddings. Verificar con `ollama show <modelo>` → capabilities; usar uno dedicado (`nomic-embed-text`, 768 dims).
- **Recall por string exacto da falsos negativos** cuando el documento repite el hecho con otra forma: 2 de 3 "fallos" iniciales fueron errores del golden set, no del pipeline.
- **nomic-embed-text quiere sus prefijos** `search_query:` / `search_document:`; LangChain no los pone solo.
- **Latencia real**: 3B en CPU = 60-135 s por respuesta, 20 s indexar 14 chunks. No prometer latencia sin medirla.
- Tutoriales viejos rompen con LangChain 1.x (`langchain.text_splitter` → `langchain_text_splitters`): si un demo "no arranca", revisar version antes que logica.

## Donde aplicarlo nosotros

- **Piloto 1 (agencia)**: baseline vendible del live = chunks 1500 → embeddings → Qdrant → 15 → FlashRank → top 5 → LLM con citas; pitch = "recall medido (X/20), no de oido" — mismo ADN que el gate de robustez.
- **Portal**: NO RAG en el flujo de pagos (determinista); si acaso, asistente FAQ de backoffice con guardrails PII en fase posterior.
- **Fondo**: NO en el loop de senales (rompe reproducibilidad); soporte interno sobre FUND-PIPELINE/papers si duela. Nunca "AI que gana".

Fuentes: live 8h "Enterprise-Grade RAG" (`AgentBiz/docs/notas-rag-live-2026-10-17.md`) y video ES embeddings (`notas-videos-2026-10-17-lote2.md` §1).