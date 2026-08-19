\---

name: cronograma-examen

description: Genera un plan de estudio día a día desde hoy hasta la fecha del examen, basado en el contenido real de los apuntes de la materia

license: MIT

compatibility: opencode

\---



\## Qué hago



\- Leo todos los apuntes .md de la carpeta actual

\- Evalúo cuánto contenido hay y qué tan completo está

\- Genero un cronograma de estudio realista hasta la fecha del examen

\- Distribuyo los temas según su complejidad y cantidad de apuntes



\## Cuándo usarme



Úsame cuando tengas una fecha de examen confirmada y quieras

organizar el tiempo de estudio que te queda.



Comandos que puedes decirme:

\- "tengo examen el \[fecha], arma el cronograma"

\- "crea el plan de estudio para el examen del \[fecha]"

\- "cuánto tiempo necesito para estudiar esto"



\## Cómo trabajo



1\. Pregunto la fecha exacta del examen si no la mencionaste

2\. Calculo los días disponibles desde hoy

3\. Leo todos los .md de la carpeta y evalúo:

&#x20;  - Cantidad de temas

&#x20;  - Complejidad aparente (fórmulas, muchos subtemas = más tiempo)

&#x20;  - Temas marcados con #revision-pendiente (sin revisar todavía)

4\. Distribuyo los temas en los días disponibles dejando el último

&#x20;  día solo para repaso general

5\. Guardo el cronograma como Cronograma-Examen-\[fecha].md



\## Formato de salida



\# Cronograma de Estudio — Examen \[fecha]

Días disponibles: \[N]

Temas a cubrir: \[N]



\---



\## Lunes 18 de agosto

\- \[ ] Repasar: Tema 1 — Distribuciones de probabilidad

\- \[ ] Repasar: Tema 2 — Media y varianza

\- Tiempo estimado: 2 horas



\## Martes 19 de agosto

\- \[ ] Repasar: Tema 3 — Teorema de Bayes

\- \[ ] Resolver flashcards de la semana 1

\- Tiempo estimado: 1.5 horas



\## \[último día] — Solo repaso general

\- \[ ] Revisar Glosario.md completo

\- \[ ] Repasar flashcards fallidas

\- \[ ] Leer resumen de cada semana

