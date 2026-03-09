---
name: page_generation
description: Cómo generar una página con GenericMaster o GenericMasterDetail; patrón de componente por entidad. Usar al añadir una nueva pantalla de listado o maestro-detalle.
---

# Frontend – generación de páginas

**Cuándo usar:** Al crear una nueva pantalla de listado (master) o maestro-detalle en un proyecto ruiBernate.

---

## 1. Patrón de componente por entidad (GenericMaster)

- Un componente por entidad que envuelve GenericMaster con el endpoint correcto y config:

```jsx
import { GenericMaster } from "@xsolutioncl/ruibernate";
import config from "../config/config";

export default function ProductoMaster() {
    return (
        <GenericMaster
            endpoint="/api/productos"
            config={config}
        />
    );
}
```

- Sustituir `ProductoMaster` y `/api/productos` por el nombre de la entidad en minúsculas (ej. `ClienteMaster`, `/api/clientes`). El endpoint debe coincidir con el que expone el backend.

---

## 2. Patrón para maestro-detalle (GenericMasterDetail)

- Para entidades con cabecera y líneas (ej. órdenes, facturas):

```jsx
import { GenericMasterDetail } from "@xsolutioncl/ruibernate";
import config from "../config/config";

export default function OrdenMasterDetail() {
    return (
        <GenericMasterDetail
            endpoint="/api/ordenes"
            config={config}
        />
    );
}
```

- Siempre pasar `config={config}`. El backend debe devolver los datos en el formato que GenericMasterDetail espera (cabecera + líneas).

---

## 3. Coordinación con backend

- La **ruta** y el **componente** que muestra esta página los define el backend en la respuesta de `/routes` (path, component, props.endpoint). Si la página usa GenericMaster o GenericMasterDetail, normalmente no hace falta registrar el componente en routeMapper (ya están); sí asegurar que el backend incluya la entrada en `/routes` con el `endpoint` correcto.
- Si la página usa un **componente custom**, hay que registrarlo en el componentMap de routeMapper.js y que el backend envíe su nombre en el campo `component` de `/routes`.
