---
description: Ruff es el único linter y formateador del código Python.
state: Aceptado
---

# ADR — Ruff como linter y formateador

## Decisión
Todo el código Python se revisa con `ruff check .` y se formatea con `ruff format .`. Su configuración va en `pyproject.toml`.

## Motivación
SDG lo indicó. Una sola herramienta, muy rápida, hace de linter y de formateador, así que el código de los tres sigue el mismo estilo y los asistentes de IA pueden comprobarlo con un comando antes de terminar.

## Alternativas consideradas
- Black más flake8 e isort. Rechazada: tres herramientas y tres configuraciones para lo que Ruff hace solo.

## Referencias
- https://docs.astral.sh/ruff/
- Reglas disponibles: https://docs.astral.sh/ruff/rules/
- Compatibilidad del formateador con Black: https://docs.astral.sh/ruff/formatter/#black-compatibility

Configuración actual, en `pyproject.toml`. La versión está fijada porque otra versión de Ruff puede formatear distinto y dar en rojo `ruff format --check`.

```toml
[project.optional-dependencies]
dev = ["ruff==0.17.1", "pytest>=8"]

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "UP", "SIM"]
```

Comprobación antes de terminar cualquier tarea (ver `AGENTS.md`):

```
ruff check .
ruff format --check .
```
