---
name: ruibernate_rules
description: Reglas para frontend con ruiBernate (GenericMaster, GenericMasterDetail, config.js, variables de entorno). Incluye criterios de validación de código frontend.
---

# Frontend – reglas ruiBernate

**Cuándo usar:** Al crear o modificar páginas, componentes que consuman API, rutas, menú o configuración de la app en un proyecto ruiBernate.

---

## 1. Componentes CRUD

- Todo listado/CRUD de entidades debe usar **GenericMaster** (lista simple) o **GenericMasterDetail** (maestro-detalle con líneas).
- No crear tablas CRUD desde cero con `useState` + `fetch`/axios manual para entidades que pueden resolverse con estos componentes.
- Para maestro-detalle (ej. órdenes con líneas): `GenericMasterDetail` con `endpoint="/api/ordenes"` (o la entidad que corresponda). Siempre pasar `config={config}`.

---

## 2. Endpoint en frontend

- El **endpoint** debe ser el patrón del backend: `/api/{entidad}` en minúsculas (ej. `/api/productos`, `/api/clientes`). No hardcodear la base URL; `config` ya lleva la base y la instancia de axios configurada.
- Importar `config` desde el archivo central del proyecto (ej. `../config/config` o la ruta que use el proyecto).

---

## 3. config.js y variables de entorno

- **Todas** las URLs base de API y endpoints configurables deben venir de **config.js** (o equivalente), que use variables de entorno (ej. `VITE_API_URL`). **Nunca** hardcodear `http://...` o `https://...` en componentes o servicios.
- Secretos y tokens **no** deben estar en el código frontend; variables de entorno solo para lo que sea seguro exponer en el cliente (ej. URL pública del API).
- Si piden "añadir un nuevo endpoint" o "llamar a la API X", usar la instancia de axios/config que ya tenga la baseUrl y, si hace falta, documentar la ruta en config o en variables de entorno.

---

## 4. Validación de código frontend (qué rechazar o advertir)

- **Error:** URLs hardcodeadas (`http://`, `https://`). Debe usarse config.js para endpoints.
- **Error:** Referencias o modificaciones a `node_modules` (no modificar código de ruiBernate ni pybernate).
- **Advertencia:** Uso de `fetch()` directo cuando el proyecto tiene axios/config; preferir la instancia configurada.
- **Advertencia:** Componente que implementa CRUD con `useState` + llamadas a API cuando podría usarse GenericMaster o GenericMasterDetail.
