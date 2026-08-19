\---

name: reporte-semanal

description: Genera un reporte semanal del vault — notas creadas, tareas pendientes, materias activas y pendientes de revisión

license: MIT

compatibility: opencode

\---



\## Qué hago



\- Escaneo el vault completo buscando actividad de la semana actual

\- Genero un reporte con notas nuevas, tareas pendientes y materias activas

\- Identifico archivos con la etiqueta #revision-pendiente

\- Sugiero en qué enfocarte la próxima semana



\## Cuándo usarme



Úsame los viernes o fines de semana para hacer balance de la semana.



Comandos que puedes decirme:

\- "dame el reporte semanal"

\- "qué hice esta semana"

\- "qué tengo pendiente"



\## Cómo trabajo



1\. Leo la fecha actual y calculo el rango de la semana

2\. Busco archivos creados o modificados en ese rango

3\. Cuento notas por carpeta (cuántas por materia, cuántas personales, etc.)

4\. Listo tareas pendientes en 03-To-Dos/

5\. Identifico notas con #revision-pendiente (fotos convertidas sin revisar)

6\. Genero el reporte en formato Markdown



\## Formato de salida



\# Reporte Semanal — \[fecha inicio] al \[fecha fin]



\## Notas creadas esta semana

(lista por carpeta)



\## Tareas pendientes

(lista de 03-To-Dos/)



\## Materias activas

(materias con actividad esta semana)



\## Por revisar

(archivos con #revision-pendiente)



\## Sugerencia para la próxima semana

(basada en lo pendiente detectado)

