---
name: business_domains
description: Dominios del ecosistema: backend (pybernate) y frontend (ruiBernate), y cómo se conectan. Usar para entender el mapa del sistema antes de tocar código.
---

# Dominios de negocio – backend y frontend

**Cuándo usar:** Para entender el reparto de responsabilidades del proyecto o explicar cómo se relacionan backend y frontend en el ecosistema.

---

## 1. Backend (pybernate)

- **Responsabilidad:** Datos, lógica de negocio, API REST, autenticación, definición de rutas del menú.
- **Stack:** pybernate (ORM compilado con Nuitka), PostgreSQL/SQLite/MySQL, Pydantic v2.
- **Expone:** Endpoints CRUD (`/api/{entidad}`), endpoint de rutas dinámicas (`/routes`), auth (`/auth/login`, `/auth/logout`, `/auth/profile`).
- **No modificar** el código fuente de pybernate; usar sus modelos generados, Query Builder, eventos y cache.

---

## 2. Frontend (ruiBernate)

- **Responsabilidad:** UI, consumo de API, menú y rutas que vienen del backend, componentes CRUD genéricos.
- **Stack:** React, ruiBernate (GenericMaster, GenericMasterDetail, Layer, Login), config.js, routeMapper.
- **No define a mano** la lista de rutas de negocio; las recibe del backend y las genera automáticamente.
- **No modificar** el código fuente de ruiBernate; personalizar en `src/pages/`, config y estilos.

---

## 3. Cómo se conectan

- El **backend** define qué pantallas existen y con qué endpoint se alimentan (respuesta de `/routes` con path, component, props.endpoint).
- El **frontend** llama a `/routes` (tras login), recibe el JSON y construye el menú y las rutas; cada ruta apunta a un componente (GenericMaster, GenericMasterDetail o custom) que usa el `endpoint` indicado en props.
- Por tanto: **una página en el frontend** corresponde a **un controlador/endpoints en el backend** y a **una entrada en la respuesta de `/routes`**.
