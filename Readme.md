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

### Comandos en privado

!lista (muestra listado de DJs)

!guardalista (guarda listado de DJs)

!leelista (lee archivo de DJs)

!borra [Nick] (borra Dj de la lista)

!agrega [Nick] (agrega DJ a lista)

!reconfigura (lee archivo de configuración)

### Comandos para manejo de mensajes programados

!mensaje (inicia la configuración de un mensaje programado con intervalo, hora de inicio, hora de fin y texto)

!vermensaje (muestra todos los mensajes programados en el sistema)

!eliminarmensaje (permite seleccionar y eliminar un mensaje programado del sistema)

## creador del bot

!dentro

!fuera

!info

!join [canal]
