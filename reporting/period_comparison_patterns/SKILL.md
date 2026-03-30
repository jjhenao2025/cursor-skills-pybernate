---
name: period-comparison-patterns
description: Construye reportes comparativos entre periodos (actual vs comparativo), calcula variacion absoluta y porcentual, y maneja casos borde como base cero. Usar cuando el usuario pida comparativos de metricas financieras, comerciales u operacionales por rango de fechas.
---

# Period Comparison Patterns

## Objetivo

Estandarizar comparaciones entre dos periodos para evitar errores de interpretacion, calculo y presentacion.

## Entradas minimas

- Periodo actual: `desde_actual`, `hasta_actual`
- Periodo comparativo: `desde_comp`, `hasta_comp`
- Filtros opcionales: empresa, unidad, centro de costo, segmento, capa, etc.

## Salida recomendada por fila

- `categoria` o `metrica`
- `monto_actual`
- `monto_comparativo`
- `variacion_abs` = `monto_actual - monto_comparativo`
- `variacion_pct` = `(variacion_abs / monto_comparativo) * 100` (si comparativo != 0)

Si el comparativo es 0:
- `variacion_pct = null`
- Mostrar `N/A` en UI (no inventar 0%).

## Regla de calculo

1. Validar que ambos rangos sean validos (`desde <= hasta`).
2. Calcular ambos periodos con el mismo set de filtros.
3. Normalizar tipos numericos antes de operar (float/decimal).
4. Calcular variaciones con formula unica del skill.
5. Devolver payload plano y estable para frontend.

## Semantica sugerida

- El signo de variacion **no implica** por si solo bueno/malo.
- Favorable/desfavorable depende del tipo de metrica; delegar esa decision a una capa de semantica.

## Checklist rapido

- [ ] Ambos periodos usan los mismos filtros
- [ ] Manejo de base 0 definido
- [ ] Variacion abs y pct calculadas una sola vez
- [ ] Payload estable y documentado
- [ ] Etiquetas de periodo legibles para UI/export
