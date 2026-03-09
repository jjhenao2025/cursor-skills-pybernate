---
name: module_structure
description: Estructura de módulos frontend: routeMapper.js, componentMap, config.js, dónde van los componentes (pages/). Usar al añadir componentes custom o configurar rutas.
---

# Frontend – estructura de módulos

**Cuándo usar:** Al añadir un componente personalizado que deba aparecer en el menú, al configurar routeMapper o al explicar la estructura del proyecto ruiBernate.

---

## 1. routeMapper.js y componentMap

- Cualquier componente referenciado por **nombre** en la respuesta del backend de rutas (campo `component` en el JSON de `/routes`) debe existir en el **componentMap** de `routeMapper.js` (o equivalente).
- **GenericMaster** y **GenericMasterDetail** suelen estar ya mapeados; al añadir una ruta nueva que los use, no hace falta registrar de nuevo el componente, pero **sí** que el backend devuelva la ruta con `path` y `props.endpoint` correctos.
- **Componentes personalizados** del proyecto deben estar **importados y añadidos** al map en routeMapper (ej. `MiComponente: MiComponente`).
- Si una pantalla no aparece en el menú, revisar: (1) que exista la entrada en la respuesta del backend de rutas (`/routes`); (2) que el `component` esté registrado en el componentMap de routeMapper.js si es custom.

---

## 2. Dónde van los componentes

- Componentes de **páginas** del proyecto: típicamente en `src/pages/` (o la convención del proyecto), organizados por pantalla o entidad.
- **Configuración** central: `config.js` (o equivalente) en `src/config/`; desde ahí se importa `config` en los componentes que necesiten baseUrl o axios.
- **Mapeo de rutas:** `routeMapper.js` (o equivalente) en `src/utils/` o similar; ahí vive el componentMap y la lógica que convierte el JSON de `/routes` en rutas de React Router.

---

## 3. Flujo al crear una nueva pantalla (resumen)

1. **Backend:** Controlador y endpoints de la entidad; entrada en `/routes` con path, component, props.endpoint.
2. **Frontend:** Si es GenericMaster o GenericMasterDetail, crear el componente de página (opcional si el backend ya envía el nombre genérico y el frontend lo resuelve). Si es **componente custom**, crearlo en `pages/` y **registrarlo en routeMapper** en el componentMap.
3. Las rutas se crean **automáticamente** en el frontend al recibir el JSON de `/routes`; no hardcodear la lista de rutas de negocio en el frontend.
