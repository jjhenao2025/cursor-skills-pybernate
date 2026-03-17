---
name: error_handling_and_api_responses
description: Convenciones de manejo de errores y formato de respuestas API para sincronizar backend pybernate con frontend ruiBernate. Usar al crear o modificar endpoints.
---

# Integración – manejo de errores y respuestas API

## Cuándo usar este skill

Usa este skill cuando:

- Diseñes o modifiques **endpoints** que consumirá el frontend ruiBernate.
- Definas cómo devolver **errores** y **mensajes** al usuario.
- Necesites consistencia entre distintos controladores.

Este skill complementa `endpoint_rules`, `pybernate_rules` y `ruibernate_rules`.

---

## 1. Estructura estándar de respuesta de éxito

Recomendación general:

- Para listados:
  - `data`: lista de registros.
  - Opcional: `pagination` o campos asociados (page, pageSize, total).

- Para operaciones sobre un solo recurso:
  - `data`: objeto de la entidad (o representación relevante).
  - Opcional: `message` con texto de confirmación amigable.

Mantener:

- Tipos de datos coherentes.
- Nombres de campos estables (no cambiarlos arbitrariamente).

---

## 2. Estructura estándar de errores

Formato recomendado:

- Código HTTP adecuado (400, 404, 409, 422, 500, etc.).
- Cuerpo JSON con:
  - `code`: identificador corto o tipo de error (ej. `validation_error`, `not_found`, `conflict`).
  - `message`: mensaje legible para el usuario o para mapearlo a uno.
  - `details`: información adicional opcional (contexto técnico, ids, etc.).
  - `fieldErrors`: lista/mapa de errores por campo cuando haya validaciones de formulario.

Objetivo:

- Permitir que el frontend:
  - Muestre mensajes de error claros.
  - Resalte campos específicos cuando la validación lo requiera.

---

## 3. Códigos HTTP recomendados

Guía rápida:

- `400 Bad Request`: request mal formado o con parámetros inválidos generales.
- `404 Not Found`: recurso no encontrado.
- `409 Conflict`: conflictos de negocio (ej. intentar conciliar algo ya conciliado).
- `422 Unprocessable Entity`: errores de validación de campos.
- `500 Internal Server Error`: errores no controlados.

Evitar:

- Devolver siempre `200` con un mensaje de error dentro; usar el código HTTP correcto.

---

## 4. Integración con frontend ruiBernate

El frontend puede:

- Mostrar:
  - `message` en notificaciones/toasts.
  - Errores de `fieldErrors` en campos de formularios o tablas/ediciones en línea.

Para facilitar esto:

- Mantener la estructura de errores consistente en todos los controladores.
- No mezclar formatos diferentes sin motivo.

---

## 5. Buenas prácticas

- Log interno:
  - Registrar detalles técnicos en logs del backend.
  - Mantener mensajes al frontend más amigables/seguros.

- No exponer:
  - Trazas completas de errores o detalles sensibles al cliente.

