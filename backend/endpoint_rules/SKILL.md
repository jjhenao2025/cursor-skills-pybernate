---
name: endpoint_rules
description: Convención de endpoints CRUD, nombrado y formato de respuestas/errores en el backend. Usar al diseñar o implementar endpoints REST.
---

# Backend – reglas de endpoints

**Cuándo usar:** Al crear o modificar endpoints REST de entidades, nombrado de rutas o formato de respuesta en un proyecto pybernate.

---

## 1. Endpoints CRUD de entidades

- **Colección:** `GET /api/{entidad}` (lista), `POST /api/{entidad}` (crear). Entidad en **minúsculas** y **plural** (ej. `/api/productos`, `/api/clientes`).
- **Recurso por ID:** `GET /api/{entidad}/{id}`, `PUT /api/{entidad}/{id}`, `DELETE /api/{entidad}/{id}` (obtener, actualizar, eliminar).
- La respuesta debe ser coherente con lo que espera el frontend (GenericMaster/GenericMasterDetail): **array de objetos** para listas, paginación si aplica, errores en formato estándar. No inventar rutas ni formatos que rompan ese contrato.

---

## 2. Nombrado (backend)

- **Python:** snake_case para funciones, variables y módulos; PascalCase para clases. Entidades: modelo en singular (ej. `Producto`), tabla en plural si aplica (ej. `productos`).
- **Endpoints:** minúsculas, plural para colecciones: `/api/productos`, `/api/clientes`. ID en path: `/api/productos/123`.
- Evitar abreviaturas oscuras; nombres que reflejen el dominio.

---

## 3. Respuestas exitosas y códigos HTTP

- **200:** OK (GET que devuelve dato(s)).
- **201:** Created (POST que crea recurso).
- **204:** No Content (DELETE exitoso, sin cuerpo).
- Cuerpo: JSON con los datos esperados (objeto o array). Estructura estable para listas (array de objetos) y paginación si el proyecto la usa.

---

## 4. Errores (cuerpo y códigos)

- Estructura fija de error en JSON, p. ej. `{ "error": "mensaje corto", "detail": "detalle opcional" }` (o la que tenga el proyecto).
- **400:** Bad Request (validación de negocio o parámetros).
- **404:** Not Found (recurso no existe).
- **422:** Unprocessable Entity (validación de entrada, p. ej. Pydantic).
- **500:** Internal Server Error (error no controlado).
- En validación (Pydantic v2), devolver **422 o 400** con el **detalle de los campos** que fallan. No exponer trazas internas en producción.
