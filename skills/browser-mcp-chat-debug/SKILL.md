---
name: browser-mcp-chat-debug
description: Diagnosticar conexiones MCP de navegador entre Chat normal, agentes locales y Electron, especialmente Firetrace/HardFire, cuando faltan herramientas o el agente está desconectado. No aplica a navegación ordinaria que ya funciona.
---

# Depurar un MCP de navegador en el chat solicitado

Mantén el objetivo del usuario: una prueba en Codex, SDK o Work/local no sustituye una llamada real en Chat normal. Esta skill no concede permisos para escribir en otros chats, desplegar servicios, cambiar conexiones o publicar datos.

## Identificar antes de corregir

Registra host/modo del chat, proyecto/remoto, versión instalada y publicada, identidad de plugin/app, endpoint y agente. Dos complementos con igual nombre pueden ser un paquete localhost y un conector remoto distinto. Lee configuración y código relevantes sin imprimir secretos.

Separa las capas: paquete → MCP local → agente → relay → MCP remoto → catálogo del chat → navegador. Health o instalación sólo prueba su capa. Herramienta ausente indica descubrimiento/catálogo; `connected:false` con herramienta disponible indica transporte/agente.

## Diagnóstico dirigido

1. Descubre herramientas realmente disponibles; no inventes nombres ni IDs.
2. Comprueba `initialize` y `tools/list` del servidor correcto. Si el remoto anuncia herramientas que el chat no recibe, actualiza la conexión existente antes de cambiar código.
3. Si faltan herramientas en el servidor, corrige su catálogo/implementación. Si falta agente, verifica configuración y proceso antes de iniciarlo dentro del alcance autorizado.
4. Conserva el transporte elegido. No añadas túneles, servicios o rutas a otro navegador para esconder el fallo.
5. Usa herramientas soportadas para la cuenta/UI. Ante bloqueo por política, autenticación o superficie no soportada, explica la acción concreta pendiente; no eludas el control con APIs privadas.

## Particularidades Firetrace/HardFire

`browser_*` controla el navegador propio de Firetrace; `hardfire_*` controla Electron. Backend esperado: `hard-browser-electron-cdp`. IDs: HardFire `tab_id`, Firetrace `browser_id`; no intercambiarlos. El bridge puede iniciar Electron cerrado, pero no recibe órdenes si el agente remoto está apagado. Modo oculto conserva renderizado y requiere sesión de escritorio; no implica transporte sólo local.

Un catálogo puede estar congelado aunque el servidor esté actualizado. El procedimiento oficial de Refresh debe comprobarse en documentación vigente. Verificar nombres necesarios es más robusto que exigir un número histórico de herramientas.

## Verificación y recuperación

Desde el chat objetivo, ejecutar estado → listado → abrir una URL inocua → comprobar URL/título y captura. Requiere autorización del usuario para enviar un mensaje de prueba a otro chat. Registrar backend, resultado y límites; no afirmar que una demo interactiva funcionó sólo porque su página cargó.

Los resultados remotos pendientes se consultan por `command_id`; no reenviar clics o escritura inciertos. Reintentar una lectura puede ser apropiado; reiniciar un agente borra deduplicación en memoria. Un error de visualización de JPEG no prueba un fallo de la acción anterior.

## Conservar el conocimiento

Documenta causa demostrada, corrección, versión, prueba en host objetivo y pendientes. No publiques credenciales, HAR de sesión, perfiles, logs o conversaciones completos. Comprueba remoto y diferencias de versión antes de publicar documentos; no mezcles una petición de documentación con cambios de código no solicitados.

Para el historial concreto y las decisiones del caso 2026-10-02, lee [la memoria de reconstrucción](references/reconstruction.md).
