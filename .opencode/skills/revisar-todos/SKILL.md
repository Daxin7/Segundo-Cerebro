\---

name: revisar-todos

description: Lee toda la carpeta 03-To-Dos/, detecta tareas vencidas y próximas a vencer, y muestra un resumen priorizado por urgencia

license: MIT

compatibility: opencode

\---



\## Qué hago



\- Leo todos los archivos .md en 03-To-Dos/

\- Identifico tareas con fechas y sin fechas

\- Las clasifico por urgencia: vencidas, hoy, esta semana, sin fecha

\- Muestro un resumen limpio y priorizado

\- Sugiero en cuáles enfocarte primero



\## Cuándo usarme



Úsame al inicio del día o de la semana para saber qué tienes

pendiente sin tener que abrir cada archivo manualmente.



Comandos que puedes decirme:

\- "qué tengo pendiente"

\- "muéstrame mis tareas"

\- "revisa mis to-dos"

\- "qué está vencido"



\## Cómo trabajo



1\. Leo recursivamente todos los .md en 03-To-Dos/

2\. Busco líneas con formato de tarea: `- \[ ] texto`

3\. Si tienen fecha, las clasifico por urgencia

4\. Si no tienen fecha, las listo al final como "sin fecha"

5\. Detecto `- \[x]` (completadas) y las omito del resumen

6\. Presento el resumen ordenado de más urgente a menos



\## Formato de salida



\# Resumen de To-Dos — \[fecha de hoy]



\## 🔴 Vencidas

\- \[ ] Entregar informe de Estadística \_(venció el 10 ago)\_



\## 🟡 Hoy

\- \[ ] Estudiar Tema 3 de Probabilidad



\## 🟢 Esta semana

\- \[ ] Leer paper de redes neuronales

\- \[ ] Actualizar Glosario de Bases de Datos



\## ⚪ Sin fecha

\- \[ ] Organizar apuntes de semana 2

\- \[ ] Revisar bandeja de entrada

