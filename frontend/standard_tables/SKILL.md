---
name: standard_tables
description: Por defecto usar las tablas estándar de ruibernate (GenericMaster / GenericMasterDetail) al crear páginas o componentes con tabla. Solo si el usuario lo solicita explícitamente se puede usar otra forma.
---

# Frontend – tablas estándar de ruibernate (por defecto)

**Cuándo usar:** Siempre que se vaya a crear una página o componente que muestre datos en una **tabla** (listado, CRUD, maestro-detalle) en un proyecto ruiBernate.

---

## 1. Regla por defecto: tablas estándar de ruibernate

- Al crear una **página o componente con tabla** (listado de entidades, CRUD, grid de datos), se debe usar por defecto las **tablas estándar de ruibernate**:
  - **GenericMaster** – para listados simples (una entidad por fila, CRUD estándar).
  - **GenericMasterDetail** – para vistas maestro-detalle (cabecera + líneas).
- **No** proponer tablas hechas a mano con `useState` + `fetch`/axios, ni otras librerías de tablas (Tabulator, AG-Grid, etc.), a menos que el usuario **lo solicite explícitamente**.
- La opción por defecto es siempre: envolver **GenericMaster** o **GenericMasterDetail** con el `endpoint` y `config` adecuados.

---

## 2. Cuándo sí se puede usar otra forma

- **Solo si el usuario lo pide de forma explícita.** Ejemplos: "quiero una tabla custom con [X librería]", "necesito una tabla que no sea GenericMaster porque [motivo]", "implementa el listado con Tabulator/AG-Grid", etc.
- En ese caso, seguir lo que el usuario indique y respetar el resto de reglas del proyecto (config.js para URLs, no hardcodear, etc.).

---

## 3. Resumen

| Situación | Acción |
|-----------|--------|
| Nueva página/componente con tabla (sin indicación del usuario) | Usar **GenericMaster** o **GenericMasterDetail** (tablas estándar de ruibernate). |
| Usuario pide explícitamente otra forma de tabla | Aplicar lo solicitado (custom, otra librería, etc.). |

**Frase clave:** *Por defecto, tablas = GenericMaster o GenericMasterDetail. Otra opción solo si el usuario lo solicita.*
