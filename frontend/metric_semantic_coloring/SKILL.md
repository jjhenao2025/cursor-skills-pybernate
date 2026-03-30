---
name: metric-semantic-coloring
description: Define colores de metricas por semantica de negocio (favorable/desfavorable) en lugar de solo signo numerico. Usar cuando se muestren variaciones, KPIs, tendencias o alertas en dashboards y tablas.
---

# Metric Semantic Coloring

## Objetivo

Aplicar colores correctos segun el significado de la metrica, evitando errores como "variacion negativa en costos pintada en rojo cuando es favorable".

## Regla base

No colorear solo por `+/-`.  
Colorear por: `tipo_metrica + direccion_cambio`.

## Mapeo recomendado

- **Ingresos / ventas / margen / utilidad**
  - Sube => verde
  - Baja => rojo
- **Costos / gastos / tiempos de espera / tasa de error**
  - Sube => rojo
  - Baja => verde
- **Metricas neutras**
  - Sin cambio => gris
  - Si no hay base => gris + `N/A`

## Implementacion sugerida

1. Clasificar metrica (ingreso, costo, calidad, riesgo, neutra).
2. Calcular direccion de cambio (`actual > comparativo`, etc.).
3. Resolver color con una funcion central (`getSemanticColor`).
4. Reusar la misma funcion en tags, badges, iconos y texto.

## Buenas practicas

- Mantener una sola fuente de verdad para colores.
- Evitar hardcodear colores por componente.
- Usar tokens del tema cuando sea posible.
- Acompanhar color con texto/icono para accesibilidad.

## Checklist rapido

- [ ] Clasificacion de metrica explicita
- [ ] Color consistente en toda la vista
- [ ] Casos `0`, `null`, `N/A` definidos
- [ ] Compatible con modo claro/oscuro
