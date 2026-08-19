<%*
const materia = await tp.system.prompt("¿De qué materia son los apuntes?");
const tema = await tp.system.prompt("¿Cuál es el tema o título de esta nota?");
const semana = await tp.system.prompt("¿A qué semana o unidad corresponde? (ej: Semana-01)");
const fecha = tp.date.now("YYYY-MM-DD");

const carpeta = `01-Materias/${materia}/${semana}`;
const nombreArchivo = `${fecha}-${tema.replace(/ /g, "-")}`;

await tp.file.move(`${carpeta}/${nombreArchivo}`);
-%>
---
materia: <% materia %>
tema: <% tema %>
semana: <% semana %>
fecha: <% fecha %>
estado: borrador
---

# <% tema %>
**Materia:** <% materia %>
**Fecha:** <% fecha %>

---

## Apuntes

(escribe aquí)

---

## Conceptos clave

-

---

## Dudas

-