\---

name: generar-flashcards

description: Lee los apuntes .md de la carpeta actual y genera tarjetas de memoria en formato compatible con el plugin Spaced Repetition de Obsidian

license: MIT

compatibility: opencode

\---



\## Qué hago



\- Leo todos los archivos .md de la carpeta actual

\- Identifico conceptos clave, definiciones, fórmulas y relaciones importantes

\- Genero tarjetas de memoria en formato compatible con Spaced Repetition

\- Guardo las flashcards en un archivo Flashcards.md dentro de la misma carpeta



\## Cuándo usarme



Úsame después de haber tomado y convertido tus apuntes de una semana o tema,

cuando quieras repasar el contenido de forma activa.



Comandos que puedes decirme:

\- "genera las flashcards de estos apuntes"

\- "crea tarjetas de memoria"

\- "quiero repasar con flashcards"



\## Cómo trabajo



1\. Leo todos los .md de la carpeta actual

2\. Identifico: definiciones, conceptos, fórmulas, relaciones causa-efecto

3\. Genero mínimo 10 tarjetas por cada nota leída

4\. No invento información — solo uso lo que está en tus apuntes

5\. Guardo el archivo Flashcards.md en la misma carpeta



\## Formato de salida



El formato debe ser exactamente este para que Spaced Repetition lo reconozca:



\#flashcards



Frente de la tarjeta :: Respuesta de la tarjeta



Ejemplos:

¿Qué es la regresión logística? :: Modelo de clasificación que predice

la probabilidad de una clase usando la función sigmoide



¿Cuál es la fórmula de la varianza? :: σ² = Σ(xi - μ)² / N

