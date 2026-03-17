---
name: service_layer_and_reuse
description: Patrones para extraer lógica de negocio reutilizable a servicios comunes en proyectos pybernate, permitiendo que un controlador pueda servir varias páginas relacionadas sin duplicar lógica.
---

# Backend – servicios y reutilización de lógica

## Cuándo usar este skill

Usa este skill cuando:

- Estés creando o modificando **controladores** para un módulo funcional (ej. Ingresos, Egresos, Conciliación).
- Veas que **varias páginas** relacionadas (ej. Facturas de Venta, Otros Ingresos, Anticipos de Clientes) comparten lógica de datos o reglas.
- Detectes código duplicado entre varios controladores/endpoints.

Este skill complementa `controller_generation`, `endpoint_rules` y `pybernate_rules`.

---

## 1. Relación módulos ↔ controladores ↔ páginas

Regla práctica para este proyecto:

- Un **módulo funcional** (ej. Ingresos) puede tener **un solo controlador** que sirva a varias páginas relacionadas:
  - Ejemplo: controlador de Ingresos que atiende:
    - Facturas de Venta
    - Otros Ingresos
    - Anticipos de Clientes

- Lo importante es:
  - Que exista un **controlador claro por módulo o grupo lógico**.
  - Que **cada página** tenga endpoints bien definidos dentro de ese controlador (rutas claras, responsabilidades claras).

Conclusión:

- “Una página = un controlador” se interpreta aquí como:
  - **Cada página tiene endpoints y lógica bien definidos en backend**, pero
  - Es totalmente válido que **un mismo controlador agrupe varias páginas del mismo módulo** siempre que la lógica compartida se gestione bien.

---

## 2. Qué va en servicios y qué en controladores

- **Servicios / capa de negocio compartida**:
  - Obtención de datos reutilizable (consultas comunes con pybernate/Query Builder).
  - Reglas de negocio que se aplican en varios endpoints/páginas del mismo módulo.
  - Cálculos complejos o transformaciones que se usan en más de un lugar.

- **Controladores**:
  - Orquestan llamadas a servicios.
  - Aplican filtros y formato específico para cada endpoint/página.
  - Manejan autenticación/autorización y estructura de la respuesta HTTP.

Regla:

- Si la misma lógica aparece en más de un endpoint/página → **extraer a servicio**.

---

## 3. Estructura sugerida de servicios

Sugerencias:

- Crear un módulo por dominio/módulo funcional:
  - `services/ingresos_service.py`
  - `services/conciliacion_service.py`
  - etc.

- Dentro del servicio:
  - Funciones claras y pequeñas, orientadas a casos de uso:
    - `obtener_facturas_venta_filtradas(...)`
    - `obtener_otros_ingresos(...)`
    - `calcular_totales_conciliacion(...)`

- Mantener la firma de las funciones estable para que controladores las usen sin romperse.

---

## 4. Ejemplo conceptual

En lugar de:

- Tres controladores distintos con lógica repetida para:
  - Facturas de Venta
  - Otros Ingresos
  - Anticipos de Clientes

Preferir:

- Un **controlador de Ingresos** con endpoints separados para cada tipo de página.
- Un **servicio de ingresos** con funciones reutilizables para:
  - Consultar movimientos.
  - Calcular saldos.
  - Aplicar validaciones comunes.

Así:

- Se evita duplicar lógica.
- Se mantiene una estructura clara por módulo funcional.

---

## 5. Helpers y utilidades generales

Para funciones ultra genéricas (fechas, formateo, normalización):

- Usar siempre los módulos de **comunes** / helpers existentes.
- Antes de crear algo nuevo:
  - Buscar en comunes si ya existe una función equivalente.

Esto mantiene:

- Servicios limpios (reglas de negocio).
- Comunes para utilidades genéricas.

