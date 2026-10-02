# Proyectos implicados

| Proyecto | Responsabilidad | Estado documentado |
| --- | --- | --- |
| [HardFire](https://github.com/Shisetsu-Code/HardFire) | Navegador Electron, MCP local, pestañas, estructura DOM, HAR y WS | Instalación 1.5.1 comprobada desde Chat normal mediante Firetrace; código main 1.4.2 al publicar la guía |
| [Firetrace](https://github.com/Shisetsu-Code/firetrace) | Navegador propio y conexión MCP remota existente; adaptador a agente HardFire | Catálogo remoto 2.1.0 con 61 herramientas; actualización del catálogo permitió usar Electron |
| Local Browser Bridge | Proyecto independiente con MCP stdio/HTTP loopback y Playwright | Pruebas locales de 12 herramientas; no se verificó su conexión directa en Chat normal |

Los documentos específicos de cada proyecto enlazan a esta memoria común. El proyecto Local Browser Bridge no tenía remoto Git propio en el entorno inspeccionado; su ficha se conserva aquí y en su carpeta local, sin inventar un repositorio.

## Local Browser Bridge

Creado para disponer de un puente nuevo, independiente de los proyectos anteriores. Endpoint HTTP local `127.0.0.1:18791/mcp`, alternativa stdio, pestañas con `tabId`, referencias ligadas al snapshot y búfer de consola/HTTP/WS limitado a 500 eventos por pestaña. Los procesos stdio y el servidor HTTP tienen sesiones distintas.

Se verificó MCP y Chromium local, modo visible/headless, referencias obsoletas y aislamiento. El descubrimiento por el host de escritorio no probó llamadas desde Chat normal. El registro local en configuración general sólo afecta al destino que realmente ejecuta MCP local; no basta para afirmar acceso desde cualquier chat. El supervisor configurado debe comprobarse tras un reinicio real antes de prometer disponibilidad permanente.

## Fuentes y alcance

Las fuentes de esta síntesis son el código instalado, catálogos MCP, pruebas directas del servidor, resultados del chat objetivo y documentos de proyecto. Los nombres de chat se conservan como contexto de verificación; sus identificadores privados, mensajes completos y logs no se publican. La sesión trató estos tres proyectos; no se presume conocimiento de otros repositorios del usuario.
