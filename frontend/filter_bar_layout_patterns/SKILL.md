---
name: filter-bar-layout-patterns
description: Construye barras de filtros responsivas y alineadas para tablas y reportes, con reglas de espaciado, jerarquia visual y CTA principal. Usar cuando una pagina tenga RangePicker, Selects, Inputs y botones de consulta/export.
---

# Filter Bar Layout Patterns

## Objetivo

Mantener consistencia visual y usabilidad en barras de filtros complejas.

## Patron base recomendado

- Contenedor `flex` con:
  - `display: flex`
  - `flex-wrap: wrap`
  - `gap` uniforme
  - `align-items: flex-end`
- Cada control en un bloque con:
  - label arriba
  - control abajo
  - ancho minimo definido

## Jerarquia de acciones

- `Consultar` como CTA primario.
- Acciones secundarias (exportar/copiar/imprimir) separadas visualmente.
- Evitar mezclar CTA principal en medio de campos.

## Reglas de alineacion

1. Todos los bloques deben terminar en la misma linea base.
2. Si hay labels ocultos para alinear, usar una sola estrategia consistente.
3. En wrap, mantener orden logico: filtros principales -> filtros opcionales -> accion.

## Reglas de densidad

- Labels cortos y claros.
- Anchos minimos coherentes por tipo de control.
- Evitar tamaños de fuente/padding dispares.

## Checklist rapido

- [ ] Boton principal alineado con controles
- [ ] Mismo espaciado entre bloques
- [ ] Orden de filtros consistente
- [ ] Comportamiento correcto en pantallas angostas
- [ ] Acciones secundarias no compiten con CTA principal
