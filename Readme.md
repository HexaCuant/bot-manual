# Lista de comandos

``` markdown
Entre paréntesis se indica lo que hace el comando.

Los * pueden sustituirse por cualquier cantidad de caracteres. ejemplo:

- petici* -> petición, peticion, peticiones, etc. y se pueden añadir palabras extra.

/en cualquier orden/ las palabras pueden aparecer en cualquier orden en la frase
y se pueden añadir palabra extra.

[bot] debe sustituirse por el nick del bot

[número] debe sistituirse por el número de la petición

[Nick] debe sustituirse por el nick del usuario
```

## DJs

### comandos de DJ

/invite (comando de IRC: invita a un canal)

!emito (comienza la emisión)

!auto (termina la emisión)

/me abr\* petici\* (abrir peticiones-caracteres mínimos)

/me cierr\* petici\* (cerrar peticiones-caracteres mínimos)

!suena [número] Mensaje de petición en emisión

!petlista (lista de peticiones pendientes)

!ignora [Nick] Ignora peticiones de [Nick]

!admite [Nick] deja de ignorar peticiones de [Nick]

!iglista lista de usuarios ignorados

[bot] recarga* (el bot sale del IRC) Aparte de [bot] y la palabra `recarga` puede añadirse cuaquier otra. ejemplo: [bot] salte a recargar un momento

[bot] sal del canal (el bot sale del canal en el que se le envía el mensaje)

!limite establece el límite de peticiones por usuario

!muestralimite muestra el limite de peticiones por ususario

!elimina [número] Elimina la petición 'número' de la lista de peticiones

!recuento Muestra el recuento de peticiones por usuario

!-[numero] [nick] (ej. !-2 fulanito) resta [número] al número de peticiones de [nick] (ej. resta 2 al número de peticiones de fulanito). ESto puede usarse para permitir alguna petición "extra a un usuario"

## Usuarios

!petici [cantante-canción-dedicatoria] (hacer petición)

[bot] petici* /en cualquier orden/ (el bot dice como se hacen las peticiones). **Ejemplo:** *¿Cómo se hace una petición. [bot]?*

[bot] refran refrán /en cualquier orden/ (cuenta un refrán). **Ejemplo:** *[bot] dinos un refrán*

[bot] chiste /en cualquier orden/ (cuenta un chiste) **Ejemplo:** *Cuéntame un chiste [bot]*

[bot] horóscopo horoscopo [signo] /en cualquier orden/ (zodiaco del día) **Ejemplo:** *[bot] dime el horóscopo de libra*

!radio (información de la radio)

## Administradores

### Comandos de Inteligencia Artificial

!ia on [canal] (activa respuestas IA en el canal especificado o el actual)

!ia off [canal] (desactiva respuestas IA en el canal especificado o el actual)

!setmodel [modelo] (selecciona el modelo IA a utilizar, ej: ollama:ministral-3:8b-cloud, models/gemini-2.5-flash)

!listmodels (descarga y guarda la lista de modelos disponibles de Google)

!showmodels (muestra los modelos guardados en google_models.json)

!econmodel (selecciona automáticamente y aplica un modelo económico de bajo coste)

!gethistory [maxLines] [canal|PM:nick|all] (muestra el historial de conversaciones)

!showhistory [canal|all] (muestra los historiales almacenados con formato detallado)

!clearhistory [canal|all] (limpia el historial del canal especificado o todo)

!whitelist add|remove|show|save [#canal|PM:nick|all] (gestiona la whitelist de históricos)

!history config set [opción] [valor] (configura la persistencia de históricos)

!help [grupo] (muestra ayuda contextual por grupos: general, dj, ia, prompt, history, admin)

!ayuda [grupo] (alias de !help)

[bot] [mensaje] (si el mensaje contiene el nick del bot, responde usando IA)

### Comandos de prompt de sistema

!prompt show (muestra las líneas actuales del prompt de sistema)

!prompt set [texto] (reemplaza todo el prompt por una única línea)

!prompt add [texto] (añade una nueva línea al final del prompt)

!prompt del [N] (elimina la línea número N del prompt)

!prompt reset (restaura el prompt de sistema a los valores por defecto)

!prompt save (guarda el prompt actual en el archivo de configuración)

### Comandos de configuración de saludos

!set_saludo_cooldown [segundos] (cambia y guarda el cooldown de saludos)

!get_saludo_cooldown (muestra el cooldown actual de saludos)

### Comandos de reconocimiento del nick (administradores)

!nickmode strict|loose (cambia el modo de reconocimiento del nick: strict=solo nick exacto, loose=nick + aliases con word-boundary)

!nickalias show (muestra los aliases configurados)

!nickalias add [alias] (añade un alias al bot)

!nickalias remove [alias] (elimina un alias)

!nickalias save (guarda aliases y modo en la configuración)

### Comandos en privado

!lista (muestra listado de DJs)

!guardalista (guarda listado de DJs)

!leelista (lee archivo de DJs)

!borra [Nick] (borra Dj de la lista)

!agrega [Nick] (agrega DJ a lista)

!reconfigura (recarga la configuración en caliente: channels, saluda, ai_model, system_prompt_lines, history, DJ, limite, radio)

!check (fuerza la verificación y reconexión a los canales configurados)

!repite [mensaje] (envía un mensaje a todos los canales desde el bot)

!showconfig (muestra un resumen de la configuración actual del bot)

### Comandos para manejo de mensajes programados

!mensaje (inicia la configuración de un mensaje programado con intervalo, hora de inicio, hora de fin, fecha inicio/fin opcionales y texto)

!vermensaje (muestra todos los mensajes programados en el sistema)

!eliminarmensaje (permite seleccionar y eliminar un mensaje programado del sistema)

!agregacanalmensaje [canal] (agrega un canal a la lista de canales donde se envían mensajes programados)

!vercanalesmensaje (muestra la lista de canales donde se envían mensajes programados)

!eliminacanalmensaje [canal] (elimina un canal de la lista de canales donde se envían mensajes programados)

## creador del bot

!dentro

!fuera

!info

!join [canal]
