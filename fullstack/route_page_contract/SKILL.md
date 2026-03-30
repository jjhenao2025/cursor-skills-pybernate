---
name: route-page-contract
description: Orquesta el contrato end-to-end entre backend y frontend al crear una pantalla nueva: endpoint, schema, servicio, pagina y registro de rutas/menu. Usar cuando el usuario pida crear una nueva vista o reporte consumido por API.
---

# Route Page Contract

## Objetivo

Evitar pantallas incompletas (por ejemplo: existe page pero no endpoint, o menu apunta a componente inexistente).

## Flujo estandar

1. **Backend**
   - Crear/ajustar controlador.
   - Definir endpoint con validaciones basicas.
   - Definir schema/DTO de respuesta estable.
2. **Frontend**
   - Crear servicio/hook para consumir endpoint.
   - Crear page/componente con estados de carga/error/vacio.
   - Conectar filtros de UI con query params.
3. **Ruteo y menu**
   - Registrar componente en mapper.
   - Agregar ruta/menu en origen de navegacion.
4. **Verificacion**
   - Validar respuesta real del endpoint.
   - Validar que la ruta abra correctamente.
   - Validar lints en archivos tocados.

## Contrato minimo recomendado

- Request con nombres consistentes (`snake_case` backend, mapping claro si frontend usa camelCase).
- Response con campos estables y predecibles.
- Errores con formato uniforme.

## Antipatrones

- Crear UI antes de confirmar payload.
- Duplicar transformaciones en varios componentes.
- Registrar ruta sin mapear el componente.

## Checklist rapido

- [ ] Endpoint y schema creados
- [ ] Servicio frontend implementado
- [ ] Page conectada a servicio
- [ ] Ruta y menu registrados
- [ ] Estados loading/error/empty visibles
- [ ] Lint sin errores
