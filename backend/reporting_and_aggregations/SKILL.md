---
name: reporting_and_aggregations
description: Guía para construir reportes y agregaciones usando el Query Builder de pybernate sin SQL crudo, devolviendo datos en un formato amigable para ruiBernate. Usar al crear endpoints de reporting o listados especiales.
---

# Backend – reportes y agregaciones con pybernate

## Cuándo usar este skill

Usa este skill cuando:

- Diseñes **endpoints de reportes** (totales, resúmenes, dashboards).
- Necesites **agregaciones** (SUM, COUNT, AVG, etc.) o agrupaciones complejas.
- Tengas la tentación de escribir SQL crudo para lograr un reporte.

Este skill se apoya en `pybernate_rules` y prohíbe SQL crudo salvo casos extremos.

---

## 1. Principios generales

- Usar el **Query Builder de pybernate** para:
  - Filtros dinámicos.
  - Joins entre entidades.
  - Agregaciones y group by.
- Devolver los resultados como:
  - **Lista de diccionarios** con nombres de columnas/alias claros.
  - Estructuras que ruiBernate pueda renderizar fácilmente en tablas o gráficos.

No:

- No convertir los resultados del Query Builder en objetos ORM manualmente.
- No mezclar SQL crudo innecesariamente.

---

## 2. Patrones de agregación

Para construir agregaciones típicas:

- Sumar montos por:
  - Período (día, mes, año).
  - Cliente/proveedor.
  - Cuenta.

- Contar registros por:
  - Estado.
  - Tipo de documento.

Regla:

- Usar alias descriptivos en las columnas agregadas (ej. `total_monto`, `cantidad_documentos`).
- Devolver siempre los campos de agrupación + las columnas agregadas.

---

## 3. Filtros dinámicos

Al diseñar endpoints de reporte:

- Recibir filtros desde el frontend (rango de fechas, estados, entidades relacionadas).
- Aplicarlos en el Query Builder de forma segura:
  - Sin concatenar strings de SQL.
  - Usando las APIs de filtro de pybernate.

Objetivo:

- Permitir flexibilidad en la UI sin comprometer la seguridad ni la mantenibilidad.

---

## 4. Formato de respuesta para frontend

Para integrarse bien con ruiBernate:

- Estructura recomendada:
  - `data`: lista de filas (dicts) para tabla o gráfico.
  - `summary` u otro campo opcional con totales globales si aplica.

- Mantener consistencia:
  - Tipos de datos coherentes (números, fechas, strings).
  - Nombres de campos que se correspondan con las columnas/series en frontend.

---

## 5. Evitar SQL crudo

Si parece que “sólo con SQL crudo se puede”:

- Revisar primero si el Query Builder puede expresar la consulta.
- En caso extremo de SQL crudo:
  - Documentar claramente el motivo.
  - Mantener la consulta en un lugar controlado y bien testeado.

Pero por defecto:

- Preferir siempre las capacidades de pybernate.

