---
name: events_and_audit_trail
description: Guía para usar eventos de pybernate para auditoría y lógica secundaria sin duplicar código en controladores. Usar al necesitar lógica before/after sobre operaciones de entidades.
---

# Backend – eventos y trazabilidad (audit trail)

## Cuándo usar este skill

Usa este skill cuando:

- Necesites ejecutar lógica **antes o después** de crear/actualizar/eliminar entidades.
- Quieras agregar **auditoría** (quién hizo qué, cuándo, con qué cambios).
- Tengas reglas de negocio que se disparan con cambios en datos (recalcular saldos, estados globales, etc.).

Este skill se apoya en `pybernate_rules` (eventos y cache).

---

## 1. Rol de los eventos en pybernate

Eventos típicos:

- before insert / after insert
- before update / after update
- before delete / after delete

Uso recomendado:

- Colocar en eventos:
  - Lógica de auditoría.
  - Reglas de consistencia que dependen de cambios en datos.
  - Actualizaciones de agregados/resúmenes.

- Dejar en controladores:
  - Orquestación de requests/responses HTTP.
  - Validaciones ligadas al contexto de la petición (permisos, filtros).

---

## 2. Auditoría básica

Para implementar audit trail:

- Registrar:
  - Entidad afectada y su identificador.
  - Operación (crear, actualizar, eliminar).
  - Usuario responsable (si está disponible en el contexto).
  - Fecha/hora.
  - Cambios relevantes (antes/después cuando aplique).

- Guardar la auditoría en:
  - Tablas de auditoría dedicadas, o
  - Estructuras de log definidas por el proyecto.

---

## 3. Lógica secundaria y consistencia

Ejemplos de buena lógica para eventos:

- Después de crear/actualizar una línea:
  - Recalcular totales del maestro.
  - Actualizar estados globales.

- Después de conciliaciones:
  - Actualizar flags de “conciliado/no conciliado” en movimientos.
  - Registrar diferencias.

Ventaja:

- Se evita duplicar esta lógica en todos los controladores/endpoints que toquen la entidad.

---

## 4. Interacción con cache

Al usar eventos:

- Considerar que pybernate puede tener cache:
  - Si los datos afectados están cacheados, asegurarse de que:
    - El evento invalida/actualiza el cache según las reglas de pybernate.

- No deshabilitar el cache a la ligera:
  - Si hay problemas de consistencia, revisar primero la configuración de eventos y cache.

---

## 5. Buenas prácticas

- Mantener la lógica de eventos:
  - **Pequeña y bien enfocada** por tipo de evento.
  - Con manejo de errores claro para no dejar el sistema en estados incoherentes.

- Documentar:
  - Qué eventos existen.
  - Qué hacen.
  - Cómo interactúan con controladores y servicios.

