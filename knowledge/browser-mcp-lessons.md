# Lo que aprendimos al conectar MCP, Chat normal y Electron

## Objetivo y resultado

El objetivo era controlar un navegador del PC desde **Chat normal**, incluyendo navegación, pestañas, inspección de tráfico y HAR, y utilizar el navegador personalizado Electron de HardFire. No se debía sustituir el objetivo por una prueba en Codex o Work con ejecución local.

Se comprobó una llamada real desde el chat «Prueba de juego»: el conector Firetrace expuso `hardfire_*`, inició Electron visible, enumeró sus pestañas, abrió la página oficial de Sweet Bonanza, devolvió su título y produjo una imagen JPEG. El backend fue `hard-browser-electron-cdp`. No se hicieron apuestas, compras ni tiradas; abrir la página no demuestra haber cargado o ejecutado la demo interactiva.

La integración final utiliza un navegador local y un transporte remoto existente: Chat normal → Firetrace MCP autenticado → relay WSS existente → agente local HardFire → bridge MCP → Electron/CDP. Las órdenes, resultados y capturas pasan por Cloudflare. No se añadió un túnel nuevo. El MCP puramente localhost funciona en clientes que ejecutan procesos locales; esa comprobación no prueba su disponibilidad en Chat normal.

## Evolución y hechos demostrados

1. Firetrace ya podía recibir llamadas desde Chat normal, pero en un momento su estado indicó `connected:false`. Las herramientas existían; faltaba la conexión del agente. El error observado pedía iniciar el worker en el PC.
2. Se exploraron ampliaciones de HardFire y se construyó Local Browser Bridge como proyecto independiente. Este último superó pruebas locales de MCP y Chromium, pero no estableció por sí mismo una conexión en Chat normal.
3. Se recuperó el agente Firetrace usando el relay existente, con autorización del usuario. Chat normal ejecutó `browser_status`, abrió `example.com` y confirmó la URL. Era el navegador independiente de Firetrace, no Electron.
4. El servidor Firetrace existente ya incluía un adaptador para HardFire. Se inició el agente HardFire y se comprobó Electron localmente; su backend y título confirmaron navegación real.
5. Una prueba autenticada directa del servidor verificó 61 herramientas, dos pestañas, referencias, escritura, teclado, espera de condición y JPEG. Aun así, Chat normal sólo veía el catálogo anterior de 14 herramientas.
6. Se identificó la conexión remota correcta en la configuración. Coexistían dos complementos llamados Firetrace: uno asociado a la app remota y otro paquete con MCP localhost. La coincidencia de nombre no implicaba la misma conexión.
7. El control de Firefox fue bloqueado por la herramienta de automatización porque no admitía sus políticas de URL; el navegador integrado no tenía sesión y el intento de inicio de sesión falló. No se trató ese bloqueo como un fallo del MCP ni se eludió mediante APIs privadas.
8. Después de que el usuario actualizara la conexión, Chat normal sí descubrió `hardfire_*` y completó la prueba Electron. La causa demostrada del catálogo incompleto en esa etapa fue la conexión sin actualizar, no una ausencia de herramientas en el servidor.

## Separar las capas evita arreglar lo equivocado

| Capa | Evidencia útil | Lo que no demuestra |
| --- | --- | --- |
| Paquete/plugin | Identidad, versión, manifiesto y archivos | Que el chat recibió herramientas |
| MCP local | Health y `initialize`/`tools/list`/`tools/call` | Que Chat normal accede a localhost |
| Agente local | Proceso correcto, conexión y heartbeat | Que el catálogo remoto está actualizado |
| Relay | Estado separado por agente, resultado de una orden | Qué herramientas conserva el chat |
| MCP remoto | Catálogo autenticado y llamada de prueba | Que la sesión de Chat normal lo importó |
| Chat normal | Llamada real y resultado del backend esperado | Que otro chat o host tenga el mismo acceso |
| Navegador | URL, título, captura y pestaña correctos | Que una operación interactiva posterior haya ocurrido |

`connected:false` y «herramienta inexistente» son fallos distintos. Tampoco debe confundirse un error al mostrar una imagen con un fallo de navegación. En la prueba final, una captura produjo `UnknownVizError` y la siguiente lectura obtuvo un JPEG correctamente.

## Identidades y versiones

Mantener separados repositorio, instalación, plugin, app remota, release, endpoint, agente y chat. No inventar un app ID ni actualizar un paquete distinto porque tenga el mismo nombre. Verificar el remoto Git antes de publicar.

La instalación HardFire comprobada era 1.5.1; cuando se publicó su guía, la rama principal de GitHub contenía 1.4.2. Se publicaron sólo documentos y se hizo explícita la diferencia. Una instalación local puede contener trabajo no publicado; nunca recomendar simplemente `git pull` como garantía de recuperar esa versión.

El número de herramientas sirve como evidencia de esa versión, no como requisito eterno. Para diagnosticar, importa más comprobar los nombres y esquemas de las herramientas necesarias.

## Diseño del bridge y el agente

- El bridge stdio permite descubrimiento aunque Electron esté cerrado y puede arrancar el navegador cuando llega una llamada. Un agente remoto apagado no recibe comandos: el arranque automático del navegador no equivale al del agente.
- Reutilizar la instancia de Electron conserva pestañas. Separar modo visible/oculto del ciclo de vida evita perder la sesión sólo por cambiar visibilidad.
- La exclusión de instancia del agente HardFire usa un socket local exclusivo. Un archivo PID ayuda al diagnóstico, pero no es suficiente para excluir instancias ni para decidir qué proceso detener.
- Heartbeats, reconexión y deduplicación reducen fallos de transporte. La deduplicación en memoria no sobrevive a reinicios; una acción sin resultado puede haber ocurrido.
- Ante un resultado pendiente, consultar su `command_id` en lugar de reenviar una acción. Verificar el estado antes de repetir clics o escritura.

## Pestañas, snapshots y tráfico

HardFire usa `tab_id`; Firetrace usa `browser_id`; Local Browser Bridge usa `tabId`. No son intercambiables. Obtener IDs de listados reales y enviarlos explícitamente evita operar sobre otra pestaña. Las referencias DOM se invalidan con navegación o eliminación de elementos.

`hardfire_snapshot` devuelve estructura y referencias, no píxeles. `hardfire_screenshot` devuelve JPEG (`image/jpeg`), no PNG. El interior de un canvas puede exigir interpretación visual. La extracción estructurada tiene límites y no garantiza inspeccionar todos los frames.

`hardfire_network_events` consulta la última captura; no inicia grabación continua. `hardfire_trigger_and_capture` combina un clic y captura filtrada. `hardfire_record_start`/`hardfire_record_save` graban y guardan HAR en Descargas/HardFire-HARs, sin recargar al iniciar. Los frames WS se guardan en extensiones HAR como `_webSocketFrames`; no todos los lectores HAR las muestran. Empezar tarde no recupera tráfico anterior. Los archivos pueden contener datos de sesión y deben revisarse antes de compartirse.

## Rendimiento

La demora entre acciones puede venir de decisiones del modelo, viajes de red, cola del agente, pausas explícitas o carga de la página. Headless evita molestias, pero no elimina esas causas. Para pruebas autorizadas, agrupar pasos conocidos con `hardfire_sequence`, usar esperas de condición y reducir lecturas redundantes. Las secuencias son ordenadas, con máximo 50 pasos; no son paralelas ni tienen rollback. No se verificó una mejora cuantitativa de latencia ni se automatizaron compras en juegos.

## Errores de proceso que no conviene repetir

- Declarar «funciona» tras health, instalación o SDK local, cuando el usuario pide Chat normal.
- Cambiar varios proyectos o transportes antes de identificar la capa fallida.
- Pedir reseleccionar el plugin repetidamente sin revisar el catálogo publicado y el recibido.
- Presentar Work/local como solución a una solicitud explícita de Chat normal.
- Crear túneles o servicios externos sin respetar el alcance autorizado.
- Reintentar acciones inciertas como si el timeout probara que no ocurrieron.
- Publicar secretos, registros completos, capturas privadas o HAR para conservar conocimiento.

## Procedimiento para reconstruir

Inventariar versiones e identidades; probar MCP local y agente por separado; comprobar el catálogo remoto autenticado; actualizar la conexión correcta; ejecutar desde el chat objetivo estado → listado → navegación inocua → título/captura; guardar evidencia sin secretos. Si falta autorización, autenticación o una superficie de UI soportada, informar el bloqueo concreto. Concluir únicamente con el alcance efectivamente comprobado.

Pendientes: sincronizar el código local 1.5.1 con GitHub mediante un trabajo separado; verificar persistencia del agente tras reiniciar Windows; medir latencia; probar HAR HTTP/WS desde Chat normal con una página de prueba controlada. No se dan por resueltos por haber documentado el proceso.
