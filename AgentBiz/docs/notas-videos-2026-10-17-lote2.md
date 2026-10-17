# Notas videos — lote 2 (2026-10-17): RAG + fondo

Tres vídeos de Carlos. Pipeline: transcript → notas → skill si tiene peso → filtro de Medina.
- **Live 8 h** "Build Enterprise-Grade RAG Applications" → notas propias: [`notas-rag-live-2026-10-17.md`](notas-rag-live-2026-10-17.md)
- Aquí: el RAG en español (§1) y el vídeo del fondo (§2 + mapeo de aplicación).

## 1. "Dame 30 Minutos y te enseño a implementar un RAG con Embeddings" (lBimeKh88OU · 30 min · ES)

Caso: chat de soporte de una academia que solo "veía" la lección abierta → RAG sobre todo el catálogo para recomendar lecciones.

### Conceptos (explicados bien, sirven para venderlo)
- **RAG** = Retrieval Augmented Generation: recuperar contexto externo relevante y pasárselo al LLM antes de generar.
- 4 piezas para montarlo: (1) **modelo de embeddings** (doc→vector y prompt→vector), (2) **store** de vectores con vínculo al documento origen (reversibilidad), (3) **métrica de distancia** (define qué significa "cerca"), (4) **query** top-k con umbral.
- Embeddings = documentos como puntos en un espacio n-dimensional; quedan agrupados por significado (lambdas juntos, bases de datos juntas).

### Stack del vídeo
- **Postgres + pgvector**: columna `vector(1024)`; la distancia es un operador SQL (`<=>` coseno, umbral 0.6), top 4, filtrando docs publicados.
- **Modelo**: Amazon Titan V2 (1024 d); compara con OpenAI (1536 d) → **cada modelo tiene sus dimensiones: una tabla/columna por modelo**.
- **Regla de oro**: mismo modelo para indexar y para consultar — mezclar modelos = "peras con manzanas".
- Distancias pgvector: L2 (euclídea), L1 (Manhattan), producto interno, coseno, Hamming, Jaccard. Él usa **coseno** (similitud semántica: médico/enfermero quedan cerca aunque las palabras no se parezcan).

### Operacional (lo que lo hace vendible)
- **Indexado automático**: lambda batch de 20 cada ~2 min; tabla `index_state` con `source_hash` → cambio detectado = pending → reindexa solo; modelos con `status enabled`; panel admin con botón "reindexar".
- **Chunking con solape**: no indexa la lección entera (mezcla temas → ruido semántico); chunks por tokens (alternativa: secciones markdown) que comparten contexto; el chunk más cercano manda y se devuelve la lección madre.
- **Evaluación con golden set**: tabla `rag_eval_question` (pregunta → lección esperada); botón "ejecutar evaluación" → recall, precisión, falsos positivos, fallos de retrieval. Permite comparar modelo/distancia/chunking **con números**. Fase 2: feedback de alumnos como labels.

### Errores que evita
Meter el documento entero en el prompt · indexar con un modelo y consultar con otro · evaluar "ojeando el chat" sin golden set.

### Aplicación a nuestros sistemas
| Destino | Qué tomamos | Cuándo |
|---|---|---|
| **Piloto 1 (agencia, Task 3)** | Stack mínimo réplica: pgvector (o vector store local), chunking con solape, **golden set de 10-20 preguntas + recall/precision ANTES de prometer "asistente AI"** | diseño del piloto |
| **Portal reembolsos** | NO RAG sobre PDFs de clientes ahora; si acaso, asistente de docs con guardrails PII (ver live) | no este mes |
| **AgentBiz** | Nuestra "memoria" actual = docs + grep + grafo de código; un RAG sobre docs se evaluaría con el MISMO truco (golden set de preguntas del dueño) | cuando duela |
| **Venta** | Pitch: "responde con RECALL medido (X/20), no de oído" — mismo ADN que el gate: medir > prometer | ya |

## 2. "Si Tuviera Poco Dinero Para Operar Haría Esto Cada Mañana" (p579vzivHoc · 31 min · ES)

### El método (4 pasos cada mañana)
1. **Dirección — temporalidad grande**: tendencia alcista/bajista/rango con herramientas básicas (soportes/resistencias, aceleración/desaceleración, doble techo, estructuras de velas). **Si es rango → no operar.**
2. **Cuándo unirse — temporalidad intermedia**: pauta básica de Dow (impulso → pullback → continuación); el pullback debe caer en **Fibonacci 1/3 a 2/3** del impulso; fuera de esa zona → no operar.
3. **Tamaño de posición**: `unidades = riesgo$ / (distancia_stop% × 100)` — ej.: arriesgar $10 con stop al 10,25% → 97,56 acciones. Riesgo fijo por trade, no tamaño a ojo.
4. **Ejecución — temporalidad pequeña**: **EMA 50** de disparador: cierre de vela cruzando la EMA50 → entrada; SL en extremo anterior, TP en extremo anterior.

Comparativa de activos: acciones (líquidas/reguladas, capital alto, horario limitado) · futuros (buen apalancamiento, casi 24/5, riesgo alto) · forex (24/5, poco capital, sensible a noticias) · cripto (24/7, poco capital, acceso a tu capital). Él mezclaría **forex + cripto**.

### Aplicación al fondo — mapeo con números
| Idea del vídeo | Estado en Segundo Cerebro Capital |
|---|---|
| 1. Dirección en temporalidad grande, no operar en rango | **Ya probado y rechazado con números**: filtro de régimen ER(20) > 0.3 sobre Donchian(20), walk-forward OOS 31 símbolos con shift(1) legal → base **+15.6%** vs +ER **+10.8%** → no se integra. `backend/regime.py` queda listo (FUND-PIPELINE § Experimento régimen ER) |
| 2. Entrada en pullback 1/3–2/3 | **Hipótesis en experimento** (H1): Donchian + entrada solo en retroceso 33–67% del impulso, shift(1), walk-forward vs base |
| 3. Tamaño por riesgo$ ÷ distancia stop | **Ya implementado**: `size_order` (pérdida determinística en el SL = equity × risk%) — lección del live "Liquidator" (FUND-PIPELINE) |
| 4. Stop en extremo anterior (estructura) | **Hipótesis en experimento** (H2): stop por estructura vs ATR(14) actual, con cap de seguridad, walk-forward vs base |
| 5. Trigger EMA50 intradía | **Fuera de alcance por ahora**: backtester diario; ejecución intradía = Trader API (ya anotado en FUND-PIPELINE) |
| 6. Mix forex+cripto | Universe estático 31; filtro de volumen > $50M ya activo en futuros; ranking por liquidez en backlog |

**Disciplina**: los 2 experimentos son solo backtest/paper — nada se promueve sin pasar el gate (WF OOS + Monte Carlo) y no tocan la config viva. Números → FUND-PIPELINE cuando terminen.

### Lectura honesta
El vídeo es clase discrecional de un canal de cursos; nuestro fondo es sistemático. Lo útil no es copiar su sistema pieza a pieza, sino que **cada pieza encaja como hipótesis medible**: 2 ya aplicadas, 1 ya refutada con números, 2 en cola del gate.

## Estado del lote
- [x] Notas live 8 h → `notas-rag-live-2026-10-17.md`
- [x] Notas RAG ES + fondo (este archivo)
- [ ] Experimento H1/H2 → números pendientes (subagente corriendo)
- [ ] Skill candidata `rag-piloto` → filtro de Medina (tu voto, Carlos)
- Transcripts en `%TEMP%\opencode\videos\txt\` (bjkjaqUZl4E / lBimeKh88OU / p579vzivHoc)