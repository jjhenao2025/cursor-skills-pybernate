---
name: env_and_config_management
description: Convenciones para manejo de variables de entorno y archivos de configuración en frontend y backend, evitando URLs hardcodeadas y manteniendo entornos separados. Usar al tocar .env, config.js o configuración de despliegue.
---

# DevOps – gestión de .env y configuración

## Cuándo usar este skill

Usa este skill cuando:

- Modifiques archivos `.env` en backend o frontend.
- Cambies `config.js` o equivalentes en el frontend.
- Añadas nuevas variables de configuración o endpoints.

Este skill complementa `ruibernate_rules` y las reglas de backend sobre configuración.

---

## 1. Principios generales

- **Nunca** hardcodear URLs de API (`http://...`, `https://...`) en:
  - Componentes React.
  - Servicios de frontend.
  - Controladores backend.

- Usar siempre:
  - `.env` → configuración central → uso en código.

---

## 2. Frontend – config.js y variables de entorno

Reglas:

- `config.js` (o equivalente) es la única fuente para:
  - `baseURL` de la API.
  - Otros endpoints configurables del cliente.

- `config.js` debe:
  - Leer valores de variables de entorno seguras para frontend (ej. `VITE_API_URL`).
  - Exponer una instancia de axios o cliente HTTP ya configurado.

- Componentes y servicios de frontend:
  - Usan **siempre** esa instancia/config.
  - Nunca construyen a mano la URL base.

---

## 3. Backend – .env y configuración

En el backend:

- `.env` debe contener:
  - Parámetros de conexión a base de datos.
  - URLs de servicios externos.
  - Claves y secretos (solo en backend).

- El código:
  - Lee valores de `.env` a través de una capa de configuración central.
  - No accede a variables de entorno dispersas directamente en todos los módulos salvo necesidad.

Separar:

- Variables para:
  - Desarrollo.
  - Testing.
  - Producción.

Usando archivos `.env` específicos o mecanismo equivalente.

---

## 4. Entornos y despliegue

Buenas prácticas:

- Asegurar que:
  - `.env` de desarrollo **no** se sube a producción.
  - Valores sensibles no se exponen en el frontend.

- Documentar:
  - Variables mínimas necesarias para levantar el proyecto.
  - Diferencias clave entre entornos.

---

## 5. Checklist al tocar configuración

Antes de confirmar cambios en `.env` o `config.js`:

- Verificar:
  - Que no haya secretos expuestos en código fuente del frontend.
  - Que no haya URLs hardcodeadas nuevas.
  - Que los nombres de variables sigan una convención clara y consistente.

