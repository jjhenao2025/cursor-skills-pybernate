---
name: theme-parity-checklist
description: Verifica paridad visual y funcional entre tema claro y oscuro usando un checklist de componentes y estados de UI. Usar cuando se implementen o ajusten estilos globales, dashboards, tablas o formularios con soporte de temas.
---

# Theme Parity Checklist

## Objetivo

Evitar que una vista quede correcta en un tema y rota/inconsistente en el otro.

## Componentes a verificar

- Layout base (fondo, header, sidebar, content, footer)
- Cards y contenedores
- Tablas (header, filas, hover, bordes)
- Inputs/select/date pickers
- Botones y tags
- Alertas/estados (success, warning, error, info)

## Estados a verificar

- Default
- Hover
- Focus
- Disabled
- Loading
- Empty/Error

## Reglas de paridad

1. Mantener jerarquia visual equivalente en ambos temas.
2. Asegurar contraste legible de texto, bordes e iconos.
3. Evitar hardcodear colores sin version dark/light.
4. Reusar tokens/variables de tema siempre que sea posible.

## Flujo recomendado

1. Aplicar estilos en tema base.
2. Replicar/ajustar para el tema alterno.
3. Revisar checklist completo por pantalla.
4. Validar casos de tabla + filtros + alertas.

## Checklist rapido

- [ ] Todos los textos son legibles en ambos temas
- [ ] Bordes y separadores siguen siendo visibles
- [ ] Estados de color mantienen su significado
- [ ] Inputs y botones tienen focus/hover claros
- [ ] No hay bloques con fondo heredado incorrecto
