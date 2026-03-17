---
name: controller_generation
description: Una página = un controlador; lógica de datos reutilizable en servicios/comunes (no repetir por controlador); helpers en comunes. Usar al crear controladores o funciones de utilidad reutilizables.
---

# Backend – controladores y helpers en comunes

**Cuándo usar:** Al crear una nueva pantalla/entidad (controlador en backend), al generar controladores con las utilidades de `orm`, o cuando crees funciones de utilidad (fecha a string, normalizar RUT, etc.).

---

## 1. Una página → un controlador

- Por cada **página** o pantalla de la aplicación (ej. listado de productos, órdenes maestro-detalle) debe existir un **controlador** (o endpoints asociados) en el **backend** que atienda la lógica y los datos de esa pantalla.
- El controlador expone los endpoints CRUD de la entidad (GET/POST /api/entidad, GET/PUT/DELETE /api/entidad/:id) y/o lógica específica que la página necesite.
- No crear páginas en el frontend que llamen a APIs que no tengan un controlador definido en el backend.
- Al añadir una nueva pantalla: (1) crear o tener el controlador y los endpoints; (2) añadir la entrada en la respuesta del endpoint de rutas (`/routes`) con path, component y props.endpoint.

---

## 2. Reutilizar lógica: no repetir por controlador

- **Una página = un controlador** no implica duplicar lógica. Si varias páginas necesitan los mismos datos (ej. movimientos, productos, clientes), la **obtención y transformación de esos datos** debe vivir en una capa compartida: **servicio**, módulo de negocio o comunes.
- Cada **controlador** consume esa lógica compartida y se encarga de **cómo** se usan los datos en esa pantalla: qué filtros aplicar, qué formato devolver, qué agregaciones o permisos aplicar para ese endpoint.
- **Ejemplo:** Página A y página B necesitan "movimientos". Crear un servicio (o función en un módulo compartido) que obtenga los movimientos; el controlador de la página A lo llama y devuelve movimientos con un formato/filtros para A; el controlador de la página B lo llama y devuelve movimientos con formato/filtros para B. No implementar la obtención de movimientos dos veces.
- Resumen: **lógica de datos reutilizable en servicios/comunes; cada controlador orquesta y adapta la respuesta a lo que su página necesita.**

---

## 3. Helpers y utilidades – siempre en comunes

**Tipos de funciones que van en comunes:** conversión de fechas (date/datetime → `"YYYY-MM-DD"`), normalizar RUT, formateo de números o strings, validadores o sanitizadores pequeños reutilizables. Ejemplos de nombres: `_fecha_a_str`, `_normalize_rut`. Documentar con **docstring**.

**Protocolo obligatorio:**

1. **Revisar primero si ya existe en comunes** – Buscar en `comunes/`, `common/`, `utils/comunes.py` o equivalente. Si existe una función que haga lo mismo o muy similar, **usarla**; no crear otra.
2. **Si no existe, crearla en comunes** – Añadir la función en el lugar adecuado dentro de comunes. Documentar. Así se usa en todo el proyecto (controladores, servicios) sin duplicar código.
3. **No duplicar en cada controlador** – Donde haga falta, **importar desde comunes** y usar la función (ej. `from comunes.xxx import _fecha_a_str`).

**Flujo ejemplo:** Usuario pide "convertir fecha del ORM a string YYYY-MM-DD". Buscar en comunes si ya hay `fecha_a_str` o `_fecha_a_str`. Si no hay, crearla en comunes y usarla desde el controlador. Si ya hay, indicar que existe y cómo importarla.

---

## 4. Lo que NO debe crearse

- **No** crear `_query_builder_to_orm_objects` ni funciones equivalentes que conviertan resultados de `QueryBuilder.get()` (lista de diccionarios) en objetos ORM usando `set_dict`, `_exists` o instanciar el modelo y cargar el dict a mano. Usar QueryBuilder/ORM tal como los define pybernate.

---

## 5. Cómo generar un controlador (flujo manual con IPython)

En este proyecto, los **controladores de backend se generan manualmente** usando utilidades de `orm`. El agente **no debe ejecutar estos comandos**, sino **explicar al usuario** los pasos y pedirle que los ejecute en su terminal.

### Pasos para el usuario

1. **Abrir la terminal** en la carpeta del backend, por ejemplo:

   - `cd flujappi2-back-main` (o el directorio raíz del backend en tu entorno).

2. **Entrar a IPython**:

   - Ejecutar: `ipython`

3. **Importar las utilidades de generación**:

   - En la consola de IPython:
     - `from orm import gen_single_controller, reverse_engineer`

4. **Generar el controlador para una tabla/objeto concreto**:

   - Ejecutar:
     - `gen_single_controller("{nombre_tabla}")`
   - Sustituir `"{nombre_tabla}"` por el nombre real de la tabla/entidad para la que quieres el controlador.

5. **Revisar el archivo generado**:

   - Verificar:
     - Ubicación del archivo de controlador.
     - Nombres de endpoints generados.
     - Que respete las reglas de `pybernate_rules` y `endpoint_rules`.

### Instrucciones para el agente

- Cuando el usuario pida **crear un nuevo controlador**:
  - **No** intentes escribirlo “desde cero” ignorando el generador.
  - Indica explícitamente los pasos anteriores y solicita al usuario que:
    - Abra la terminal en el backend.
    - Entre a IPython.
    - Ejecute `from orm import gen_single_controller, reverse_engineer`.
    - Ejecute `gen_single_controller("{nombre_tabla}")` con el nombre correcto.
- Después de eso, puedes ayudar a:
  - Revisar el código generado.
  - Ajustar detalles según las reglas de arquitectura.

