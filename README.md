# Resume: conocimiento de los proyectos

Memoria técnica reproducible de los proyectos HardFire, Firetrace y Local Browser Bridge. Documenta decisiones, fallos, comprobaciones y límites; no contiene credenciales, registros privados ni perfiles de navegador.

## Navegación y MCP — 2 de octubre de 2026

- [Historia, arquitectura y aprendizajes](knowledge/browser-mcp-lessons.md)
- [Inventario y responsabilidad de cada proyecto](projects/README.md)
- [Skill reutilizable: depurar un MCP de navegador en Chat normal](skills/browser-mcp-chat-debug/SKILL.md)
- [Guía de uso de HardFire Electron](https://github.com/Shisetsu-Code/HardFire/blob/main/docs/electron-firetrace-chat-normal.md)

La integración comprobada utiliza HardFire local 1.5.1 y Firetrace MCP 2.1.0. La documentación de una versión instalada no significa que su código esté publicado en la rama principal de cada repositorio. Antes de reconstruir el entorno, verificar versiones y catálogo reales.

## Usar la skill

Copia `skills/browser-mcp-chat-debug` a `~/.codex/skills/browser-mcp-chat-debug`. Se puede invocar como `$browser-mcp-chat-debug`; también está diseñada para descubrimiento automático en solicitudes de diagnóstico de esta conexión. La skill no contiene acceso a cuentas ni autorización para modificar otros chats o servicios.

## Mantener esta memoria

Al cambiar una integración, registrar el problema observable, la causa demostrada, la corrección, dónde se verificó y lo que queda pendiente. Conservar los resultados fallidos relevantes sin presentarlos como limitaciones permanentes del producto. Actualizar los documentos del proyecto y la síntesis; no publicar tokens, HAR reales ni registros de conversaciones completos.
