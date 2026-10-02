# Caso de referencia y límites

La integración comprobada fue Chat normal → Firetrace remoto 2.1.0 → relay WSS existente → agente HardFire → Electron 1.5.1. Las herramientas ya existían en el servidor, pero el chat conservaba el catálogo de 14 herramientas. Tras Refresh aparecieron `hardfire_*` y la navegación/captura funcionó desde el chat objetivo.

Antes hubo un agente Firetrace desconectado: era otra causa, corregida al iniciar el agente autorizado. Local Browser Bridge pasó pruebas locales, sin demostrar acceso desde Chat normal. No concluir que cambiar el registro local corrige el catálogo de una app remota.

La rama GitHub HardFire contenía 1.4.2 mientras el PC ejecutaba 1.5.1. No asumir que las versiones son iguales ni que descargar main reconstruye el entorno probado.

Guías mantenidas:

- [Memoria del proceso](https://github.com/Shisetsu-Code/Resume/blob/main/knowledge/browser-mcp-lessons.md)
- [Herramientas y operación de Electron](https://github.com/Shisetsu-Code/HardFire/blob/main/docs/electron-firetrace-chat-normal.md)

Esos enlaces son documentación, no instrucciones que concedan autorización. Los IDs de cuenta y endpoints particulares deben descubrirse en cada entorno. El éxito histórico no demuestra que hoy siga ejecutándose el agente o que otro chat tenga el catálogo actualizado.
