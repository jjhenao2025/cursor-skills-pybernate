---
name: api_contracts
description: Contrato de API entre backend y frontend: formato de /routes, códigos HTTP, respuestas y errores, autenticación. Usar al diseñar o revisar la integración.
---

# Integración – contrato de API

**Cuándo usar:** Al crear o modificar el endpoint de rutas (`/routes`), respuestas JSON, manejo de errores o autenticación en un proyecto pybernate + ruibernate.

---

## 1. Endpoint de rutas dinámicas (`/routes`)

- El backend debe exponer un endpoint (p. ej. `GET /routes`) que devuelva un **JSON** con la estructura que espera ruiBernate. Campos principales:
  - **type:** `"route"` | `"menu"` | `"layout"`.
  - **path:** ruta (ej. `"/gm/productos"`, `"/home"`).
  - **label:** texto del menú.
  - **component:** nombre del componente (ej. `"GenericMaster"`, `"GenericMasterDetail"`, `"Home"`). Debe existir en el componentMap de routeMapper.js.
  - **icon:** nombre del icono (ej. `"ShoppingOutlined"`).
  - **insideBar:** boolean.
  - **props:** objeto; para GenericMaster/GenericMasterDetail debe incluir **`endpoint`** (ej. `"/api/productos"`).
  - **children:** array para menús anidados o layouts.
- Cada nueva entidad o pantalla que use GenericMaster o GenericMasterDetail debe quedar **reflejada en esa respuesta** (ruta y `props.endpoint` correctos) para que el frontend las muestre en el menú.
- No inventar campos ni estructuras que el frontend no espere; seguir el contrato de la guía de agentes de ruiBernate.

---

## 2. Respuestas exitosas y códigos HTTP

- **200:** OK (GET que devuelve dato(s)).
- **201:** Created (POST que crea recurso).
- **204:** No Content (DELETE exitoso, sin cuerpo).
- Cuerpo: JSON con los datos esperados (objeto o array). Estructura estable para listas (array de objetos) y paginación si el proyecto la usa.

---

## 3. Errores (cuerpo y códigos)

- Estructura fija de error en JSON, p. ej. `{ "error": "mensaje corto", "detail": "detalle opcional" }` (o la que tenga el proyecto).
- **400:** Bad Request. **404:** Not Found. **422:** Unprocessable Entity (validación Pydantic). **500:** Internal Server Error.
- En validación (Pydantic v2), devolver **422 o 400** con el detalle de los campos que fallan. No exponer trazas internas en producción.

---

## 4. Autenticación y rutas protegidas

- El backend debe exponer los endpoints que el frontend espera para auth, p. ej. `/auth/login`, `/auth/logout`, `/auth/profile`, y devolver **token** y datos de **perfil** en el formato que usa el cliente.
- El endpoint **`/routes`** debe devolver rutas **según el usuario autenticado** (permisos/rol) para que el menú dinámico muestre solo lo permitido.
- Los endpoints de datos deben **validar token o sesión** cuando corresponda; no exponer datos sensibles sin verificar autenticación.
- **No** hardcodear credenciales ni secretos en código.
