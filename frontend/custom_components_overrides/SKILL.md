---
name: custom_components_overrides
description: Guía para decidir cuándo y cómo extender o envolver GenericMaster/GenericMasterDetail con componentes custom sin romper las reglas de ruibernate. Usar en pantallas con requisitos visuales o lógicos especiales.
---

# Frontend – componentes custom alrededor de GenericMaster

## Cuándo usar este skill

Usa este skill cuando:

- Una pantalla tenga requisitos visuales o de interacción que **no caben** cómodamente en un `GenericMaster` “puro”.
- Necesites combinar:
  - Tabla estándar (GenericMaster/GenericMasterDetail) +
  - Gráficos, dashboards, paneles laterales, formularios auxiliares, etc.
- Quieras validar si realmente necesitas un componente custom o puedes mantenerte en lo estándar.

---

## 1. Criterios para salir del flujo estándar

No crees componentes custom de entrada. Antes, comprueba:

- ¿La pantalla es básicamente un listado/CRUD con filtros y acciones estándar?
  - **Sí** → Usa sólo `GenericMaster` / `GenericMasterDetail`.
  - **No** → Sigue abajo.

Considera un componente custom cuando:

- Necesitas **visualizaciones ricas** (gráficos, KPIs, dashboard).
- La interacción combina varios subflujos complejos en una sola vista.
- Hay lógica de interfaz muy específica que no se puede expresar bien sólo con configuración.

---

## 2. Patrón recomendado: envolver, no reemplazar

Cuando necesites un componente custom:

- Mantén `GenericMaster` o `GenericMasterDetail` como **núcleo** de la parte tabular.
- Crea un componente que **lo envuelva**, por ejemplo:

- Layout típico:
  - Panel superior: KPIs, filtros avanzados, botones globales.
  - Columna principal: tabla basada en GenericMaster.
  - Columna lateral: detalles, vista previa, widgets.

Ventajas:

- Sigues usando la integración estándar con rutas y `endpoint`.
- No rompes el patrón ni las reglas de `ruibernate_rules`.

---

## 3. Rutas y componentMap

Incluso con componentes custom:

- La entrada en `/routes` debe seguir viniendo del **backend**.
- En el campo `component` se usará el nombre del componente custom.
- Ese componente debe estar registrado en el `componentMap` de `routeMapper.js` (o equivalente).
- El componente custom recibirá, como mínimo, `endpoint` y/o props necesarias para seguir delegando en GenericMaster cuando aplique.

Regla clave:

- **Nunca** hardcodear URLs de API en el componente custom.
- Siempre usar `config.js` y las props que trae la ruta.

---

## 4. Lógica de negocio: dónde debe estar

Aunque el componente custom pueda tener lógica de UI compleja:

- La **lógica de negocio** (validaciones fuertes, cálculos, reglas de estados) debe estar:
  - En el **backend** (controladores/servicios pybernate), o
  - En módulos de lógica compartida de frontend si de verdad es sólo de presentación.

Evita:

- Repetir en el frontend reglas críticas que ya existen en backend.
- Tomar decisiones de negocio sólo desde el cliente.

---

## 5. Ejemplos de buenos usos

Casos en los que un componente custom alrededor de GenericMaster tiene sentido:

- Dashboard de conciliación:
  - Arriba: totales conciliados/no conciliados, diferencias.
  - Centro: tabla de movimientos basada en GenericMasterDetail.
  - Lateral: detalle de la conciliación seleccionada.

- Panel de análisis de ingresos:
  - Filtros avanzados por múltiples dimensiones.
  - Gráficos de distribución y tendencia.
  - Tabla de soporte con GenericMaster como detalle.

