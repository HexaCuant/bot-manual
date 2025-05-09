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

## Comandos exclusivos del creador del bot

Estos comandos solo son operativos para el creador del bot y dentro de una conversación privada con el bot.

### !dentro y !fuera

El bot detecta cuando su creador entra y sale del canal y lo anota en el archivo `log`. Con fines de desarrollo y depuración se dispone de los comandos `!dentro` y `!fuera` que permiten al creador del bot simular su entrada y salida y replicar ciertos mensajes recibidos por el bot en una conversación privada.

### !info

El comando `!info` muestra información sobre el DJ que está emitiendo, el estado de las peticiones y si la réplica de mensajes está activada.

### !join [canal]

El comando `!join [canal]` permite al creador del bot indicarle que se una a un canal cualquiera. No es necesario que el creador del bot se encuentre en ese canal ni que tenga privilegios en él y si el canal no existe se creará uno temporal en el que el bot será el moderador al ser el usuario que crea el canal.

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

Este comando hace que el bot vuelva a leer su archivo de configuración. Permite realizar cambios en la configuración del bot con un editor externo y cargarlos sin tener que reiniciar al bot.

### Comandos para manejo de mensajes programados

Estos comandos permiten a los administradores configurar, visualizar y eliminar mensajes programados que el bot enviará automáticamente en los canales según un intervalo y rango horario definidos. Los comandos deben ejecutarse en una conversación privada con el bot.

#### !mensaje

Inicia un proceso interactivo para configurar un mensaje programado. El bot guiará al administrador paso a paso para definir:
- El intervalo de tiempo en minutos para repetir el mensaje.
- La hora de inicio (formato HH:MM).
- La hora de fin (formato HH:MM).
- El texto del mensaje a enviar.

Una vez configurado, el mensaje se enviará a todos los canales del bot dentro del rango horario especificado y con la frecuencia indicada, siempre que haya actividad en el canal o el servidor envíe un PING.

#### !vermensaje

Muestra una lista de todos los mensajes programados en el sistema, sin importar quién los haya configurado. Para cada mensaje, se muestran:
- El intervalo de repetición en minutos.
- La hora de inicio y fin del rango horario.
- El texto del mensaje.
- El nick del administrador que lo creó.

Esto permite a los administradores tener un control completo sobre los mensajes programados activos.

#### !eliminarmensaje

Inicia un proceso interactivo para eliminar un mensaje programado del sistema. El bot muestra una lista numerada de todos los mensajes programados, y el administrador debe seleccionar el número del mensaje que desea eliminar. Una vez eliminado, el mensaje se retira tanto de la lista en memoria como del archivo persistente, asegurando que no se vuelva a enviar.

#### !agregacanalmensaje [canal]

Permite a los administradores agregar un nuevo canal a la lista de canales donde se envían los mensajes programados. El canal debe especificarse con el formato correcto (empezando con #, por ejemplo, #nuevo_canal). Si el canal ya está en la lista, el bot informará que no es necesario agregarlo. Este comando debe ejecutarse en una conversación privada con el bot.

#### !vercanalesmensaje

Muestra la lista actual de canales donde se envían los mensajes programados. Esto permite a los administradores verificar a qué canales están siendo dirigidos los mensajes automáticos. Este comando debe ejecutarse en una conversación privada con el bot.


## Comandos para DJs autorizados

### Invitar al bot a que se una a otro canal

Cualquier nick en la lista de DJ autorizados puede enviar una invitación al bot para que se una a otro canal. El bot se unirá al canal y enviará un mensaje de saludo al nuevo canal.

Para enviar una invitación al bot debe ejecutar el comando `/invite` seguido del nick del bot desde el canal al que se quiere invitar.

Este comando no es exclusivo del bot sino que se trata de un comando del IRC. El bot solo está programado para aceptarlo si se envía desde un nick autorizado. Al tratarse de un comando del IRC es posible que el nick necesite ser moderador del canal al que invita al bot, o tener algún otro tipo de privilegios en ese canal.

El creador del bot puede saltarse estas restricciones del IRC usando el comando `!join` descrito anteriormente.

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

También se l e puede indicar que solo salga de un canal enviándole el mensae:

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
