---
name: modo-estudio
description: Lee los apuntes en Markdown del directorio actual y genera un resumen con preguntas de preparación para exámenes.
metadata:
  audience: estudiante
---
## Qué hago
1. Escaneo y leo todos los archivos `.md` presentes en el directorio de trabajo actual (los apuntes de clase).
2. Extraigo los conceptos teóricos, reglas clave.
3. Redacto un resumen estructurado usando viñetas.
4. Genero un pequeño cuestionario práctico para validar los conocimientos.

## Cuándo usarme
Úsame automáticamente cuando el usuario pida "modo estudio", "dame un resumen de mis apuntes", o "prepárame para el examen".

## Flujo de Trabajo
1. Analiza los archivos Markdown locales.
2. Identifica de qué trata la materia (ej. estadística, combinatoria, estructuras de datos, etc.).
3. Crea el resumen.
4. Redacta 3 preguntas. Si el tema involucra matemáticas, asegúrate de que los cálculos sean precisos (por ejemplo, si evalúas tamaños de muestra o coeficientes de asimetría, verifica las fórmulas y los redondeos exactos antes de dar la respuesta).