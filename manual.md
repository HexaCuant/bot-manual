# Bot3: un bot IRC

## Uso

Desde el directorio en el que se encuentra el bot:

`./bot3.py <archivo configuración>`

El archivo de configuración es opcional. Si no se especifica pregunta los datos necesarios y crea un archivo de configuración con los datos que se hayan especificado.

## Configuración

El archivo de configuración es un json con los siguientes datos

- server: el servidor de IRC
- port: el puerto de IRC
- nickname: el nick del bot
- password: la contraseña del bot si está registrado
- channels: lista de canales a los que tiene que ingresar el bot
- DJ: el DJ que emite en el momento que entre el bot a su canal
- radio: la URL de la radio del canal
- saluda: lista de canales donde se desea que el bot salude a los usuarios que entran
	- Nota: por compatibilidad `saluda` puede ser también la cadena "si"/"no" o un booleano, en cuyo caso `si` activará el saludo en todos los canales del bot.
- saludo_cooldown_seconds: segundos de espera antes de volver a saludar a un mismo nick en un canal/PM; por defecto: 20 (20 segundos). Evita bucles de saludo entre bots.
 - google_model / ai_model: modelo de IA a usar (ej. `"ollama:ministral-3:8b-cloud"` para Ollama o `"models/gemini-2.5-flash"` para Google). El bot acepta ambas claves; `ai_model` tiene prioridad.
 - ai_channels: lista de canales donde la IA está habilitada (ej. `["#canal1", "#canal2"]`).
 - ai_enabled: (obsoleto, usar ai_channels) habilita o deshabilita las respuestas IA por defecto (booleano).
 - system_prompt_lines: lista de líneas que componen el prompt de sistema de la IA. Admite el marcador `{nickname}` que se sustituye por el nick del bot. Si no se define se usa un prompt por defecto.
 - history_persist: guarda la historia a disco entre reinicios; por defecto: true.
 - history_dir: carpeta donde se guardan las historias; por defecto: `histories`.
 - history_max_messages: número máximo de mensajes a guardar en el historial por canal/PM; por defecto: 30.
 - history_whitelist: lista de claves permitidas para persistir histórico (ej. `[#canal, "PM:nick", "all"]`). Si está vacía no se persiste nada.
  
El archivo de configuración puede tener cualquier nombre pero se recomienda que sea el nick del bot con extensión `.json`.

## DJ autorizados

El bot mantiene una lista de DJs autorizados a ejecutar los comandos propios para controlar una emisión de radio

## Archivo `log`

El archivo `bot3.log` contiene los mensajes enviados por el bot y los mensajes recibidos. *TODO: hacer que se guarde en un archivo diferente para cada bot*

## Saludos

Si se ha especificado que el bot salude a los nuevos usuarios el bot espera unos segundos antes de enviar el mensaje de saludo y sigue esperando a que halla alguna actividad en el canal. Si el canal no tiene actividad el bot saludará tras el primer ping del servidor.

Para no saturar el canal se recomienda desactivar el saludo en el archivo de configuración en canales con mucha actividad.

## Recepción de mensajes

Cada vez que se recibe un mensaje el bot trata de discernir los diferentes elementos del mensaje:

- Nick
- Usuario
- host
- comando
- canal
- texto del mensaje

para poder actuar en consecuencia

## Ingreso en el canal

El bot conecta con el servidor y espera a recibir los mensajes de bienvenida del servidor. Una vez recibidos se unirá al canal especificado en su configuración

## Comandos disponibles

## Comandos de Inteligencia Artificial

El bot incorpora funcionalidades de IA generativa (Google Gemini y Ollama) para responder a los usuarios.

### !ia on / !ia off

Activa o desactiva las respuestas automáticas de la IA en el canal actual.
- `!ia on`: Activa la IA para el canal donde se ejecuta el comando (o PM).
- `!ia off`: Desactiva la IA para el canal donde se ejecuta el comando (o PM).

### Respuestas de IA mediante mención

Cuando un mensaje en un canal (donde la IA está habilitada) contiene el nick del bot, este responderá usando inteligencia artificial generativa. El bot mantiene un historial de conversaciones para proporcionar contexto y respuestas coherentes.

Ejemplos (asumiendo que el bot se llama "Robot"):
- **Robot, ¿qué opinas sobre la música electrónica?**
- **¿Puedes explicarme qué es un refrán, Robot?**
- **Robot: ¿cuál es tu canción favorita?**

Las respuestas pueden ser largas y se dividirán automáticamente en múltiples mensajes si es necesario.

## Comandos de Historial

El bot mantiene un historial de conversaciones para dar contexto a la IA.

### !gethistory [maxLines] <canal|PM:nick|all>

Muestra el historial almacenado.
- `maxLines`: (Opcional) Número de líneas a mostrar (por defecto 20).
- `canal`: Nombre del canal (ej. `#general`).
- `PM:nick`: Historial privado con un usuario.
- `all`: Todo el historial disponible.

### !showhistory [canal|all]

Muestra los historiales almacenados con formato detallado, incluyendo el rol (Bot o nick del usuario) para cada mensaje:
- Sin argumentos en un canal: muestra el historial de ese canal
- `!showhistory #canal`: muestra el historial del canal especificado
- `!showhistory all`: muestra una lista de todos los canales con historial guardado
- `!showhistory PM:nick`: muestra el historial de mensajes privados con ese usuario

A diferencia de `!gethistory`, este comando formatea los mensajes mostrando claramente quién envió cada uno.

### !clearhistory [canal|all]

Limpia el historial de conversaciones almacenado:
- Sin argumentos en un canal: limpia el historial de ese canal
- `!clearhistory #canal`: limpia el historial del canal especificado
- `!clearhistory all`: limpia todo el historial almacenado
- `!clearhistory PM:nick`: limpia el historial de mensajes privados con ese usuario

Este comando es útil para resetear el contexto de la IA o para borrar información sensible del historial.

### !whitelist add|remove|show|save <#canal|PM:nick|all>

Gestiona la lista blanca (whitelist) de canales y usuarios cuyos históricos se guardan:
- `!whitelist show`: muestra la whitelist actual
- `!whitelist add #canal`: añade un canal a la whitelist
- `!whitelist add PM:nick`: añade mensajes privados de un usuario
- `!whitelist add all`: añade todos los canales
- `!whitelist remove #canal`: elimina un canal de la whitelist
- `!whitelist save`: guarda la whitelist en el archivo de configuración

Solo se guardan históricos de canales/usuarios en la whitelist cuando está activada esta funcionalidad.

### `!history config` — Ajuste de la configuración de historial en tiempo de ejecución

 - `!history config get` — Muestra la configuración actual de historial (history_max_messages, history_persist, history_dir y la whitelist).
 - `!history config set max_messages <N>` — Establece el número máximo de mensajes guardados en memoria por canal/PM (p.e. `!history config set max_messages 20`).
 - `!history config set persist <true|false>` — Activa o desactiva la persistencia a disco del historial (p.e. `!history config set persist true`).
 - `!history config set dir <path>` — Cambia la carpeta donde se guardarán las historias (p.e. `!history config set dir histories`).
 - Si el bot fue iniciado con un archivo `conf` y es editable, los cambios intentarán persistirse automáticamente en ese archivo; si no es posible, el cambio solo quedará en memoria hasta un reinicio.

Ejemplo de uso:
 - Mostrar la configuración: `!history config get` (respuesta privada para el admin)
 - Limitar la historia a 10 mensajes: `!history config set max_messages 10` (persistirá en config si es posible)
 - Desactivar persistencia a disco: `!history config set persist false`

Estas opciones de historial son útiles para dar «contexto» a las respuestas de IA, o para mantener logs con fines de auditoría y restauración. Use `history_whitelist` para evitar persistir historiales de canales que no quieras o que no sean relevantes.

## Comandos exclusivos del creador del bot

Estos comandos solo son operativos para el creador del bot y dentro de una conversación privada con el bot.

### !dentro y !fuera

El bot detecta cuando su creador entra y sale del canal y lo anota en el archivo `log`. Con fines de desarrollo y depuración se dispone de los comandos `!dentro` y `!fuera` que permiten al creador del bot simular su entrada y salida y replicar ciertos mensajes recibidos por el bot en una conversación privada.

### !info

El comando `!info` muestra información sobre el DJ que está emitiendo, el estado de las peticiones y si la réplica de mensajes está activada.

### !join [canal]

El comando `!join [canal]` permite a los administradores del bot indicarle que se una a un canal cualquiera. No es necesario que el administrador se encuentre en ese canal ni que tenga privilegios en él y si el canal no existe se creará uno temporal en el que el bot será el moderador al ser el usuario que crea el canal.

## Comandos para admistradores del bot

Estos comandos están disponibles para la lista de administradores autorizados del bot.

Los comandos se pueden ejecutar tanto en el chat general como en una conversación privada con el bot. La respuesta del bot siempre será en una conversación privada con el administrador.

### !lista

Lista los DJs autorizados del canal

### !guardalista

Guarda la lista en un archivo que será leído cada vez que se active el bot haciendo así permanentes los cambios que se hayan podido producir en la lista de DJs autorizados.

Si se realizan modificaciones y no se archivan solo serán válidas hasta el siguiente reinicio del bot.

### !leelista

El bot lee la lista de DJs autorizados del archivo y la carga en la lista de DJs autorizados del canal. Esto permite modificar la lista manualmente en un editor de texto y cargarla en los datos del canal sin tener que reiniciar el bot.

No es necesario leer la lista del archivo cada vez que se active el bot, ya que éste se lee automáticamente cada vez que se active el bot.

Solo es necesario ejecutar este comando si se han realizado modificaciones del archivo desde un editor externo.

### !borra **nick**

Elimina a **nick** de la lista de DJs autorizados del canal.

### !agrega **nick**

Agrega a **nick** a la lista de DJs autorizados del canal.

### !reconfigura

Hace que el bot vuelva a leer su archivo de configuración JSON y aplique en caliente los valores que se puedan cargar en tiempo de ejecución, sin necesidad de reiniciar el proceso. Es útil cuando se ha editado el archivo de configuración desde un editor externo o cuando se quieren aplicar cambios realizados con comandos como `!prompt save`.

Claves recargadas por `!reconfigura` (seguras en caliente):

- `channels` — lista de canales donde el bot debe unirse; el bot intentará hacer `JOIN` a los nuevos canales.
- `saluda` — lista de canales donde saludar (o `si`/`no` para compatibilidad antigua) y `saludo_cooldown_seconds` — el tiempo de cooldown entre saludos.
- `ai_enabled`, `ai_model` (o `google_model`) — habilita/deshabilita IA y selecciona el modelo IA (validado y con fallback automático si es necesario). Admite modelos Ollama con prefijo `ollama:`.
- `system_prompt_lines` — actualiza el prompt de sistema de la IA.
- `history_max_messages`, `history_dir`, `history_persist`, `history_whitelist` — opciones para la persistencia de historiales y límites locales.
- `DJ`, `limite`, `radio` — valores para la emisión y las peticiones.
- `nick_match_mode`, `nick_aliases` — modo de reconocimiento del nick y lista de aliases.

Nota: `server`, `port`, `nickname` no se actualizan con `!reconfigura` en caliente; para estos cambios será necesario reiniciar el bot.

### !check

Fuerza al bot a verificar su presencia en todos los canales configurados y reconectarse a aquellos de los que se haya desconectado (por ejemplo, tras un "split" de la red IRC). Este comando es útil cuando el bot ha perdido conexión con algún canal pero sigue conectado al servidor.

### !repite <mensaje>

Permite enviar un mensaje a todos los canales configurados como si fuera enviado directamente por el bot. Este comando debe ejecutarse en una conversación privada con el bot. Es útil para hacer anuncios generales en todos los canales simultáneamente.

Ejemplo:
```
!repite La radio estará fuera de servicio por mantenimiento durante 1 hora
```

El bot enviará el mensaje "La radio estará fuera de servicio por mantenimiento durante 1 hora" a todos los canales en los que esté presente.

### Comandos de IA + modelos (administradores)

- `!ia on` — activa respuestas de IA (si está permitida en el config) y el bot responderá cuando se le mencione.
- `!ia off` — desactiva respuestas IA en tiempo de ejecución.
- `!setmodel <modelo>` — establece un modelo IA en tiempo de ejecución; ejemplo: `!setmodel ollama:ministral-3:8b-cloud` o `!setmodel models/gemini-2.5-flash`. Los modelos con prefijo `ollama:` se envían a Ollama; el resto a Google Gemini.
- `!econmodel` — selecciona automáticamente y aplica un modelo económico (bajo coste) entre modelos guardados en `google_models.json`.
- `!listmodels` — descarga la lista de modelos disponibles mediante la API de Google y la guarda en `google_models.json` para inspección.
- `!showmodels` — muestra en el canal/privado la lista de modelos descargados en `google_models.json`.

### Comandos de configuración de saludos (administradores)

 - `!set_saludo_cooldown <segundos>` — cambia el cooldown de saludos en tiempo de ejecución y lo guarda en el archivo de configuración (persistente). Responde siempre en privado al administrador.
 - `!get_saludo_cooldown` — muestra el cooldown actual (responde en privado al administrador).

### Reconocimiento flexible del nick (administradores)

El bot puede funcionar en dos modos de reconocimiento de su nick:

- **strict** (por defecto): el bot solo reacciona cuando su nick exacto aparece en el mensaje (comparación case-insensitive por subcadena). Es el comportamiento clásico.
- **loose**: además del nick exacto, el bot también reconoce una lista configurable de **aliases** (por ejemplo "cepo", "bot", "botijo" para el bot c3pO). Los aliases se comprueban con *word-boundary matching* (`\b`), de forma que "bot" no genera falsos positivos con palabras como "robot" o "botella".

Cuando se detecta una mención por alias, el alias se elimina del texto antes de enviarlo a la IA, igual que se hace con el nick real.

#### Comandos de administración

- `!nickmode strict|loose` — cambia el modo de reconocimiento del nick en tiempo de ejecución. Persiste automáticamente en el archivo de configuración.
- `!nickalias show` — muestra la lista actual de aliases configurados.
- `!nickalias add <alias>` — añade un nuevo alias (sin duplicados, case-insensitive).
- `!nickalias remove <alias>` — elimina un alias de la lista.
- `!nickalias save` — guarda los aliases y el modo actual en el archivo de configuración (persistente entre reinicios).

#### Configuración JSON

Los valores se almacenan en el archivo de configuración del bot:

```json
{
  "nick_match_mode": "loose",
  "nick_aliases": ["cepo", "bot", "botijo"]
}
```

Estos valores se cargan al arrancar y también se recargan con `!reconfigura`.

### Comandos de prompt de sistema (administradores)

Los administradores pueden gestionar el prompt de sistema que se antepone a cada petición de IA. El prompt es una lista de líneas y admite el marcador `{nickname}` que se sustituye por el nick del bot.

- `!prompt show` — muestra las líneas actuales del prompt de sistema (responde en privado).
- `!prompt set <texto>` — reemplaza todo el prompt por una única línea con el texto indicado.
- `!prompt add <texto>` — añade una nueva línea al final del prompt.
- `!prompt del <N>` — elimina la línea número N del prompt (empezando en 1).
- `!prompt reset` — restaura el prompt de sistema a los valores por defecto.
- `!prompt save` — guarda el prompt actual en el archivo de configuración JSON (persistente entre reinicios).

Los comandos anteriores requieren permisos de administrador y el fichero `key` o la variable `GOOGLE_API_KEY` configurada si se desea hacer llamadas en tiempo real.

### `!showconfig` (solo administradores)

- Uso: `!showconfig` (responde en privado)
- Proporciona un resumen legible de la configuración en runtime — canales, `saluda`, `saludo_cooldown_seconds`, IA (`ai_enabled`, `ai_model`), prompt de sistema, opciones de historia (`history_persist`, `history_dir`, `history_max_messages`), y otros valores de interés (`DJ`, `limite`, `radio`).

Ejemplo de respuesta (privado al admin):

```
channels: ['#el_desvan_musical', '#r3']
saluda: ['#el_desvan_musical'] (greet_all=False)
saludo_cooldown_seconds: 20
ai_enabled: True
ai_model: ollama:ministral-3:8b-cloud
system_prompt_lines: 9 líneas
nick_match_mode: strict
nick_aliases: []
history_persist: True (dir=histories)
history_max_messages: 30
DJ: hexadecimal
limite: 0
radio: https://zeno.fm/radio/el_desvan_musical_radio/
```

### Privacidad y uso de mensajes privados (PM)

- El bot no responderá a conversaciones privadas (PM) de usuarios que no sean administradores (`admins`) ni DJs autorizados (`autorizados`). Esto evita que usuarios no privilegiados obtengan información de administración o ayudas privadas.
- Los comandos administrativos se responden siempre por privado al administrador (por ejemplo `!setmodel`, `!set_saludo_cooldown`, `!showconfig`, `!get_saludo_cooldown`).

### `!help` / `!ayuda` (privado)

- Uso: `!help` o `!ayuda` para ver los grupos disponibles; `!help <grupo>` para listar comandos del grupo.
- Responde siempre por privado y devuelve solo los grupos/comandos que el solicitante puede usar según su rol.
- Grupos disponibles por defecto:
	- `general`: comandos generales accesibles a todos (p. ej. `!radio`, `!peticion`)
	- `dj`: comandos para DJs autorizados (p. ej. `!emito`, `!auto`)
	- `ia`: comandos para IA (p. ej. `!ia on`, `!ia off`)
	- `prompt`: comandos para gestionar el prompt de sistema (p. ej. `!prompt show`, `!prompt set`)
	- `history`: comandos para historial (p. ej. `!gethistory`, `!history config`)
	- `admin`: comandos exclusivamente para administradores (p. ej. `!setmodel`, `!set_saludo_cooldown`, `!nickmode`, `!nickalias`, `!showconfig`)

Ejemplo de uso:

```
!help
> Grupos disponibles: general, dj, ia, history

!help admin
> !set_saludo_cooldown <segundos> — Cambia el cooldown de saludos y lo persiste en config
	!get_saludo_cooldown — Muestra el valor actual
	!showconfig — Muestra un resumen de la configuración actual
```

Nota: si hay muchos comandos, el bot devuelve los grupos y permite pedir ayuda específica para cada grupo.

### Comandos para manejo de mensajes programados

Estos comandos permiten a los administradores configurar, visualizar y eliminar mensajes programados que el bot enviará automáticamente en los canales según un intervalo y rango horario definidos. Los comandos deben ejecutarse en una conversación privada con el bot.

#### !mensaje

Inicia un proceso interactivo para configurar un mensaje programado. El bot guiará al administrador paso a paso para definir:
- El intervalo de tiempo en minutos para repetir el mensaje.
- La hora de inicio (formato HH:MM).
- La hora de fin (formato HH:MM).
- La fecha de inicio (formato YYYY-MM-DD o DD/MM/YYYY, opcional).
- La fecha de fin (formato YYYY-MM-DD o DD/MM/YYYY, opcional).
- El texto del mensaje a enviar.

Una vez configurado, el mensaje se enviará a todos los canales del bot dentro del rango horario especificado y con la frecuencia indicada, siempre que haya actividad en el canal o el servidor envíe un PING.

Validaciones: el bot rechaza `fecha_fin` anterior a `fecha_inicio` y solicita reentrada con una advertencia. Para cubrir envíos que cruzan medianoche, use `fecha_fin = fecha_inicio + 1 día`.

Auto-eliminación: cuando una entrada configurada tenga `fecha_fin` definida y la fecha final haya pasado, el bot eliminará permanentemente esa entrada del archivo `nickname_mensajes_programados.json`.

Ejemplo de entrada JSON (`nickname_mensajes_programados.json`):

```json
[{
	"estado": "configurado",
	"nick": "Magari",
	"intervalo": 30,
	"hora_inicio": "09:00",
	"hora_fin": "11:00",
	"fecha_inicio": "2025-12-01",
	"fecha_fin": "2025-12-31",
	"texto_mensaje": "M4ri0n no podrá emitir hoy"
}]
```

Buenas prácticas:

- Para emitir solo un día concreto pon `fecha_inicio` y `fecha_fin` iguales.
- Si la ventana horaria cruza medianoche y quieres incluir la madrugada del día siguiente, establece `fecha_fin` como el día siguiente.

#### !vermensaje

Muestra una lista de todos los mensajes programados en el sistema, sin importar quién los haya configurado. Para cada mensaje, se muestran:
- El intervalo de repetición en minutos.
- La hora de inicio y fin del rango horario.
- La fecha de inicio y de fin (si están definidas).
- El texto del mensaje.
- El nick del administrador que lo creó.

Esto permite a los administradores tener un control completo sobre los mensajes programados activos.

#### !eliminarmensaje

Inicia un proceso interactivo para eliminar un mensaje programado del sistema. El bot muestra una lista numerada de todos los mensajes programados, y el administrador debe seleccionar el número del mensaje que desea eliminar. Una vez eliminado, el mensaje se retira tanto de la lista en memoria como del archivo persistente, asegurando que no se vuelva a enviar.

#### !agregacanalmensaje [canal]

Permite a los administradores agregar un nuevo canal a la lista de canales donde se envían los mensajes programados. El canal debe especificarse con el formato correcto (empezando con #, por ejemplo, #nuevo_canal). Si el canal ya está en la lista, el bot informará que no es necesario agregarlo. Este comando debe ejecutarse en una conversación privada con el bot.

#### !vercanalesmensaje

Muestra la lista actual de canales donde se envían los mensajes programados. Esto permite a los administradores verificar a qué canales están siendo dirigidos los mensajes automáticos. Este comando debe ejecutarse en una conversación privada con el bot.

#### !eliminacanalmensaje [canal]

Permite a los administradores eliminar un canal de la lista de canales donde se envían los mensajes programados. El canal debe especificarse con el formato correcto (empezando con #, por ejemplo, #nuevo_canal). Si el canal no está en la lista, el bot informará que no puede ser eliminado. Este comando debe ejecutarse en una conversación privada con el bot.

## Comandos para DJs autorizados

### Invitar al bot a que se una a otro canal

Cualquier nick en la lista de DJ autorizados puede enviar una invitación al bot para que se una a otro canal. El bot se unirá al canal y enviará un mensaje de saludo al nuevo canal.

Para enviar una invitación al bot debe ejecutar el comando `/invite` seguido del nick del bot desde el canal al que se quiere invitar.

Este comando no es exclusivo del bot sino que se trata de un comando del IRC. El bot solo está programado para aceptarlo si se envía desde un nick autorizado. Al tratarse de un comando del IRC es posible que el nick necesite ser moderador del canal al que invita al bot, o tener algún otro tipo de privilegios en ese canal.

Los administradores del bot pueden saltarse estas restricciones del IRC usando el comando `!join` descrito anteriormente.

### !emito

El DJ informa al bot que comienza a emitir. El bot envia un mensaje público de saludo al DJ que comienza la emisión. A partir de este momento el bot indicará el nombre del DJ en emisión cuando cualquier usuario ejecute el comando `!radio` (ver más adelante en comandos generales). Basta con ejecutar este comendo en uno solo de los canales para que el bot indique el DJ en emisión en todos los demás canales.

### !auto

Informa al bot que la emisión ha terminado y que la radio se pone en modo automático. El bot envía un mensaje a todos los canales indicando que la emisión es automática así como la URL donde se está produciendo.

Si el DJ en emisión se ausenta del canal más de 10 minutos, el bot pasará la emisión a automática.

### !recuento

Si se ha establecido un límite al número de peticiones por usuario el bot muestra la lista de peticiones por ususario. Este comando se usa en una conversación privada con el bot

### !-[número] [nick]

Resta [número] al recuento de peticiones de [nick]. Puede usarse, por ejemplo, si el usuario ha realizado una petición errónea y se le anula, o para permitir que haga alguna petición extra.

### !elimina [numero]

Elimina la petición correspondiente a [número] de la lista de peticiones

### Abrir peticiones

Cuando un DJ desea que los usuarios puedan realizar peticiones de canciones debe indicarlo así en un mensaje subjetivo. El mensaje debe empezar por `/me` y contener alguna forma del verbo `abrir` y la palabra `petición`, con cualquier combinación de mayúsculas, en singular o plural y con o sin acento:

Ejemplos válidos:

- `/me abre petición`
- `/me abre peticiones`
- `/me Abro peticion`
- `/me comunica que ha abierto las peticiones`

El bot indicará en un mensaje general en todos los canales que las peticiones están abiertas y recordará la sintaxis del comando para realizarlas.

Solo los DJs en emisión pueden abrir peticiones. El bot lo indicará así a cualquiera que intente abrir peticiones y no sea el DJ que se encuentra emitiendo en ese momento.

Cuando se abren las peticiones el bot almacena el estado abierto, de forma que si cae, al volver a entrar recuerda el DJ que estaba emitiendo y el estado de las peticiones, por lo que no hay que volverlo a indicar.

Basta con abrir las peticiones en uno solo de los canales para que sean válidas en todos los canales.

### Cerrar peticiones

Cuando un DJ desea que los usuarios no puedan realizar peticiones de canciones debe indicarlo así en un mensaje subjetivo. El mensaje debe empezar por `/me` y contener alguna forma del verbo `cerrar` y la palabra `petición`, con cualquier combinación de mayúsculas, en singular o plural y con o sin acento:

Ejemplos válidos:

- `/me cierra petición`
- `/me cierra peticiones`
- `/me Cierro peticion`
- `/me siente tener que cerrar ya las peticiones`
  
a partir de este momento el bot indicará en un mensaje general en todos los canales que las peticiones están cerradas. Si un usuario realiza una petición le indicará el estado cerrado de las peticiones solo en el canal donde la haya solicitado.

## Peticiones de canciones

Si las peticiones están abiertas por el DJ en emisión cualquier usuario puede solicitar una canción con el comando:

`!peticion cantante-canción-dedicatoria`

El comando puede escribirse con o sin acento y el orden o inclusión de los datos de la petición son libres. El bot enviará al DJ en emisión un mensaje privado con los datos de la petición.

En el chat general el bot enviará un mensaje indicando que la petición ha sido recibida.

Si el nick del bot aparece en la dedicatoria dará las gracias a quien realiza la petición.

La petición es recibida por el DJ en emisión en una conversación privada. Cada petición comienza con # seguido del número de la petición.

los siguentes comandos pueden ser ejecutados por el DJ en emisión en la conversación privada con el bot:

Cuando la petición se encuentra en emisión del DJ puede indicarle al bot en esta conversacion privada que la petición se está emitiendo usando el comando:

`!suena ` 

seguido del número  de la peticion. Por ejemplo:

`!suena 4`

indica que está sonando la petición número 4 (No hay que olvidar el espacio antes del número) El bot emitirá un mensaje en el chat general indicando que la petición se está emitiendo. La petición indicada se borra entonces de la lista de peticiones pendientes.

`!petlista`

escribe en la conversación privada la lista de peticiones pendientes.

`!ignora Nick`

El bot ignorará en adelante las peticiones de ese Nick. Puede usarse para protegerse de trolls o ataques que intenten saturar al bot con peticiones. El Nick se añade a una lista de usuarios ignorados que solo es válida para la ejecución en curso del bot. La lista se borra si el bot reinicia.

`!admite Nick`

Retira a Nick de la lista de usuarios ignorados. A partir de ese momento el usuario puede realizar peticiones

`!iglista`

El bot muestra una lista de los usuarios ignorados.

`!limite #`

donde # se sustituye por el número máximo de peticiones por usuario

`!muestralimite`

muestra el número límite de peticiones por usuario para esta sesión

`!limite 0`

Inactiva la limitación de peticiones

El límite, si existe, se restablece al terminar la emisión

Los comandos de límite solo funcionan en una conversación privada con el bot y el límite se puede alterar en cualquier momento dentro de la misma emisión. El bot mostrará en el chat general información sobre las peticiones que lleva cada usuario y avisará cuando se alcance o se supere el número máximo.

## Ayuda para peticiones

Si un mensaje contiene el nick del bot y un derivado de la palabra petición, el bot enviará un mensaje indicando como se hacen las peticiones. Esto permite solicitarle ayuda en un lenguaje natural.

Ejemplos válidos para un bot cuyo nick sea `Robot` son:

- **Robot, me gustaría saber como hacer peticiones**
- **Por favor, Robot, explícale a [nick] como se hacen las peticiones**
- **Me gustaría saber como hacer peticiones por favor, Robot**

### Sacar al bot del IRC

Cualquier DJ autorizado puede indicarle al bot que salga del IRC. Puede ser por alguna situación de emergencia por un mal funcionamiento del bot o para reiniciarlo tras efectuar cambios en el código. Para ello se le debe mencionar en un mensaje que contenga algún derivado del verbo recargar.

Ejemplos válidos:

- **Robot salte a hacer una recarga**
- **Robot, recarga**
- **Robot, salte a recargar las baterías, por favor**

El bot se desconectará del IRC. Se podrá volver a conectar y que entre a los canales indicados en su configuración desde una página web de control del bot o ejecutándolo manualmente desde el servidor donde esté alojado.

También se le puede indicar que solo salga de un canal enviándole el mensaje:

- **Robot sal del canal**

en el canal que se desea que abandone.

Sin embargo esta orden es solo temporal ya que el bot se volverá a unir al canal de manera automática la siguiente vez que reciba un PING del servidor. Esta función hará que el bot vuelva a unirse al canal si por alguna razón ha salido, como a causa de un split, por ejemplo.

## Comandos generales

Estos comandos pueden ser ejecutados por cualquier usuario y se responden en el canal donde se ejecutan

## Refranes

El comando se ejecuta cuando un nick cualquiera envía un mensaje al canal con las siguientes caraterísticas:

- el mensaje contiene el nick del bot. De esta forma el bot sabe que se están dirigiendo a él.
- el mensaje contiene la palabra refrán, con o sin acento.

esto permite solicitar un refrán al bot en un lenguaje natural ya que el resto del contenido del mensaje se ignora.

Ejemplos válidos para un bot cuyo nick sea `Robot` son:

- **Robot dime un refrán**
- **Por favor Robot, dime un refran**
- **Me gustaría leer un refran de Robot**

ya que todos ellos contienen el nick del bot y la palabra refrán

## Chistes

El comando se ejecuta cuando un nick cualquiera envía un mensaje al canal con las siguientes características:

- el mensaje contiene el nick del bot.
- el mensaje contiene la palabra chiste.

Esto permite solicitar un chiste al bot en lenguaje natural.

Ejemplos válidos para un bot cuyo nick sea `Robot` son:

- **Robot cuéntame un chiste**
- **¿Sabes algún chiste, Robot?**

## Horóscopos

Cuando se recibe un mensaje que contiene el nick del bot y la palabra `horoscopo`, con o sin acento, y el nombre de un signo zodiacal el bot responde con el horóscopo del día correspondiente a ese signo.

Los horóscopos tardan un tiempo en actualizarse por lo que pasadas las 00:00 horas puede responder aún con el horóscopo del día anterior.

Los signos pueden escribirse con cualquier combinación de mayúsculas y minúsculas y con o sin acentos, el bot tratará de reconocer a qué signo zodiacal se refiere el usuario.

Si no se identifica ningún signo zodiacal el bot responderá con un mensaje público dirigido al usuario indicando el error.

Ejemplos válidos para un bot cuyo nick sea `Robot` son:

- **Robot, dime el horóscopo de acuario**
- **Robot, Me gustaría leer el horóscopo de Acuario**
- **Me gustaría saber el horóscopo de acuario por favor, Robot**

Ya que todos ellos contienen la palabra horóscopo, el signo zodiacal y el nick del bot.

## !radio

El bot escribe mensajes todos los canales indicando la URL de la radio y el nick del DJ que se encuentra en emisión (el que previamente ha ejecutado el comando `!emito`) y el estado de las peticiones.

En el canal de la radio el bot también pondrá un enlace a la web de la radio.

## "Bugs" or "Features"

Pueden darse situaciones erróneas debidas a la flexibilidad de los comandos del bot. Por ejemplo si aparece el nick del bot y algún derivado de la palabra `petición` en un mensaje a cualquier otro usuario el bot responderá indicando cómo se hace una petición, aunque no se le haya preguntado. Es el precio que hay que pagar por la flexibilidad de los comandos. A su vez esto hace al bot mas **humano**. ¡Nadie es perfecto!

También pueden darse situaciones externas que no podemos controlar. Por ejemplo se ha detectado que si se hace un uso continuado de comandos en una conversación privada con el bot, el IRC puede llegar a bloquear los mensajes del bot a esa conversación. Esto suele ocurrir cuando los mensajes del bot son largos. En un uso "normal" de los comandos no suele darse esta situación.

[**Solucionado**] Cuando se da una acumulación de mensajes al bot es posible que se repita alguno de ellos, aunque se haya enviado una sola vez.

## Integración con IA (Ollama y Google Gemini)

El bot puede usar modelos generativos para contestar a los usuarios cuando se le menciona en el chat o en privado. Soporta dos proveedores de IA:

### Proveedores

- **Ollama** (local o cloud): modelos se identifican con el prefijo `ollama:` (ej. `ollama:ministral-3:8b-cloud`). Requiere una instancia de Ollama accesible en `http://localhost:11434`.
- **Google Gemini**: modelos sin prefijo `ollama:` (ej. `models/gemini-2.5-flash`). Requiere la API key en el archivo `key` o en la variable de entorno `GOOGLE_API_KEY`.

### Módulo unificado `ai_api.py`

Toda la lógica de enrutamiento se centraliza en `ai_api.py`, que expone:

- `ask_ai(prompt, model, fallback, temperature)` — envía el prompt al proveedor correcto (Ollama o Google) según el prefijo del modelo. Si el modelo falla y `fallback=True`, recorre una cadena de fallback (por defecto `gemini-2.5-flash`).
- `validate_model(model)` — comprueba que el modelo es accesible (ping a Ollama o validación con la API de Google).
- `get_display_name(model)` / `get_provider_name(model)` — devuelven el nombre corto del modelo y el nombre del proveedor (`Ollama` o `Google`).

### Configuración

- `ai_model` (o `google_model` por compatibilidad): el modelo a usar. Ejemplo: `"ollama:ministral-3:8b-cloud"`. Si no se define se usa `ollama:ministral-3:8b-cloud` por defecto.
- `ai_channels`: lista de canales donde la IA está habilitada.
- `system_prompt_lines`: lista de líneas del prompt de sistema; admite `{nickname}` como marcador.
- Los administradores pueden activar/desactivar la IA con `!ia on` / `!ia off`.
- Los administradores pueden cambiar el modelo en tiempo de ejecución con `!setmodel <modelo>`.
	- También pueden pedir al bot que seleccione un modelo económico automáticamente: `!econmodel`.

### Cadena de fallback

Si el modelo principal falla, el bot intentará automáticamente con los modelos de la cadena de fallback (definida en `ai_api.py`). El orden por defecto es:

1. Modelo configurado (ej. `ollama:ministral-3:8b-cloud`)
2. `gemini-2.5-flash` (Google)

En público el bot enviará un mensaje amistoso de disculpa cuando ocurra un fallo con la IA; en privado se notificará a `hexadecimal` con una descripción corta y la ruta del fichero con la traza.

### Prompt de sistema

El prompt de sistema se antepone a cada petición de IA para dar contexto e instrucciones al modelo. Se puede personalizar con los comandos `!prompt` (ver sección de comandos de administrador).

El prompt por defecto incluye instrucciones de idioma, formato de respuesta, limitaciones de longitud y contexto del canal IRC.

### Scripts de prueba

- `python3 test_list_models.py` — obtiene y guarda modelos disponibles de Google.
- `python3 test_generate_flash_v1beta.py` — prueba el endpoint `v1beta` con el formato `contents`.
- `python3 test_generate_flash.py` — usa `ask_google()` para probar modelos Flash.
- `python3 test_generate_gemini_ask.py` — prueba `ask_google()` con un modelo Gemini.
- `python3 test_generate_gemini.py` — otro test de generación con `ask_google()`.
- `scripts/test_ai_api.py` — tests unitarios del módulo `ai_api.py`.
- `scripts/test_system_prompt.py` — tests del sistema de prompt configurable.

Ten en cuenta las limitaciones de uso de la API, las cuotas y los costes asociados.

## Scripts auxiliares

El proyecto incluye varios scripts shell organizados por función. Todos están pensados para ejecutarse desde el directorio `/srv/http/bot` (o el directorio donde se instale el bot).

### Scripts de arranque (`run-*`)

Estos scripts son el punto de entrada para arrancar el bot. El crontab de root los invoca cada minuto para garantizar que el bot se mantenga activo (ver sección *Mantenimiento automático*).

| Script | Descripción |
|--------|-------------|
| `run-http-c3pO.sh` | Ejecuta `runcheck-c3pO.sh` directamente (el crontab corre como root). Es el script activo en producción para el bot c3pO. |
| `run-http-c3p0.sh` | Ejecuta `runcheck-c3p0.sh` como usuario `http` (`sudo -u http`). Variante para servidores donde el bot debe correr como usuario `http`. |
| `run-http-r3.sh` | Ejecuta `runcheck-r3.sh` como usuario `http`. Para el bot r3 (actualmente desactivado en crontab). |
| `run-www-data-c3pO.sh` | Ejecuta `runcheck-c3pO-www-data.sh` como usuario `www-data`. Variante para servidores Debian/Ubuntu donde el usuario web es `www-data`. |
| `run-www-data-c3p0.sh` | Igual que el anterior pero para c3p0, apuntando a `/var/www/html/bot/`. |
| `run-www-data-r3.sh` | Igual que el anterior pero para r3, apuntando a `/var/www/html/bot/`. |

**¿Cuál usar?** Depende del usuario del sistema bajo el que se quiera ejecutar el bot y de la ruta de instalación:
- Arch Linux / usuario `http` → `run-http-*.sh`
- Debian/Ubuntu / usuario `www-data` → `run-www-data-*.sh`
- Ejecución directa como root → `run-http-c3pO.sh` (que no hace `sudo`)

### Scripts de comprobación (`runcheck-*`)

Cada script `runcheck` comprueba si el proceso del bot ya está en ejecución (vía `pgrep`). Si lo está, muestra el PID; si no, lo arranca en segundo plano.

| Script | Bot | Notas |
|--------|-----|-------|
| `runcheck-c3pO.sh` | c3pO | Activa el entorno virtual (`.venv`) antes de arrancar. **Script activo en producción.** |
| `runcheck-c3pO-www-data.sh` | c3pO | Igual pero pensado para ser invocado vía `sudo -u www-data`. Activa `.venv`. |
| `runcheck-c3p0.sh` | c3p0 | Versión sin entorno virtual (legacy). |
| `runcheck-c3p0-www-data.sh` | c3p0 | Idem, apunta a `/var/www/html/bot/`. |
| `runcheck-r3.sh` | r3 | **Desactivado** (`exit 1` al inicio). |
| `runcheck-r3-www-data.sh` | r3 | Apunta a `/var/www/html/bot/`. |

### Scripts de reinicio (`restart-*`)

Matan el proceso del bot (`kill -9`) y lo vuelven a arrancar inmediatamente. Útiles para aplicar cambios en el código o la configuración que no se puedan recargar con `!reconfigura`.

| Script | Bot |
|--------|-----|
| `restart-c3pO.sh` | c3pO |
| `restart-c3p0.sh` | c3p0 |
| `restart-r3.sh` | r3 |

**Cuidado:** `restart-c3p0.sh` tiene un bug conocido: comprueba el proceso de `r3.json` pero mata el de `c3p0.json`. Si se usa este script conviene revisarlo.

### Script de tests (`run-tests.sh`)

Ejecuta la suite de tests de integración contra la API de Google (los de la carpeta `tests/`). Configura `PYTHONPATH` para que los imports funcionen correctamente.

```bash
./run-tests.sh
```

Los tests que ejecuta son: `test_list_models`, `test_generate_gemini`, `test_generate_gemini_ask`, `test_generate_flash_v1beta` y `test_generate_flash` (con `models/gemini-2.5-flash`).

### Script de permisos (`permisos.sh`)

Cambia recursivamente el grupo de todos los archivos del directorio a `web` y otorga permisos de escritura al grupo. Útil después de hacer `git pull` o crear archivos nuevos para que el usuario del servidor web pueda acceder a ellos.

```bash
sudo ./permisos.sh
```

## Tests

El proyecto tiene dos carpetas de tests:

### `tests/` — Tests de integración con la API de Google

Requieren una API key válida y conexión a Internet. Se ejecutan con `./run-tests.sh` o individualmente:

| Script | Descripción |
|--------|-------------|
| `test_list_models.py` | Obtiene y guarda los modelos disponibles de Google. |
| `test_generate_flash.py` | Genera texto usando `ask_google()` con un modelo Flash. |
| `test_generate_flash_v1beta.py` | Prueba el endpoint `v1beta` con formato `contents`. |
| `test_generate_gemini.py` | Genera texto con `ask_google()` y un modelo Gemini. |
| `test_generate_gemini_ask.py` | Otro test de generación con `ask_google()`. |
| `select_best_model.py` | Selecciona el mejor modelo disponible según criterios de coste. |

### `scripts/` — Tests unitarios y funcionales del bot

No requieren conexión a Internet (la mayoría). Prueban la lógica interna del bot importando directamente sus módulos:

| Script | Descripción |
|--------|-------------|
| `test_ai_api.py` | Tests del módulo unificado `ai_api.py`. |
| `test_system_prompt.py` | Tests del prompt de sistema configurable. |
| `test_config_utils.py` | Tests de `config_utils.py`. |
| `test_history.py` | Tests del sistema de historial. |
| `test_history_persist.py` | Tests de persistencia del historial a disco. |
| `test_saluda.py` | Tests del sistema de saludos. |
| `test_saluda_join.py` | Tests de saludo al hacer JOIN. |
| `test_greet_cooldown.py` | Tests del cooldown de saludos. |
| `test_default_greet_cooldown.py` | Tests del cooldown por defecto. |
| `test_admin_set_get_saludo_cooldown.py` | Tests de los comandos admin de cooldown. |
| `test_help_and_showconfig.py` | Tests de `!help` y `!showconfig`. |
| `test_reload_configuration.py` | Tests de `!reconfigura`. |
| `test_split_message.py` | Tests de partición de mensajes largos. |
| `test_ask_google_fallback.py` | Tests del fallback de Google. |
| `test_r2d2.py` | Tests específicos de la configuración r2d2. |
| `diag_google.py` | Diagnóstico de conectividad con la API de Google. |
| `http_test_generate.py` | Test HTTP directo contra el endpoint de generación. |

## Mantenimiento automático (crontab)

El bot se mantiene en ejecución permanente mediante tareas programadas en el crontab de root. Si el bot se cae por cualquier motivo (error, desconexión del IRC, reinicio del servidor), el crontab lo volverá a arrancar automáticamente en menos de un minuto.

### Configuración actual del crontab de root

```cron
# Bot c3pO — comprobar cada minuto si está vivo y arrancarlo si no
*/1 * * * * /srv/http/bot/run-http-c3pO.sh

# Bot r3 — desactivado (comentado)
#*/1 * * * * /srv/http/bot/run-http-r3.sh

# Renovación automática del certificado SSL (Let's Encrypt)
15 3 * * * /usr/bin/certbot renew --quiet --post-hook "systemctl reload apache2"
```

### Cómo funciona

1. Cada minuto, cron ejecuta `run-http-c3pO.sh` como root.
2. Ese script llama a `runcheck-c3pO.sh`, que:
   - Activa el entorno virtual `.venv`.
   - Comprueba con `pgrep` si ya existe un proceso `bot3.py c3pO.json`.
   - Si existe → no hace nada (muestra el PID).
   - Si no existe → arranca `python3 ./bot3.py c3pO.json &` en segundo plano.
3. El bot se ejecuta de forma indefinida hasta que se desconecte o se mate el proceso.

### Cuándo intervenir manualmente

| Situación | Acción |
|-----------|--------|
| Cambios en el código que no se pueden recargar con `!reconfigura` | Ejecutar `./restart-c3pO.sh` o matar el proceso y esperar a que cron lo rearranca. |
| Desplegar cambios menores (config recargable) | Usar `!reconfigura` desde el IRC; no hace falta reiniciar. |
| Desactivar un bot temporalmente | Comentar la línea correspondiente en el crontab y matar el proceso. |
| Activar el bot r3 | Descomentar la línea en el crontab y quitar el `exit 1` de `runcheck-r3.sh`. |
| Después de un `git pull` | Ejecutar `sudo ./permisos.sh` y opcionalmente reiniciar el bot. |
| Cambios en el certificado SSL | La renovación es automática (certbot, diaria a las 3:15). |
