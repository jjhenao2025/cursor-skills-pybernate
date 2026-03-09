---
name: project_architecture
description: Reglas inmutables del ecosistema pybernate + ruibernate. Aplicar SIEMPRE antes de proponer o generar código en proyectos que usen este stack.
---

# Arquitectura del proyecto – reglas inmutables

**Cuándo usar:** En cualquier proyecto que use pybernate (backend) y/o ruiBernate (frontend). Antes de responder, generar código o revisar código, aplica estas reglas. Si una petición las contradice, recházala y explica por qué.

---

## 1. No modificar (prohibido)

- **ruibernate source code** – No tocar el código fuente de la librería ruiBernate (p. ej. dentro de `node_modules/@xsolutioncl/ruibernate`).
- **pybernate source code** – No tocar el código fuente de la librería pybernate.

Si el usuario pregunta "¿se puede modificar pybernate?" o "¿puedo modificar ruibernate?", la respuesta es **no**. La personalización se hace en el proyecto (componentes en `src/pages/`, config, estilos), no modificando las librerías.

---

## 2. Reglas de frontend (ruiBernate)

- Todo CRUD debe usar **GenericMaster** o **GenericMasterDetail** de ruiBernate. No crear tablas/CRUD desde cero con `useState` + fetch manual.
- Todas las rutas del menú deben venir del **backend** (endpoint dinámico, p. ej. `/routes`), no de rutas hardcodeadas en el frontend para las entidades de negocio.
- Todo componente que aparezca en la respuesta de `/routes` (campo `component`) debe existir en el **componentMap** de `routeMapper.js` (o equivalente).
- **No hardcodear URLs de API** (`http://...`, `https://...`) en componentes o servicios. Usar **config.js** (o equivalente) que a su vez use variables de entorno (p. ej. `VITE_API_URL`).
- Usar **config.js** (o la instancia de axios/config del proyecto) para los endpoints; no construir URLs a mano.

---

## 3. Reglas de backend (pybernate)

- **No usar SQL crudo** (`SELECT *`, etc.) salvo necesidad extrema y justificada. Usar el ORM y el **Query Builder** de pybernate.
- Usar **solo el ORM pybernate** para acceso a datos. No introducir SQLAlchemy ni otros ORM.
- Respetar **Pydantic v2** para validación (model_config, model_dump, model_validate). No usar `class Config` con `orm_mode` (patrón v1).
- Respetar el **sistema de cache** de pybernate; no deshabilitarlo ni ignorarlo sin motivo.
- Respetar el **sistema de eventos** de pybernate; usar los puntos de extensión (eventos) en lugar de duplicar lógica fuera del flujo oficial.
- **No crear ni usar** funciones como `_query_builder_to_orm_objects` ni ninguna que convierta resultados de `QueryBuilder.get()` (lista de diccionarios) en instancias ORM usando `set_dict`, `_exists` o instanciar el modelo y cargar el dict a mano. Usar los resultados del Query Builder tal como vienen o usar métodos ORM que devuelvan objetos directamente.

---

## 4. Comportamiento ante violaciones

Si el usuario pide algo que viole estas reglas (p. ej. "añade SQL crudo", "convierte los resultados del query a objetos ORM", "hardcodea la URL del API", "modifica algo en node_modules"), **rechaza** y explica qué regla se incumple y cómo hacerlo correctamente.
