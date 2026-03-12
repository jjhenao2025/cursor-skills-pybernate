---
name: genericmaster_config_patterns
description: Patrones para configurar columnas, filtros, acciones y paginación de GenericMaster y GenericMasterDetail en ruiBernate. Usar al crear o modificar pantallas de listado/CRUD en el frontend.
---

# Frontend – patrones de configuración para GenericMaster

## Cuándo usar este skill

Usa este skill siempre que:

- Crees una **nueva pantalla de listado/CRUD** basada en `GenericMaster` o `GenericMasterDetail`.
- Tengas que **ajustar columnas, filtros o acciones** de una pantalla existente.
- Quieras revisar si una nueva pantalla puede resolverse con `GenericMaster` en lugar de un componente custom.

Este skill **complementa** las reglas globales de `ruibernate_rules` y `frontend_backend_sync`.

---

## 1. Checklist para una nueva pantalla con GenericMaster

Al definir una nueva pantalla de listado:

1. **Confirmar endpoint de backend**
   - Debe existir un endpoint tipo `/api/{entidad}` en minúsculas (ej. `/api/facturas-venta`, `/api/clientes`).
   - El endpoint debe devolver datos en un formato tabular razonable para listar.

2. **Definir columnas básicas**
   - Identificador principal (ej. `id`, número de documento, código).
   - Campos de negocio clave (ej. cliente, descripción, monto, moneda).
   - Fechas relevantes (emisión, vencimiento, última actualización).
   - Estado principal (ej. pendiente, conciliado, anulado).

3. **Definir filtros mínimos**
   - Rango de fechas principal (emisión o movimiento).
   - Estado (multi–select si aplica).
   - Texto libre (por código/número/documento).
   - Algún filtro de entidad relacionada frecuente (cliente/proveedor, cuenta, etc.) si el dominio lo usa mucho.

4. **Acciones estándar**
   - Ver detalle.
   - Editar (si la entidad es editable).
   - Eliminar/Anular (según reglas de negocio).
   - Acciones adicionales de negocio (ej. Conciliar, Aprobar, Reversar) como acciones de fila o masivas.

5. **Paginación y orden**
   - Paginación activada por defecto.
   - Orden inicial coherente (ej. fecha descendente o número descendente).

---

## 2. Patrones de columnas

Al definir columnas para `GenericMaster` / `GenericMasterDetail`:

- **Nombres claros**: usar labels entendibles para usuario final, no nombres de campo técnicos.
- **Orden recomendado**:
  1. Identificador/número principal
  2. Información de contraparte (cliente/proveedor)
  3. Datos económicos (monto, moneda)
  4. Fechas
  5. Estado
  6. Acciones
- **Anchos razonables**:
  - Columnas de texto corto (códigos, estados) → ancho reducido.
  - Descripciones largas → permitir ancho mayor y truncar con tooltip si es necesario.
- **Formato consistente**:
  - Fechas en formato uniforme para todo el sistema.
  - Montos con separador de miles y decimales definidos por el negocio.

---

## 3. Patrones de filtros

Al configurar filtros:

- Incluir siempre al menos:
  - **Rango de fecha** principal.
  - **Estado** (cuando exista).
  - **Texto libre** para búsqueda rápida.
- Evitar crear 10+ filtros poco usados; priorizar los que el usuario de negocio usará a diario.
- Cuando la entidad tenga muchas combinaciones posibles, preferir:
  - 2–4 filtros muy potentes (fecha, estado, contraparte, tipo).
  - Y luego filtros secundarios opcionales si se detecta necesidad real.

---

## 4. Acciones y UX mínima

Para acciones de fila:

- Incluir iconos y tooltips descriptivos.
- Respetar siempre las reglas de negocio:
  - Deshabilitar/ocultar acciones que no aplican según el **estado actual**.
  - Confirmar acciones destructivas (anular, eliminar) con diálogo de confirmación.
- Para acciones masivas:
  - Pedir confirmación indicando cuántos registros serán afectados.
  - Mostrar feedback claro de éxito o error (notificaciones).

---

## 5. Cuándo NO usar GenericMaster “puro”

Aunque GenericMaster es el estándar, considera envolverlo o extenderlo cuando:

- La pantalla requiera **mucha interacción visual custom** (gráficos, paneles laterales complejos, dashboards).
- La lógica de filtros o acciones sea muy específica y no encaje bien con la configuración estándar.

En esos casos:

- Mantén a `GenericMaster` como **núcleo de tabla y filtros**.
- Añade componentes alrededor (resúmenes, gráficos, panel de detalles) sin romper las reglas de ruibernate (rutas desde backend, uso de `config`, etc.).

