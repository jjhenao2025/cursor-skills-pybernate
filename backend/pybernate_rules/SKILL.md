---
name: pybernate_rules
description: Reglas y convenciones para backend con pybernate (ORM, Query Builder, eventos, cache, Pydantic v2, relaciones, formularios, testing, migraciones). Incluye criterios de validación de código backend.
---

# Backend – reglas pybernate

**Cuándo usar:** Al crear o modificar modelos, controladores, servicios, consultas, validación, tests o esquema de base de datos en un proyecto pybernate.

---

## 1. Modelos ORM

- Los modelos de entidades (tablas) deben ser **los generados por pybernate** desde el esquema. No definir a mano clases ORM o Pydantic para entidades que ya tienen modelo generado.
- Si hace falta un DTO o schema solo para la API (request/response), puede ser un **Pydantic aparte** que use o transforme el modelo generado, sin reemplazar el modelo ORM.
- Buscar en el proyecto el módulo o paquete donde pybernate expone los modelos generados e **importar desde ahí**; no redefinir la entidad en otro archivo.

---

## 2. Query Builder

- Usar el **Query Builder de pybernate** para consultas que no se resuelvan con los métodos estándar del ORM (filtros complejos, joins, etc.).
- Los resultados de `QueryBuilder.get()` son **listas de diccionarios** (o similar). Usarlos así en la capa de negocio o en la API. **No** crear funciones que conviertan esos resultados en instancias ORM mediante `set_dict` ni `_exists`.
- No usar SQL crudo (`SELECT *`, etc.) salvo necesidad extrema y justificada.
- Si el usuario pide "convertir resultados del query a objetos ORM", rechazar y explicar que se trabaja con los resultados del Query Builder tal como vienen o se usan métodos ORM que ya devuelvan objetos.

---

## 3. Eventos y cache

- pybernate ofrece **eventos** (p. ej. after save, before delete). Al implementar creación, actualización o eliminación de entidades, usar esos puntos de extensión en lugar de duplicar lógica fuera del flujo oficial.
- Para consultas frecuentes o datos que cambien poco, considerar el **cache** según la documentación de pybernate; no deshabilitar o ignorar el cache sin motivo.
- Si piden "evitar el cache" o "saltarse eventos", preguntar el motivo y sugerir la forma correcta (configuración, parámetros) en lugar de parches en el código.

---

## 4. Relaciones entre entidades

- Las relaciones (FK, one-to-many, etc.) se definen y usan con el mecanismo que ofrece pybernate (atributos de relación, métodos de carga). No reimplementar "a mano" joins o cargas de relaciones que el ORM ya soporta.
- En respuestas API para maestro-detalle (GenericMasterDetail), incluir los datos relacionados en el formato que el frontend espera (anidado o referencias), usando la serialización del ORM o los schemas Pydantic, sin duplicar lógica.

---

## 5. Formularios generados

- pybernate puede **generar formularios web** a partir de los modelos. Antes de proponer formularios hechos a mano para entidades estándar, comprobar si la generación automática cubre el caso.
- Si hace falta personalización, indicar cómo **extender o configurar** la generación de formularios según la documentación de pybernate, no reemplazarla por completo.

---

## 6. Pydantic v2

- Usar **Pydantic v2**: `model_config = ConfigDict(...)` en lugar de `class Config` con `orm_mode`.
- Validación custom: `@field_validator` / `@model_validator`.
- Serialización hacia API: `model_dump()`, `model_validate()`, etc.; no `dict()` antiguo ni serialización manual innecesaria.
- Si piden "convertir modelo a dict" o "validar request", proponer `model_dump`, `model_validate`, etc.

---

## 7. Testing

- Usar **base de datos de prueba** (SQLite en memoria, schema de test) o mocks según la documentación del proyecto con pybernate.
- **No** ejecutar tests contra la base de producción. Aislar datos de test (transacciones que se revierten, fixtures).
- Para controladores que usan ORM, tests que validen comportamiento esperado (respuesta HTTP, formato JSON) con el stack real o mock del ORM/servicio según convenga.

---

## 8. Esquema y migraciones

- Si pybernate o el proyecto tienen **flujo de migraciones** (scripts, CLI), usarlo en lugar de ejecutar DDL a mano en producción.
- **No** proponer `DROP TABLE` ni cambios destructivos sin confirmación. En desarrollo, documentar cómo recrear o actualizar el esquema de forma segura.
- Si no hay herramienta de migraciones, definir pasos claros (scripts SQL o instrucciones) y ejecutarlos en el orden correcto (dependencias entre tablas).

---

## 9. Validación de código backend (qué rechazar o advertir)

- **Error:** SQL crudo (`SELECT *` o SQL literal). Usar pybernate ORM o Query Builder.
- **Error:** Uso de SQLAlchemy u otro ORM. Solo pybernate está permitido.
- **Error:** Cualquier función que convierta resultados de `QueryBuilder.get()` en instancias ORM mediante `set_dict`, `_exists` o patrón similar (p. ej. `_query_builder_to_orm_objects`). Usar los resultados del Query Builder tal como vienen o los métodos ORM que devuelvan objetos.
- **Advertencia:** Uso de `class Config` con `orm_mode` en modelos Pydantic. Preferir Pydantic v2 con `model_config = ConfigDict(...)`.
