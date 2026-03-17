---
name: financial_reporting
description: Patrones y convenciones para reportes financieros (Estado de Resultado, Balance, KPIs) usando pybernate y consumidos por ruiBernate.
---

# Backend – reportes financieros con pybernate

**Cuándo usar:**  
Al crear o modificar **Estados de Resultado**, balances, KPIs financieros, o cualquier reporte contable que use cuentas contables, layers contables y/o centros de costo.

---

## 1. Principios generales

- **Sin SQL crudo**, salvo casos extremos y muy justificados.  
  Usar siempre pybernate + Query Builder; si no alcanza, documentar claramente la excepción.
- Los reportes deben ser **idempotentes, solo lectura** y no modificar datos.
- El contrato debe estar **pensado para frontend**: estructura clara (`rows`, `columns`, `values`) y sin lógica de negocio embebida en el front.

---

## 2. Manejo de cuentas contables

- Usar siempre los modelos generados por pybernate:
  - `cuenta_contable`, `tipo_cuenta_contable`, `detalle_voucher_contable`, `voucher_contable`, `centro_costo`, etc.
- No redefinir entidades en otros módulos; importar desde el paquete `orm/flujappi/public`.
- Para clasificar cuentas:
  - Primero usar `tipo_cuenta_contable.desc_tipo_cuenta_contable` (ej. contiene `INGRESO`, `COSTO`, `GASTO`).
  - Si no es suficiente, usar **fallback por prefijo de número de cuenta** (ej. `31xx`, `32xx`, `41xx`, `42xx`, `43xx`, `44xx`), documentando la regla aplicada en el controlador.

---

## 3. Signos y cálculo de montos

- Para reportes de resultado (EERR):

  - Monto base por cuenta:  
    \( monto = \text{haber} - \text{debe} \)  
    sobre `detalle_voucher_contable` + `voucher_contable`.

  - Para **ingresos**: valores positivos indican mayor ingreso.  
  - Para **costos y gastos**: se pueden mostrar como negativos o positivos según el diseño del reporte, pero siempre con una **regla única por endpoint** (no mezclar criterios en el mismo reporte).

- Hacer la agregación con `SUM` a nivel de base de datos y solo sumar/restar en Python para:
  - Totales por sección.
  - KPI compuestos (ej. Ganancia bruta, Resultado operacional, Resultado antes de impuestos).

---

## 4. Layout de Estado de Resultado

Cuando se construya un Estado de Resultado, seguir este patrón de filas lógicas (se pueden omitir las que no tengan datos, pero mantener el orden):

- Filas **`group`** (divisiones principales):
  - `Ingresos de actividades ordinarias`
  - `Costos de venta`
  - `Ganancia bruta`
  - `Otros ingresos`
  - `Costos de distribución`
  - `Gastos de administración`
  - `Otros gastos por función`
  - `Costos financieros`
  - `Ganancias (pérdidas) de actividades operacionales`
  - `Ganancia (pérdida), antes de impuestos`
  - `Ganancia (pérdida)`

- Filas **`detail`**: cuentas específicas (310101, 410101, 430101, 440101, etc.).

### 4.1 Reglas de duplicidad

- Si una categoría (ej. `Ingresos de actividades ordinarias`) queda asociada a **un solo prefijo/cuenta** (ej. `3101`):
  - **No** crear otra fila azul para `3101`.  
  - Dejar **solo** la fila azul de la categoría y, si se desea, los detalles de cuentas debajo.

- Si un subgrupo por prefijo (ej. `4401`) contiene una única cuenta cuyo código es exactamente ese prefijo:
  - Evitar filas redundantes (`4401` como grupo y `4401` como detalle); la categoría principal ya expone el total.

---

## 5. Estructura de respuesta recomendada

Para tablas financieras (EERR, balances), usar esta forma general:

```python
{
  "anio": 2025,
  "centro_costo": {
    "cod_centro_costo": 1,
    "desc_centro_costo": "CC Ventas",
  } | None,
  "columns": ["01", "02", "03", "04", "05", "06", "07", "08", "09", "10", "11", "12", "TOTAL"],
  "rows": [
    {
      "type": "group",   # "group" | "detail"
      "label": "Ingresos de actividades ordinarias",
      "cuenta_codigo": None,
      "cuenta_descripcion": None,
      "values": { "01": 0.0, ..., "12": 0.0, "TOTAL": 0.0 },
    },
    {
      "type": "detail",
      "cuenta_codigo": "310101",
      "cuenta_descripcion": "Ingresos por Ventas",
      "values": { "01": 0.0, ..., "12": 0.0, "TOTAL": 0.0 },
    },
    # ...
  ],
  "totales": { "01": 0.0, ..., "12": 0.0, "TOTAL": 0.0 },
  "empresa_nombre": "Mi Empresa SPA",
}
```

- `columns` y `values` deben alinear siempre en sus llaves; no agregar campos sueltos que el frontend deba interpretar por convención.

---

## 6. Filtros estándar para reportes

- Parámetros recomendados:
  - `anio` **o** `fecha_desde` + `fecha_hasta` (`YYYY-MM-DD`).
  - `cod_layer_contable` (1 = Financiero contable, 2 = Tributario, 3 = Otro).
  - `cod_centro_costo` opcional.
  - `rut_contribuyente` opcional (por defecto, `user.cod_empresa`).

- Validar siempre:
  - Formato de fechas.
  - Rango consistente (`fecha_desde <= fecha_hasta`).
  - Que el `centro_costo` pertenezca al contribuyente del usuario.

---

## 7. Integración con ruiBernate / frontend

- Los reportes financieros **no** deben usar `GenericMaster`/`GenericMasterDetail`; se consumen con páginas React custom.
- Aun así, el backend debe mantener un contrato uniforme:
  - `columns`: definición de columnas.
  - `rows`: filas con `type`, `label` y `values`.
  - Nada de lógica de negocio en el frontend (solo formateo y presentación).

- No hardcodear URLs completas en el frontend; siempre usar el `config/axios` del proyecto y variables de entorno para la base URL.

---

## 8. Qué rechazar o advertir

- **Error:** SQL crudo innecesario para agregaciones que se pueden expresar con Query Builder.
- **Error:** duplicar lógica financiera (signos, totales) en frontend.
- **Advertencia:** múltiples endpoints que resuelven el mismo reporte con contratos distintos; preferir centralizar la lógica en un solo controlador/servicio reutilizable.

---

