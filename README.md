# Cursor Skills – pybernate + ruibernate

Skills reutilizables para proyectos que usan el stack **pybernate** (backend) y **ruiBernate** (frontend). Pensadas para usarse con Cursor en `.cursor/skills/`.

## Estructura

```
.
├── architecture/          # Reglas de arquitectura y dominios
│   ├── business_domains/
│   └── project_architecture/
├── backend/               # Backend con pybernate
│   ├── controller_generation/
│   ├── endpoint_rules/
│   └── pybernate_rules/
├── frontend/              # Frontend con ruiBernate
│   ├── module_structure/
│   ├── page_generation/
│   └── ruibernate_rules/
└── integration/           # Contratos y sincronización frontend-backend
    ├── api_contracts/
    └── frontend_backend_sync/
```

Cada carpeta contiene un `SKILL.md` con el nombre, descripción y reglas de la skill.

## Uso en otro proyecto

### Opción 1: Clonar y copiar (recomendado)

1. Clona este repositorio en tu máquina (o descarga el ZIP).
2. En tu proyecto, crea la carpeta de skills si no existe:
   ```bash
   mkdir -p .cursor/skills
   ```
3. Copia la carpeta `architecture`, `backend`, `frontend` e `integration` dentro de `.cursor/skills/`:
   ```bash
   cp -r /ruta/al/clon/cursor-skills-pybernate/architecture .cursor/skills/
   cp -r /ruta/al/clon/cursor-skills-pybernate/backend .cursor/skills/
   cp -r /ruta/al/clon/cursor-skills-pybernate/frontend .cursor/skills/
   cp -r /ruta/al/clon/cursor-skills-pybernate/integration .cursor/skills/
   ```
   O en una sola línea (desde la raíz del clon):
   ```bash
   cp -r architecture backend frontend integration /ruta/a/tu/proyecto/.cursor/skills/
   ```

### Opción 2: Submódulo Git

Para mantener las skills actualizadas desde este repo:

```bash
cd /ruta/a/tu/proyecto
git submodule add https://github.com/jjhenao2025/cursor-skills-pybernate.git .cursor/skills-pybernate
```

Luego en Cursor puedes referenciar las skills desde `.cursor/skills-pybernate/` (depende de cómo Cursor resuelva rutas; si solo lee `.cursor/skills/`, copia o enlaza ahí el contenido del submódulo).

### Opción 3: Enlace simbólico

Si tienes el repo clonado en un lugar fijo:

```bash
cd /ruta/a/tu/proyecto
mkdir -p .cursor/skills
ln -s /ruta/al/clon/cursor-skills-pybernate/architecture .cursor/skills/architecture
ln -s /ruta/al/clon/cursor-skills-pybernate/backend .cursor/skills/backend
ln -s /ruta/al/clon/cursor-skills-pybernate/frontend .cursor/skills/frontend
ln -s /ruta/al/clon/cursor-skills-pybernate/integration .cursor/skills/integration
```

Así, al actualizar el clon, tu proyecto usa siempre la última versión de las skills.

## Requisitos

- Proyectos que usen **pybernate** (backend) y/o **ruiBernate** (frontend).
- Cursor con soporte para [Agent Skills](https://docs.cursor.com/context/agent-skills) en `.cursor/skills/`.

## Licencia

Ajusta la licencia según tu organización. Sin restricción por defecto para uso interno.
