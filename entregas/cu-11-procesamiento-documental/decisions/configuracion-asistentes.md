---
description: Las instrucciones de los asistentes de IA van en AGENTS.md, los skills en .claude/skills/ y los subagentes en .claude/agents/, que leen tanto Copilot como Claude Code.
state: Aceptado
---

# ADR — Dónde vive la configuración de los asistentes de IA

## Decisión
- Instrucciones de proyecto: `AGENTS.md` en la raíz. `.github/copilot-instructions.md` solo remite a él.
- Skills: `.claude/skills/<nombre>/SKILL.md`, con el campo `name` igual al nombre de la carpeta.
- Subagentes: `.claude/agents/<nombre>.md`, en formato de Claude.
- Servidores MCP: `.vscode/mcp.json`.

## Motivación
El equipo usa GitHub Copilot en VS Code y Claude Code. VS Code lee skills de `.github/skills/`, `.claude/skills/` y `.agents/skills/`, y agentes de `.github/agents/` y `.claude/agents/`. Claude Code solo lee `.claude/skills/` y `.claude/agents/`. Guardarlos en `.claude/` hace que cada pieza se escriba una vez y la usen las dos herramientas.

## Alternativas consideradas
- `.github/skills/` y `.github/agents/`. Rechazada: Claude Code no los lee.
- `.agents/skills/`, la carpeta agnóstica que propone la guía del curso. Rechazada: Claude Code no la documenta como ubicación de skills.
- Duplicar cada pieza en las dos ubicaciones. Rechazada: las copias acaban siendo distintas.

## Referencias
- https://code.visualstudio.com/docs/copilot/customization/agent-skills
- https://code.visualstudio.com/docs/copilot/customization/custom-agents
- https://code.claude.com/docs/en/skills
- `docs/uso-asistentes-ia.pdf`, página 17
