---
name: conciliacion_flows
description: Skill específico para flujos de conciliación, describiendo la orquestación backend (pybernate) + frontend (ruiBernate/GenericMasterDetail). Usar al crear o modificar lógica o pantallas relacionadas con conciliaciones.
---

# Integración – flujos de conciliación

## Cuándo usar este skill

Usa este skill cuando:

- Trabajes sobre **controladores, servicios o pantallas** relacionados con conciliaciones.
- Diseñes o modifiques el flujo de:
  - Selección de movimientos.
  - Cálculo de diferencias.
  - Confirmación/anulación/reapertura de conciliaciones.

Este skill se apoya en:

- Reglas de `pybernate_rules` para backend.
- Reglas de `ruibernate_rules` y `genericmasterdetail_line_items` para frontend.

---

## 1. Visión general del flujo de conciliación

Aunque los detalles concretos dependen del negocio, típicamente:

- Hay un **maestro de conciliación** (cabecera):
  - Identificador de conciliación.
  - Rango de fechas o período.
  - Cuenta(s) o entidad a conciliar.
  - Estado (abierta, conciliada, parcialmente conciliada, anulada, etc.).

- Hay **líneas o movimientos**:
  - Movimientos de banco/libro.
  - Movimientos marcados como conciliados/no conciliados.
  - Posibles diferencias (montos, fechas, faltantes, sobrantes).

---

## 2. Backend – controlador y servicios de conciliación

Recomendaciones:

- Controlador específico de conciliación (o parte de un controlador de módulo) que:
  - Liste conciliaciones.
  - Permita crear/editar cabeceras de conciliación.
  - Exponga operaciones de negocio clave:
    - Cargar movimientos candidatos a conciliar.
    - Marcar/desmarcar movimientos conciliados.
    - Confirmar conciliación.
    - Anular/reabrir cuando aplique.

- Servicios de conciliación:
  - Encapsular lógica:
    - Selección de movimientos.
    - Cálculo de diferencias.
    - Regla de estados.

Evitar:

- Repetir cálculos complejos en cada endpoint; centralizarlos en servicios.

---

## 3. Frontend – uso de GenericMasterDetail

Para pantallas de conciliación:

- Usar `GenericMasterDetail` cuando:
  - Se maneje un maestro (conciliación) con múltiples movimientos/líneas.

- Backend debe devolver:
  - Datos del maestro (cabecera).
  - Colección de líneas/movimientos.
  - Totales/diferencias (ej. totales conciliados vs no conciliados).

- En la UI:
  - Mantener visible el estado general (totales, diferencias) mientras el usuario trabaja con líneas.
  - Ofrecer acciones claras:
    - Conciliar/Desconciliar movimiento.
    - Confirmar conciliación.
    - Ver detalle de diferencias.

---

## 4. Estados y reglas de negocio

Definir (y documentar en código/skill de dominio si aplica):

- Estados permitidos para conciliaciones.
- Transiciones válidas:
  - Ej. de abierta → conciliada; de conciliada → anulada (si se permite).

- Regla:
  - No permitir acciones inconsistentes con el estado (ej. no modificar líneas de una conciliación cerrada).
  - Backend debe ser la **fuente de verdad** para estas reglas; el frontend sólo las refleja.

---

## 5. Auditoría y trazabilidad

Ligado a `events_and_audit_trail`:

- Registrar:
  - Quién concili ó/desconcili ó movimientos.
  - Cambios de estado de conciliaciones.
  - Ajustes/diferencias relevantes.

- Usar eventos de pybernate:
  - Para recalcular totales al modificar líneas de conciliación.
  - Para dejar rastro en tablas de auditoría si el negocio lo requiere.

