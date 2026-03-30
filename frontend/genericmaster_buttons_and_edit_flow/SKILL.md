---
name: genericmaster-buttons-and-edit-flow
description: Estandariza la implementacion de botones de tabla y flujo de edicion en pantallas CRUD con GenericMaster o GenericMasterDetail. Usar cuando se creen o corrijan botones Editar/Eliminar/acciones custom, o cuando haya errores como POST /undefined o pantalla de edicion en blanco.
---

# GenericMaster Buttons and Edit Flow

## Objetivo

Definir un contrato claro para que botones y edición funcionen de forma consistente en vistas basadas en `GenericMaster` y `GenericMasterDetail`.

## Regla 1: Elegir el componente correcto

- Usa `GenericMaster` para grillas simples (sin flujo maestro-detalle).
- Usa `GenericMasterDetail` para endpoints `gmd/*` y pantallas con detalle/edición de registro.
- No mezclar endpoint `gmd/*` con comportamientos pensados para `GenericMaster` sin validar el flujo de edición.

## Regla 2: Contrato de botones (backend)

En `buttons_master`, usar estructura consistente:

- Editar:
  - `action: "edit"`
  - `only_selected_rows: true`
- Eliminar:
  - `action: "delete"`
  - `only_selected_rows: true`
  - `danger: true`

Para acciones custom que sí llaman API o ruta:

- usar `path` explícito (ej: `"/conciliacion"` o `"/api/..."`)
- no usar `action` con valores de ruta.

## Regla 3: Evitar path undefined en frontend

Al renderizar:

- pasar `...props` antes de `path` y `pk`
- dejar `path`/`pk` al final para que no sean sobreescritos accidentalmente.

Patrón recomendado:

```jsx
<GenericMasterDetail
  {...props}
  pk="cod_documento"
  path={path}
/>
```

## Regla 4: Convenciones de URL de edición

La URL de detalle debe ser:

- `.../gmd/<entidad>/details/<pk>`

No debe quedar:

- `.../gmd/<entidad>?filtro=.../details/<pk>`

Si aparece ese patrón, revisar cómo se construye `path` en frontend y aplicar fallback defensivo en backend solo como compatibilidad temporal.

## Diagnóstico rápido

### Síntoma: `POST /undefined`

Revisar en este orden:

1. Botón custom sin `path`.
2. `action` usado como ruta (incorrecto).
3. `path` pisado por `...props` en el componente.
4. Configuración inconsistente entre `GenericMaster` y endpoint `gmd/*`.

### Síntoma: editar abre en blanco

Revisar:

1. Request de detalle (debe ser `/details/<pk>`).
2. Respuesta de backend con estructura de `get_master_detail`.
3. `detail_config` y `header_layout` del controlador.

## Checklist antes de cerrar cambio

- [ ] Botones edit/delete con `action` correcto.
- [ ] Acciones custom con `path` explícito.
- [ ] Componente correcto (`GenericMaster` vs `GenericMasterDetail`).
- [ ] `path` final no sobreescrito por props.
- [ ] Editar vía botón funciona.
- [ ] Editar vía doble click funciona.
- [ ] Sin `POST /undefined` en consola/terminal.
