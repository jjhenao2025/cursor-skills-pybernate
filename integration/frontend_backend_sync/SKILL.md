---
name: frontend_backend_sync
description: El backend crea las rutas; el frontend las recibe y las crea automáticamente. Una página = un controlador. Usar al añadir pantallas o explicar el flujo de sincronización.
---

# Integración – sincronización frontend ↔ backend

**Cuándo usar:** Al crear una nueva pantalla/entidad, al explicar cómo funcionan las rutas en el proyecto, o cuando pregunten por la relación entre páginas, controladores y menú.

---

## 1. Una página → un controlador

- Por cada **página** o pantalla de la aplicación (ej. listado de productos, órdenes maestro-detalle) debe existir un **controlador** (o endpoints asociados) en el **backend** que atienda la lógica y los datos de esa pantalla.
- El controlador expone los endpoints CRUD de la entidad (GET/POST /api/entidad, GET/PUT/DELETE /api/entidad/:id) y/o lógica específica que la página necesite.
- No crear páginas en el frontend que llamen a APIs o rutas que no tengan un controlador definido en el backend.

---

## 2. Las rutas las define el backend

- El **backend** es quien define qué rutas/pantallas existen y cómo aparecen en el menú.
- El backend expone un endpoint de **rutas dinámicas** (normalmente `GET /routes` o equivalente) que devuelve un JSON con la estructura del menú: qué ítems hay, qué path tiene cada uno, qué componente se debe mostrar y con qué props (p. ej. `endpoint` para GenericMaster/GenericMasterDetail).
- Ese JSON incluye, para cada pantalla de entidad: **path** (ej. `/gm/productos`), **component** (ej. `"GenericMaster"`), **props** (ej. `{ "endpoint": "/api/productos" }`), **label**, **icon**, etc.
- Al añadir una **nueva página/entidad**: (1) crear o tener el controlador en el backend que atienda esa entidad; (2) **añadir la entrada correspondiente en la respuesta del endpoint de rutas** (`/routes`) con el path, component y props adecuados.

---

## 3. En el frontend las rutas se crean automáticamente

- El **frontend no define a mano** la lista de rutas de negocio (productos, clientes, órdenes, etc.). Esas rutas **vienen del backend**.
- Flujo típico: el usuario inicia sesión → el frontend llama a `GET /routes` (o al endpoint que el proyecto use) → recibe el JSON de menú/rutas → el frontend (p. ej. mediante `routeMapper.js` y el router) **genera las rutas automáticamente** a partir de ese JSON.
- Por tanto: **no** hardcodear en el frontend un listado de rutas para cada entidad; **sí** asegurar que el backend devuelva en `/routes` todas las pantallas que deben verse, con `path`, `component` y `props.endpoint` correctos.
- Si una pantalla no aparece en el menú, revisar que exista su entrada en la respuesta del backend de rutas y que el `component` esté registrado en el `componentMap` de `routeMapper.js` (si es un componente custom).

---

## 4. Resumen del flujo al añadir una nueva pantalla

1. **Backend:** Crear o tener el **controlador** (y los endpoints) de la entidad (ej. `/api/productos`).
2. **Backend:** **Añadir la ruta** en la respuesta del endpoint de rutas (`/routes`): path (ej. `/gm/productos`), component (ej. `"GenericMaster"`), props (ej. `{ "endpoint": "/api/productos" }`), label, icon, etc.
3. **Frontend:** Si la pantalla usa GenericMaster o GenericMasterDetail, no hace falta registrar el componente (ya están en routeMapper); las rutas se crean solas al recibir el JSON. Si la pantalla usa un **componente custom**, además hay que **registrarlo en routeMapper.js** en el `componentMap`.

Así se cumple: **una página → un controlador**, **las rutas las crea el backend** y **en el frontend se crean automáticamente** a partir de lo que devuelve el backend.
