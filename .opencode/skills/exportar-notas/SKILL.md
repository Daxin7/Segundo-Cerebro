\---

name: exportar-notas

description: Unifica todas las notas .md de una materia o carpeta en un solo documento Markdown limpio, listo para compartir o imprimir

license: MIT

compatibility: opencode

\---



\## Qué hago



\- Leo todas las notas .md de la carpeta actual

\- Las uno en un solo archivo ordenado y limpio

\- Elimino etiquetas internas (#revision-pendiente, etc.)

\- Genero el archivo unificado listo para compartir con compañeros



\## Cuándo usarme



Úsame cuando quieras compartir tus apuntes con alguien, imprimir

un resumen, o tener una vista unificada de toda la materia.



Comandos que puedes decirme:

\- "exporta los apuntes de esta materia"

\- "une todas las notas en un solo archivo"

\- "quiero compartir estos apuntes"



\## Cómo trabajo



1\. Leo todos los .md de la carpeta actual (incluyendo subcarpetas)

2\. Los ordeno cronológicamente por nombre de archivo

3\. Uno el contenido con separadores claros entre notas

4\. Limpio: elimino etiquetas internas, frontmatter técnico,

&#x20;  y referencias que solo tienen sentido dentro del vault

5\. Guardo como Apuntes-Completos-\[materia]-\[fecha].md en \_Attachments/



\## Formato de salida



\# Apuntes Completos — \[Nombre de la materia]

Exportado el: \[fecha]

Semanas incluidas: \[N]

Total de notas: \[N]



\---



\## Semana 1 — \[fecha]



\[contenido de la nota 1]



\---



\## Semana 2 — \[fecha]



\[contenido de la nota 2]

