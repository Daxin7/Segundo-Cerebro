\---

name: glosario-materia

description: Extrae todos los términos técnicos y definiciones de los apuntes de una materia y genera un archivo Glosario.md centralizado en esa carpeta

license: MIT

compatibility: opencode

\---



\## Qué hago



\- Escaneo todas las notas .md de la carpeta de la materia actual

\- Extraigo términos técnicos, definiciones, siglas y fórmulas importantes

\- Genero un Glosario.md ordenado alfabéticamente

\- Si ya existe un Glosario.md, lo actualizo sin borrar lo anterior



\## Cuándo usarme



Úsame cuando quieras tener una referencia rápida de todos los términos

de una materia, especialmente antes de un examen.



Comandos que puedes decirme:

\- "genera el glosario de esta materia"

\- "crea el glosario"

\- "actualiza el glosario"



\## Cómo trabajo



1\. Leo recursivamente todos los .md en la carpeta actual y subcarpetas

2\. Identifico: términos en negrita, definiciones explícitas,

&#x20;  siglas entre paréntesis, fórmulas con explicación

3\. Elimino duplicados — si un término aparece varias veces,

&#x20;  uso la definición más completa

4\. Ordeno alfabéticamente

5\. Si existe Glosario.md, comparo y agrego solo lo nuevo



\## Formato de salida



\# Glosario — \[Nombre de la materia]

Última actualización: \[fecha]

Total de términos: \[N]



\---



\## A



\*\*Algoritmo\*\*: Secuencia finita de pasos para resolver un problema.



\## B



\*\*Bagging\*\*: Técnica de ensemble que entrena múltiples modelos

en subconjuntos aleatorios del dataset.

