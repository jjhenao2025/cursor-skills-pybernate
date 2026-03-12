---
name: genericmasterdetail_line_items
description: Guía para implementar pantallas maestro-detalle con GenericMasterDetail en ruiBernate, incluyendo manejo de líneas, validaciones y UX básica. Usar cuando una entidad tenga líneas o detalles asociados.
---

# Frontend – maestro-detalle con GenericMasterDetail

## Cuándo usar este skill

Usa este skill cuando:

- La entidad de negocio tenga un **maestro con líneas/detalles** (ej. facturas con ítems, conciliaciones con movimientos, órdenes con líneas).
- Sea necesario mostrar y editar maestro y detalle en una sola experiencia de usuario.
- Tengas que ajustar la configuración de un `GenericMasterDetail` existente.

---

## 1. Modelo de datos esperado

Para que `GenericMasterDetail` funcione de forma consistente:

- El backend debe exponer un endpoint principal (ej. `/api/facturas-venta`) que:
  - Liste registros maestro.
  - Al consultar un maestro concreto (ej. `/api/facturas-venta/{id}`) devuelva:
    - Datos del maestro.
    - Colección de líneas/detalles asociadas.
    - Totales o campos agregados cuando aplique (montos totales, impuestos, diferencias).

- El nombre del campo que contiene las líneas debe ser:
  - Coherente con el dominio (ej. `lineas`, `detalles`, `movimientos`), y
  - Estable (no cambiarlo arbitrariamente entre endpoints).

---

## 2. Configuración básica de GenericMasterDetail

Al configurar `GenericMasterDetail`:

- Definir:
  - **Endpoint maestro** (ej. `/api/facturas-venta`).
  - **Campos de maestro** visibles en la parte superior (código, cliente, fechas, totales).
  - **Tabla de líneas** con sus columnas (producto, cantidad, precio, subtotal, etc.).

- Alinear los nombres de columnas y campos con los nombres del backend; evitar transformaciones innecesarias en el frontend.

---

## 3. Validaciones en el detalle

Buenas prácticas para validación en las líneas:

- Validar:
  - Campos obligatorios (ej. producto, cantidad).
  - Rangos permitidos (cantidades > 0, precios ≥ 0).
  - Consistencia con el maestro cuando aplique (ej. moneda, centro de costo).
- Mostrar errores:
  - Lo más cerca posible del campo (mensajes en línea) cuando sea posible.
  - Complementar con notificación general si la acción afecta muchas líneas.

Si la validación fuerte se hace en backend:

- Asegurarse de que los mensajes de error viajen con suficiente detalle para mapearlos a la UI (campo, descripción).

---

## 4. Acciones sobre maestro y detalle

Al diseñar acciones:

- **Sobre el maestro**:
  - Guardar/Borrar/Anular/Confirmar según el flujo de negocio.
  - Acciones de workflow (aprobar, rechazar, conciliar, etc.).

- **Sobre las líneas**:
  - Agregar nueva línea.
  - Duplicar línea (si el negocio lo admite).
  - Eliminar una o varias líneas.

Recomendaciones:

- Deshabilitar acciones que no apliquen según el **estado del maestro**.
- En flujos críticos (ej. conciliación, aprobación), pedir confirmación clara antes de cambios irreversibles.

---

## 5. UX recomendada

Para mejorar la experiencia de usuario:

- Mantener el **maestro siempre visible** mientras se trabaja con las líneas.
- Mostrar **resúmenes** (totales, contadores, diferencias) actualizados cuando cambian las líneas.
- Evitar recargar toda la pantalla por cambios pequeños en el detalle (idealmente usar actualizaciones parciales).

---

## 6. Relación con backend

Este skill asume:

- Backend implementado con pybernate que:
  - Expone maestro y detalle en un formato consistente.
  - Maneja la lógica de negocio fuerte (reglas de estados, cálculos complejos).

Al diseñar o modificar la API:

- Mantener el contrato de datos estable para no romper la configuración de `GenericMasterDetail`.

