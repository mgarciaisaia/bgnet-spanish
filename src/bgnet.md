<!--
Beej's Guide to Network Programming book source

# vim: ts=4:sw=4:nosi:et:tw=72
-->

<!--
	History:

	2.3.2:		socket man page
	2.3.3:		sockaddr_in man page
	2.3.4:		bind, listen man page
	2.3.5:		connect man page
	2.3.6:		listen, perror man page
	2.3.7:		errno man page
	2.3.8:		htonl etc man page
	2.3.9:		close man page, expanded man page leader
	2.3.10:		inet_ntoa, setsockopt man pages
	2.3.11:		getpeername man page
	2.3.12:		send/sendto man pages
	2.3.13:		shutdown man pages
	2.3.14:		gethostname man pages, fix inet_aton links
	2.3.15:		fcntl man page
	2.3.16:		recv/recvfrom man page
	2.3.17:		gethostbyname/gethostbyaddr man page
	2.3.18:		changed GET / to GET / HTTP/1.0
	2.3.19:		added select() man page
	2.3.20:		added poll() man page
	2.3.21:		section on NAT and reserved networks
	2.3.22:		typo fixes in sects "man" and "privnet"
	2.3.23:		added broadcast packets section
	2.3.24:		manpage prototype changed to code, subtitle moved out of title
	2.4.0:		big overhaul, serialization stuff
	2.4.1:		minor text changes in intro
	2.4.2:		changed all sizeofs to use variable names instead of types
	2.4.3:		fix myaddr->my_addr in listener.c, sockaddr_inman example
	2.4.4:		fix myaddr->my_addr in server.c
	2.4.5:		fix 14->18 in son of data encap
	3.0.0:		IPv6 overhaul
	3.0.1:		sa-to-sa6 typo fix
	3.0.2:		typo fixes
	3.0.3:		typo fixes
	3.0.4:		cut-n-paste errors, selectserver hints fix
	3.0.5:		typo fixes
	3.0.6:		typo fixes
	3.0.7:		typo fixes, added front matter
	3.0.8:		getpeername() code fixes
	3.0.9:		getpeername() code fixes, this time fer sure
	3.0.10:		bind() man page code fix, comment changes
	3.0.11:		socket syscall section code fix, comment changes
	3.0.12:		typos in "IP Addresses, structs, and Data Munging"
	3.0.13:		amp removals, note about errno and multithreading
	3.0.14:		type changes to listener.c, pack2.c
	3.0.15:		fix inet_pton example
	3.0.16:		fix simple server output, optlen in getsockopt man page
	3.0.17:		fix small typo
	3.0.18:		reverse perror and close calls in getaddrinfo
	3.0.19:		add notes about O_NONBLOCK with select() under Linux
	3.0.20:		fix missing .fd in poll() example
	3.0.21:		change sizeof(int) to sizeof yes
    3.0.22:     C99 updates, bug fixes, markdown
    3.0.23:     Book reference and URL updates
    3.1.0:      Section on poll()
    3.1.1:      Add WSL note, telnot
    3.1.2:      pollserver.c bugfix
    3.1.3:      Fix freeaddrinfo memleak
    3.1.4:      Fix accept example header files
    3.1.5:      Fix dgram AF_UNSPEC
-->

<!-- prevent hyphenation of the following words: -->
[nh[strtol]]
[nh[sprintf]]
[nh[accept]]
[nh[bind]]
[nh[connect]]
[nh[close]]
[nh[getaddrinfo]]
[nh[freeaddrinfo]]
<!--
Don't know how to make this work with underscores. I love
you, Knuth, but... daaahm.

[nh[gai_strerr]]
-->
[nh[gethostname]]
[nh[gethostbyname]]
[nh[gethostbyaddr]]
[nh[getnameinfo]]
[nh[getpeername]]
[nh[errno]]
[nh[fcntl]]
[nh[htons]]
[nh[htonl]]
[nh[ntohs]]
[nh[ntohl]]
<!--
[nh[inet_ntoa]]
[nh[inet_aton]]
[nh[inet_addr]]
[nh[inet_ntop]]
[nh[inet_pton]]
-->
[nh[listen]]
[nh[perror]]
[nh[strerror]]
[nh[poll]]
[nh[recv]]
[nh[recvfrom]]
[nh[select]]
[nh[setsockopt]]
[nh[getsockopt]]
[nh[send]]
[nh[sendto]]
[nh[shutdown]]
[nh[socket]]
[nh[struct]]
[nh[sockaddr]]
<!--
[nh[sockaddr_in]]
[nh[in_addr]]
[nh[sockaddr_in6]]
[nh[in6_addr]]
-->
[nh[hostent]]
[nh[addrinfo]]
[nh[closesocket]]

# Introducción

¡Hey! ¿Te frustra la programación con sockets? ¿Te parece que es
demasiado complejo como para entenderlo con las páginas del `man`?
¿Querés programar cosas copadas con la Internet, pero no tenés tiempo
de cruzarte mil `struct`s tratando de entender si tenés que llamar a
`bind()` antes que a `connect()`, y demás?

Bueno, ¡adiviná! Yo ya pasé por todo ese proceso horrible, ¡y muero por
compartir esa información con todo el mundo! Viniste al lugar correcto.
Este documento debería darle a cualquier persona que programe en C
competentemente la cancha que necesita para entender todo esto de la
programación en red.

Y, todavía más: finalmente me puse al día con el futuro (justo a tiempo)
y ¡actualicé la Guía para IPv6! ¡Que la disfrutes!

## Público

Este documento fue escrito como un tutorial, no como una referencia
completa. Probablemente lo aprovechen mejor quienes estén empezando a
programar con sockets y estén buscando un punto de apoyo. Bajo ningún
punto de vista es la guía _completa y total_ de programación de
sockets.

Dicho esto, con algo de suerte es más que suficiente como para que esas
páginas del `man` empiecen a tener sentido... `:-)`


## Plataforma y compilador

El código contenido en este documento fue compilado en una PC con Linux,
usando el compilador Gnu's [ix[compilers!gcc]] `gcc`. Debería, de todos
modos, compilar en cualquier otra plataforma que use `gcc`.
Naturalmente, esto no aplica si estás programando para Windows - fijate
la [sección sobre programación en Windows](#windows), más abajo.


## Página oficial y libros a la venta

La ubicación oficial de la versión original en inglés de este documento
es:

* [`https://beej.us/guide/bgnet/`](https://beej.us/guide/bgnet/)

Ahí también podés encontrar ejemplos de código y traducciones de esta
guía a varios idiomas.

Para comprar una bonita copia impresa (hay quienes las llaman "libros"),
visitá:

* [`https://beej.us/guide/url/bgbuy`](https://beej.us/guide/url/bgbuy)

Te agradeceré la compra, porque me ayuda a sostener mi estilo de vida
de escritor de documentación.

Esta traducción en español está en:

* [`https://mgarciaisaia.github.io/bgnet-spanish/`](https://mgarciaisaia.github.io/bgnet-spanish/)

## Nota para programadores Solaris/SunOS {#solaris}

Al compilar para [ix[Solaris]] Solaris o [ix[SunOS]] SunOS, necesitás
especificar algunos parámetros extra en la línea de comandos para
linkear las bibliotecas correspondientes. Para hacerlo, simplemente
agregá "`-lnsl -lsocket -lresolv`" al final de cada comando de
compilación, así:

```
$ cc -o server server.c -lnsl -lsocket -lresolv
```

Si aún así obtenés errores, probá agregándole también un `-lxnet` al
final de eso. No sé qué es lo que hace exactamente, pero parece que
algunas personas lo necesitan.

También podrías tener problemas en las llamadas a `setsockopt()`. El
prototipo difiere del de mi máquina Linux, así que en lugar de:

```{.c}
int yes=1;
```

Poné esto:

```{.c}
char yes='1';
```

Como no tengo una máquina Sun, no pude probar nada de esta información -
es, simplemente, lo que algunas personas me dijeron por email.


## Nota para programadores Windows {#windows}

En este punto en la guía, históricamente le pegaba un poco a
[ix[Windows]] Windows, simplemente porque no me gusta demasiado. Pero
debo ser justo y decirte que Windows tiene una base de usuari@s enorme,
y obviamente es un sistema operativo perfectamente usable.

Dicen que la ausencia hace crecer el cariño, y, en este caso, creo que
es cierto (o quizá es la edad). Pero lo que puedo decir es que después
de más de una década de no usar sistemas operativos de Microsoft para mi
trabajo personal, ¡soy mucho más feliz! Tal es así que puedo relajarme y
decir tranquilamente "Sí, ¡sentite libre de usar Windows!"... Ok, sí -
no puedo evitar decirlo apretando los dientes.

Así que sigo alentándote a probar [ix[Linux]]
[fl[Linux|https://www.linux.com/]], [fl[BSD|https://bsd.org/]], o
cualquier otra versión de Unix, en cambio.

Pero a cada persona le gusta lo que le gusta, y a ustedes, personas de
Windows, les encantará saber que esta información también vale para
ustedes en general, a veces con algunos cambios menores.

Una cosa copada que podés hacer es instalar [ix[Cygwin]]
[fl[Cygwin|https://cygwin.com/]], que es una colección de herramientas
de Unix para Windows. Escuché por ahí que usándolo, todos estos
programas compilan sin modificaciones.

Otra cosa que deberías evaluar es [ix[WSL]] [ix[Windows Subsystem For
Linux]] el [fl[Windows Subsystem for
Linux|https://docs.microsoft.com/en-us/windows/wsl/about]]. Esto te
permite básicamente instalar algo como una máquina virtual de Linux en
Windows 10. Con eso, definitivamente deberías estar bien.

Pero hay quienes querrán hacer las cosas en la Forma Pura de Windows. Es
algo muy valiente de su parte, y esto es lo que deberías hacer: ¡salí
corriendo y conseguite un Unix inmediatamente! No, no - era un chiste.
Se supone que debería ser más amistoso con Windows estos días...

Esto es lo que vas a tener que hacer (¡a menos que quieras instalar
[Cygwin](https://cygwin.com/)!): primero que nada, ignorá prácticamente
todos los headers de archivos de sistema que menciono acá. Todo lo que
necesitás incluir (`#include`) es:

[ix[Winsock]]

```{.c}
#include <winsock.h>
```

¡Esperá! También vas a tener que llamar a [ixtt[WSAStartup()]]
`WSAStartup()` antes de hacer cualquier otra cosa con la biblioteca de
sockets. El código para hacerlo tiene esta pinta:

```{.c .numberLines}
#include <winsock.h>

{
    WSADATA wsaData;   // si esto no funciona
    //WSAData wsaData; // proba con esto otro

    // MAKEWORD(1,1) para Winsock 1.1, MAKEWORD(2,0) para Winsock 2.0:

    if (WSAStartup(MAKEWORD(1,1), &wsaData) != 0) {
        fprintf(stderr, "WSAStartup failed.\n");
        exit(1);
    }
```

También tenés que decirle a tu compilador que linkee la biblioteca
Winsock, en general llamada `wsock32.lib` o `winsock32.lib`, o
`ws2_32.lib` para Winsock 2.0. En VC++, esto lo hacés en el menú
`Project`, dentro de `Settings...`. Clickeá la pestaña `Link`, y buscá
la caja titulada "Object/library modules". Agregá "wsock32.lib" (o la
biblioteca que prefieras) a esa lista.

O así dicen.

Por último, tenés que llamar a [ixtt[WSACleanup()]] `WSACleanup()`
cuando termines de usar los sockets. Consultá tu ayuda online para más
detalles.

Una vez hecho esto, el resto de los ejemplos en este tutorial deberían
funcionar en general, con algunas excepciones. Por ejemplo, no podés
usar `close()` para cerrar un socket - tenés que usar
[ixtt[closesocket()]] `closesocket()` en su lugar. Además,
[ixtt[select()]] `select()` sólo funciona con descriptores que sean
sockets, y no con archivos (como `0` para `stdin`).

También podés usar una clase de sockets, [ixtt[CSocket]] `CSocket`.
Consultá la documentación de tu compilador para más información.

Para más información sobre Winsock, leé las [ix[Winsock!FAQ]]
[fl[Preguntas frecuentes de Winsock|https://tangentsoft.net/wskfaq/]] y
andá viendo.

Finalmente, escuché que Windows no tiene una system call [ixtt[fork()]]
`fork()`, que, lamentablemente, uso en algunos de mis ejemplos. Quizá
tengas que linkear alguna biblioteca POSIX o algo así para que hacerlos
funcionar, o podés usar [ixtt[CreateProcess()]] `CreateProcess()` en su
lugar. `fork()` no recibe parámetros, y `CreateProcess()` recibe algo
así como 48 mil millones de parámetros. Si eso no es lo tuyo,
[ixtt[CreateThread()]] `CreateThread()` es algo más sencilla de usar...
Lamentablemente, discutir sobre multithreading está más allá del alcance
de este documento. ¡No puedo hablar de todo!


## Política de emails

Estoy disponible para ayudar con [ix[email to Beej]] preguntas por email
así que sentite libre de escribirme, pero no puedo garantizar una
respuesta. Tengo una vida bastante ocupada, y hay veces en que
simplemente no puedo contestarte preguntas. En esos casos, generalmente
borro el mensaje. No es nada personal: simplemente nunca voy a tener el
tiempo de darte la respuesta detallada que necesitás.

Como regla, mientras más compleja sea la pregunta, menos probable es que
vaya contestarla. Si podés acotar tu pregunta antes de enviarla, y te
asegurás de incluir toda la información relevante (como plataforma,
compilador, mensajes de error que estás recibiendo, y cualquier otra
cosa que creas que pueda ayudarme a entender el problema), más probable
es que consigas una respuesta. Para más indicaciones, leé el documento
de ESR, [fl[How To Ask Questions The Smart
Way|http://www.catb.org/~esr/faqs/smart-questions.html]].

Si no conseguís una respuesta, seguí intentándolo un poco más, tratá de
encontrar tu respuesta, y si se te sigue escapando, volvé a escribirme
con la nueva información que tengas, y con algo de suerte alcance para
que esta vez te pueda ayudar.

Tras fastidiarte diciéndote cómo sí y cómo no escribirme, quiero que
sepas que _realmente_ aprecio todos los elogios que esta guía recibió a
lo largo de estos años. Es un gran empujón a la moral, y me alegra saber
que se está usando. `:-)` ¡Gracias!


## Mirroring (réplicas)

[ix[mirroring]] Son más que bienvenidas las réplicas de este sitio,
tanto las públicas como las privadas. Si querés publicar una réplica
pública del sitio y que la enlace desde la página principal, escribime a
[`beej@beej.us`](beej@beej.us).


## Nota para traductores

[ix[translations]] Si querés traducir esta guía a otro idioma, escribime
a [`beej@beej.us`](beej@beej.us) y enlazo a tu traducción desde la
página principal. Sentite libre de agregar tu nombre y datos de contacto
a la traducción.

Este documento markdown fuente usa codificación UTF-8.

Por favor, prestá atención a las restricciones de la licencia en la
sección [Copyright, distribución y legales](#legal) más abajo.

Si querés que hostee la traducción, avisame. También puedo linkearla si
preferís hostearla vos - cualquier opción me funciona igual.


## Copyright, distribución, y legales {#legal}

La Beej's Guide to Network Programming es Copyright © 2019 Brian "Beej
Jorgensen" Hall.

Con las excepciones específicas del código fuente y las traducciones,
detalladas debajo, este trabajo está licenciado bajo la licencia
Creative Commons Attribution- Noncommercial- No Derivative Works 3.0.
Para ver una copia de la licencia, visita

[`https://creativecommons.org/licenses/by-nc-nd/3.0/`](https://creativecommons.org/licenses/by-nc-nd/3.0/)

o envía una carta a Creative Commons, 171 Second Street, Suite 300, San
Francisco, California, 94105, USA.

Una excepción específica a la porción "No Derivative Works" (No Obras
Derivadas) de la licencia es la siguiente: esta guía puede ser traducida
libremente a cualquier idioma, siempre que la traducción sea precisa, y
la guía sea reimpresa en su totalidad. Las mismas restricciones de la
licencia que aplican a la obra original también aplican a las
traducciones. Las traducciones también pueden incluir el nombre y datos
de contacto de quien las traduzca.

Por la presente, se otorga al dominio público el código fuente C
presentado en este documento, dejándolo completamente libre de cualquier
restricción de licencia.

Se alienta libremente a que los y las educadores recomienden o provean
copias de este documento a sus estudiantes.

Exceptuando acuerdos por escrito entre ambas partes, el autor ofrece
esta obra "como es", no haciendo representación o garantía alguna de
ningún tipo respecto a la obra, expresa, implícita, legal o de otro
tipo, incluyendo, pero sin limitarse a, garantías de título,
comerciabilidad, idoneidad para un propósito particular, no infracción,
o la ausencia de defectos latentes o de otro tipo, precisión, o
presencia de ausencia de errores, ya sea detectables o no.

Excepto en la medida requerida por la ley aplicable, en ningún caso el
autor será responsable ante usted en cualquier teoría legal por
cualquier daño especial, incidental, consecuentes, punitivo o ejemplar
que surja del uso de este trabajo, incluso si el autor ha sido advertido
de la posibilidad de tales daños.

Contacta a [`beej@beej.us`](mailto:beej@beej.us) para más información.


## Dedicación

Gracias a cada persona que haya ayudado en el pasado y en el futuro a
que escriba esta guía. Y gracias a todas las personas que producen los
paquetes y programas libres que usé para hacer esta guía: GNU, Linux,
Slackware, vim, Python, Inkscape, pandoc, y varios otros. Y, finalmente,
un gran gracias a los (literalmente) miles de ustedes que me han escrito
con sugerencias para mejoras y palabras de aliento.

Dedico esta guía a algunos de mis mayores héroes e inspiraciones en el
mundo de las computadoras: Donald Knuth, Bruce Schneier, W. Richard
Stevens, y a Woz, mis lectores y lectoras, y a la comunidad del software
libre y de código abierto en su totalidad.


## Información sobre la publicación

Este libro fue escrito en Markdown usando el editor vim en una máquina
Arch Linux con herramientas GNU. El "arte" de tapa y los diagramas
fueron hechos con Inkscape. El Markdown se convierte a HTML y LaTex/PDF
con Python, Pandoc y XeLaTex, usando las tipografías Liberation. Toda la
cadena está compuesta por 100% software libre y de código abierto.


# ¿Qué es un socket?

Escuchás hablar de [ix[socket]] "sockets" (_enchufes_) todo el tiempo, y
quizá te estés preguntando qué es lo que son exactamente. Bueno, esto
son: una forma de hablar con otros programas usando
[ix[file descriptor]] descriptores de archivos estándar de Unix.

¿Qué?

Ok - quizá hayas escuchado a algún/alguna hacker Unix decir "¡En Unix,
_todo_ es un archivo!". A lo que puede que se haya estado refiriendo esa
persona es a que cuando un programa Unix hace cualquier tipo de E/S, lo
hace leyendo o escribiendo un descriptor de archivo. Un descriptor de
archivo es simplemente un número entero (un `int`) asociado a un archvo
abierto. Pero (y acá está el truco) ese archivo puede ser una conexión
de red, una cola FIFO, un pipe (_tubería_), una terminal, un archivo
"real" en el disco, o cualquier otra cosa. ¡Todo en Unix _es_ un
archivo! Así que cuando te quieras comunicar con otro programa a través
de Internet, lo vas a hacer a través de un descriptor de archivos, lo
creas o no.

Puede que en este preciso instante te estés preguntando "¿Y de dónde
saco este descriptor de archivos para comunicación por red, Don
Sabelotodo?" - y, si no, lo voy a contestar de todos modos: llamando a
la rutina de sistema [ixtt[socket()]] `socket()`. Te devuelve el
[ix[socket descriptor]] descriptor de socket, y te comunicás a través de
él usando las llamdas a sistema especializadas para sockets
[ixtt[send()]] `send()` y [ixtt[recv()]] `recv()` ([`man
send`](#sendman), [`man recv`](#recvman)).

"Pero, ¡hey!" puede que estés exclamando en este momento. "Si es un
descriptor de archivos, ¿por qué demonios no puedo simplemente usar las
llamadas [ixtt[read()]] `read()` y [ixtt[write()]] `write()` normales
para comunicarme a través del socket?". Y la respuesta corta es "Podés,
pero [ixtt[send()]] `send()` y [ixtt[recv()]] `recv()` te ofrecen mucho
mayor control sobre la transmisión de datos."

¿Qué sigue? Esto: hay montones de tipos de sockets. Hay
[ix[socket!types]] direcciones de Internet DARPA (sockets de Internet),
nombres de ruta en un nodo local (sockets Unix), direcciones CCITT X.25
(sockets X.25, que tranquilamente podés ignorar), y probabemente muchos
otros tipos dependiendo del sabor de Unix que estés ejecutando. Este
documento sólo se encarga de los primeros: sockets de Internet.


## Dos tipos de sockets de Internet

¿Cómo? [ix[socket!types]] ¿Hay dos tipos de sockets de Internet? Sí.
Buen, no. Estoy mintiendo. Hay más, pero no quería asustarte. Sólo voy a
hablar de dos tipos acá. Excepto por esta oración, donde te voy a decir
que los [ix[raw sockets]] [ix[socket!raw]] "Raw Sockets" (_sockets en
crudo_) también son muy poderosos y deberías leer sobre ellos.

OK, dale. ¿Cuáles son los dos tipos de sockets? Unos son los
[ix[socket!stream]] "Sockets Stream"; los otros son los
[ix[socket!datagram]] "Datagram Sockets", a los que me voy a referir
como "`SOCK_STREAM`" y "`SOCK_DGRAM`" respectivamente. A los sockets
datagram a veces se los llama "no-conectados" (aunque se les puede hacer
[ixtt[connect()]] `connect()` si tuvieras muchas ganas; mirá
[`connect()`](#connect) debajo).

Los sockets stream son flujos de comunicación bidireccional confiable y
orientados a conexión. Si enviás dos elementos por un socket en el orden
"1, 2", van a llegar en el orden "1, 2" del otro lado. También van a
llegar sin errores. Estoy tan seguro, incluso, de que van a llegar
libres de errores, que simplemente voy a taparme los oídos y gritar _la
la la la_ si alguien trata de proponer lo contrario.

¿Qué usos tienen los [ix[socket!stream]] sockets stream? Bueno, habrás
escuchado de la aplicación [ix[telnet]] `telnet`, ¿verdad? Bueno, usa
sockets stream. Todos los caracteres que tipeás tienen que llegar en ese
mismo orden, ¿no? También los navegadores web usan el Protocolo de
Transferencia de Hipertexto [ix[HTTP]] (HTTP), que usa sockets stream
para conseguir las páginas. De hecho, si hacés `telnet` a un sitio web
en el puerto 80, tipeás "`GET / HTTP/1.0`" y le das ENTER dos veces, ¡te
va a devolver el HTML de la página!

> Si no tenés `telnet` instalado y no querés instalarlo, o tu `telnet`
> está exquisito a la hora de conectarse, esta guía trae un programa
> simil-`telnet` llamado [flx[`telnot`|telnot.c]]. Debería cubrir todos
> los casos de la guía (notá que telnet es en realidad un
> [flrfc[protocolo de red especificado|854]], y que `telnot` no
> implementa este protocolo en absoluto).

¿Cómo logran los sockets stream este alto nivel de calidad de
transmisión de datos? Usan un protocolo llamado el "Protocolo de Control
de Transmisión", también conocido como [ix[TCP]] TCP (por sus siglas en
inglés; mirá la [flrfc[RFC 793|793]] para información extremadamente
detallada de TCP). TCP se asegura de que tu información llegue de manera
secuencial y libre de errores. Puede que hayas escuchado "TCP" antes
como la mitad de "TCP/IP", donde [ix[IP]] "IP" significa "Protocolo de
Internet" (por sus siglas en inglés; mirá la [flrfc[RFC 791|791]]). IP
se encarga principalmente del routeo en Internet, y no se encarga de la
integridad de los datos.

Piola. [ix[socket!datagram]] ¿Y qué hay de los sockets datagram? ¿Por
qué se los llama sockets "no conectados"? ¿De qué va todo esto? ¿Por qué
no son confiables? Bueno, estos son algunos hechos: si enviás un
datagram, puede que llegue. Puede que llegue fuera de orden. Si llega,
los datos dentro del paquete van a llegar libres de errores.

Los sockets datagram también usan IP para routear, pero no usan TCP;
usan el "Protocolo de Datagramas de Usuario", o [ix[UDP]] "UDP" (por sus
siglas en inglés; mirá la [flrfc[RFC 768|768]]).

¿Por qué son "no conectados"? Bueno, básicamente, porque no tenés que
mantener una conexión abierta como lo necesitás con los sockets stream.
Simplemente armás un paquete, le agregás una cabecera IP con la
información del destino, y lo mandás. No hace falta conectarse. Se los
suele usar o bien cuando no hay un stack TCP disponible, o bien cuando
perder algún que otro paquete no resulte en el fin del universo.
Ejemplos de aplicaciones: `tftp` (protocolo de transferencia de archivos
triviales, un hermaano menor de FTP), `dhcpcd` (un cliente DHCP), juegos
multijugador, streaming de audio, videoconferencias, etc.

"¡Un momento! `tftp` y `dhcpcd` transfieren aplicaciones binarias de un
host a otro! ¡No podés perder datos si esperás que la aplicación
funcione cuando llega del otro lado! ¿Qué clase de magia oscura es
esta?"

Bueno, mi querid@ human@, `tftp` y otros programas similares tienen su
propio protocolo por sobre UDP. Por ejemplo, el protocolo tftp dice que
por cada paquete que se envía, el receptor tiene que enviar otro paquete
de vuelta que diga "OK, ¡lo recibí!" (un paquete "ACK"). Si el emisor
del paquete original no recibe una respuesta en, ponele, cinco segundos,
volverá a transmitir el paquete hasta que finalmente consiga un ACK.
Este procedimiento de reconocimiento es muy importante a la hora de
implementar aplicaciones `SOCK_DGRAM` confiables.

Para aplicaciones que no necesitan esa garantía de confianza como
juegos, audo o video, simplemente podés ignorar los paquetes perdidos, o
quizá ingeniártelas para compensar su ausencia. (Si jugaste Quake, quizá
conozcas la manifestación de este efecto con el término _lag maldito_ -
"accursed lag". En este contexto, el término "maldito" / "accursed"
representa cualquier insulto extremo.)

¿Por qué usarías un protocolo no confiable? Dos razones: velocidad, y
velocidad. Es mucho más rápido mandar-y-olvidarse que llevar registro de
qué cosas llegaron bien y asegurarse que están en orden y todo eso. Si
estás mandando mensajes de chat, TCP es genial; si estás mandando 40
actualizaciones de posición por segundo de los jugadores en el mundo,
quizá no importe tanto si una o dos de ellas se pierden, y entonces UDP
es una buena elección.


## Sinsentidos de bajo nivel y teoría de red {#lowlevel}

Dado que acabo de mencionar el apilamiento de protocolos en capas, es
tiempo de hablar sobre cómo funcionan realmente las redes, y mostrar
algunos ejemplos sobre cómo se construyen los paquetes
[ixtt[SOCK\_DGRAM]] `SOCK_DGRAM`. En la práctica, podés saltearte esta
sección, pero provee un buen trasfondo de todos modos.

![Encapsulamiento de datos](dataencap.pdf "[Diagrama de protocolos encapsulados]")

¡Bueno, chiquis, es hora de aprender sobre [ix[data encapsulation]]
_Encapsulamiento de datos_! Esto es muy, muy importante. Es tan
importante que puede que sólo te enteres que existe si hacés el curso de
redes acá en la Chico State `;-)`. Básicamente, dice esto: un paquete
nace, se lo envuelve ("encapsula") en un [ix[header]] encabezado /
"header" (y, rara vez, un "pie de página" / "footer") por un primer
protocolo (digamos, el [ix[TFTP]] protocolo TFTP), y luego a todo eso
(incluyendo el encabezado TFTP) vuelve a encapsularse por el protocolo
siguiente (digamos, [ix[UDP]] UDP), y luego otra vez por el siguiente
[ix[IP]] (IP), y finalmente una vez más por el protocolo final en la
capa (física) del hardware (digamos, [ix[Ethernet]] Ethernet).

Cuando otra computadora recibe el paquete, el hardware le saca el
encabezado Ethernet, el kernel le saca los encabezados IP y UDP, el
programa TFTP le saca el encabezado TFTP, y ahí finalmente consigue los
datos.

Ahora sí, finalmente puedo hablar sobre el infame [ix[layered network
model]] _Modelo de red en capas_ (también conocido como "ISO/OSI"). Este
Modelo de Red describe un sistema de funcionalidad de red que tiene
varias ventajas sobre los demás modelos. Por ejemplo, podés escribir
programas con sockets que son exactamente los mismos sin importar cómo
se transmiten los datos físicamente (puerto serie, thin Ethernet, AUI, o
lo que sea) porque hay programas en niveles más bajos que lidian con eso
por vos. La topología y hardware de red reales son transparentes a quien
programa los sockets.

Sin más preámbulos, voy a presentar todas las capas del modelo.
Recordalas para tus exámenes de redes:

* Aplicación
* Presentación
* Sesión
* Transporte
* Red
* Enlace de datos
* Física

La capa física es el hardware (serie, Ethernet, etc.). La capa de
aplicación es lo más lejano a la capa física que puedas imaginar - es
donde l@s usuari@s interactúan con la red.

Ahora, este modelo es tan general que probablemente puedas usarlo como
una guía de reparación de automóviles si lo intentaras lo suficiente. Un
modelo de capas más consistente con Unix podría ser:

* Capa de aplicación (_telnet, ftp, etc._)
* Capa de transporte Host-a-Host (_TCP, UDP_)
* Capa de Internet (_IP y routeo_)
* Capa de acceso a red (_Ethernet, wi-fi, o lo que sea_)

A esta altura, puede que ya veas cómo estas capas corresponden al
encapsulamiento de los datos originales.

¿Ves todo lo que hay que hacer para construir un simple paquete? ¡Uff!
¡Y tenés que tipear los encabezados de los paquetes a mano usando
"`cat"`! Nah, mentira. Todo lo que tenés que hacer con los sockets
stream es [ixtt[send()]] `send()` para enviar los datos. Todo lo que
tenés que hacer para los sockets datagrama es encapsular el paquete de
la forma que quieras, y hacerle [ixtt[sendto()]] `sendto()` para
enviarlo. El kernel construye las capas de transporte e internet por
vos, y el hardware se encarga de la capa de acceso a red. Ah, ¡la
tecnología moderna!

Así termina nuestra breve incursión en la teoría de redes. Ah, sí, me
olvidé de decirte todo lo que quería decirte sobre routeo: ¡nada! Así
es, no voy a hablar de ello en absoluto. El router saca el encabezado IP
del paquete, consulta su tabla de routeo, [ix[blah blah blah]] _blah
blah blah_. Revisá la [flrfc[IP RFC|791]] si realmente te interesa. Si
nunca aprendés sobre ello, meh, vas a sobrevivir igual.


# Direcciones IP, `struct`s y manejo de datos

Esta es la parte del juego en la que empezamos a hablar de código, para
variar.

Pero antes, ¡charlemos más cosas que no son código! ¡Yay! Primero,
quiero hablar de las [ix[IP]] direcciones y puertos IP por un momento,
para entenderlo de entrada. Luego hablaremos sobre cómo la API de
sockets almacena y manipula las direcciones IP y otros datos.


## Direcciones IP, versiones 4 y 6

En los viejos tiempos en que Ben Kenobi aún se llamaba Obi Wan Kenobi,
había un sistema de de routeo de red maravilloso llamado El Protocolo
Internet Versión 4, también llamado [ix[IPv4]] IPv4. Tenía direcciones
conformadas por cuatro bytes (también conocidos como cuatro "octetos"),
y eran comunmente escritas en forma de "números y puntos" ("dots and
numbers"), como `192.0.2.111`.

Probablemente las hayas visto.

De hecho, mientras escribo esto, prácticamente todo sitio en Internet
usa IPv4.

Tod@s, incluído Obi Wan, eran felices. Las cosas marchaban bien, hasta
que algún detractor llamado Vint Cerf empezó a alertar que ¡nos
estábamos por quedar sin direcciones IPv4!

(Además de alertar a todo el mundo sobre la Venida del Apocalipsis de
Destrucción y Obscuridad de IPv4, [ix[Vint Cerf]] [fl[Vint
Cerf|https://en.wikipedia.org/wiki/Vint_Cerf]] también es reconocido por
ser el Padre de la Internet. Así que no me veo en posición de poner en
duda su juicio.)

¿Quedarse sin direcciones? ¿Cómo podría suceder esto? Digo, hay algo así
como _miles de millones_ de direcciones IPv4 de 32-bit. ¿Realmente hay
miles de millones de computadoras _ahí afuera_?

Sí.

Además, al principio, cuando sólo existían algunas pocas computadoras y
a cualquiera le parecía que mil millones era un número inalcanzable, a
algunas organizaciones grandes les asignaron generosamente millones de
direcciones IP para su uso propio (algunas como Xerox, MIT, Ford, HP,
IBM, GE, AT&T, y una empresita llamada Apple, para nombrar algunas).

De hecho, si no fuera por varias medidas provisionales, nos habríamos
quedado sin direcciones hace rato ya.

Pero ahora estamos viviendo en una era en la que cada ser humano tiene
una dirección IP; cada computadora, cada calculadora, cada teléfono,
cada parquímetro, y (¿por qué no?) cada cachorrito también.

Entonces nació [ix[IPv6]] IPv6. Dado que Vint Cerf probablemente sea
inmortal (incluso si muriera en su forma física, Dios no lo permita,
probablemente ya esté existiendo como alguna versión híper-inteligente
del programa [fl[ELIZA|https://en.wikipedia.org/wiki/ELIZA]] en alguna
parte de las profundidades de la Internet2), nadie quiere tener que
volver a escucharlo decir "Se los dije" si nos quedamos sin direcciones
en la próxima versión del protocolo Internet.

¿Qué te sugiere esto?

Que necesitamos _muchísimas_ más direcciones. Necesitamos no sólo dos
veces las direcciones que tenemos, ni mil millones de veces, ni mil
trillones de veces esa cantidad, si no _¡79 millones de billones de
trillones la cantidad de direcciones que tenemos!_ ¡A ver si aprenden!

Estarás diciendo "Beej, ¿es cierto esto? Tengo motivos para descreer de
números tan grandes". Bueno, la diferencia entre 32 bits y 128 bits
puede no sonar a mucho; son sólo 96 bits más, ¿no? Pero acordate,
estamos hablando de potencias: 32 bits representa unos 4 mil millones de
números (2^32^), mientras que 128 bits representa unos 340 trillones de
trillones de trillones de números (en realidad, 2^128^). Eso es algo así
como un millón de Internets IPv4 por _cada estrella en el Universo_.

Olvidate de la pinta números-y-puntos de IPv4, también; ahora tenemos
una representación hexadecimal, con cada par de bytes separado por dos
puntos, así:

```
2001:0db8:c9d2:aee5:73e3:934a:a5ae:9551
```

¡Eso no es todo! Un montón de veces, vas a tener direcciones IP con
montones de ceros en ella, y los podés comprimir entre dos dos puntos. Y
podés ignorar los ceros al principio de cada par de bytes. Por ejemplo,
cada uno de estos pares de direcciones son equivalentes:

```
2001:0db8:c9d2:0012:0000:0000:0000:0051
2001:db8:c9d2:12::51

2001:0db8:ab00:0000:0000:0000:0000:0000
2001:db8:ab00::

0000:0000:0000:0000:0000:0000:0000:0001
::1
```

La dirección `::1` es la _dirección de loopback_. Siempre significa
"esta máquina en la que estoy ejecutando ahora". En IPv4, la dirección
de loopback es `127.0.0.1`.

Finalmente, existe un modo de compatibilidad IPv4 para las direcciones
IPv6 que puede que veas. Si querés, por ejemplo, representar la
dirección IPv4 `192.0.2.33` como una dirección IPv6, usás esta notación:
"`::ffff:192.0.2.33`".

Qué divertido todo.

De hecho, es tan divertido que quienes crearon IPv6 han honrosamente
descartado trillones y trillones de direcciones para uso reservado, pero
habiendo tantas, en serio, ¿quién se gastaría en llevar la cuenta? Aún
sobran suficientes para cada hombre, mujer, niñ@, cachorro y parquímetro
en cada planeta de la galaxia. Y, creeme, cada planeta de la galaxia
tiene parquímetros. Sabés que es cierto.


### Subredes

Para organizarse mejor, a veces conviene declarar que "esta primer parte
de esta dirección IP hasta este bit es la _porción de la red_ de la
dirección IP, y que el resto es la _porción del host_".

Por ejemplo, con IPv4, podés tener `192.0.2.12`, y podríamos decir que
los primeros 3 bytes son la red y el último byte son el host. O, dicho
de otra forma, estamos hablando del host `12` en la red `192.0.2.0`
(fijate cómo anulamos el byte que era del host).

Y ahora, ¡sigamos con más información desactualizada! ¿List@? En los
Tiempos Antiguos, había tres "clases" de subredes, donde uno, dos o los
tres primeros bytes de la dirección eran la parte de la red. Si tenías
la suerte de tener un byte para la red y tres para el host, podías tener
24 bits de hosts en tu red (unos 16 millones). Eso era una red "clase
A". El extremo opuesto era la "clase C", con tres bytes para la red, y
uno para el host (256 hosts, menos algunas direcciones reservadas).

Así que, como verás, había sólo algunas pocas redes clase A, montones de
clase C, y alguna cantidad en el medio de clase B.

La porción de red de la dirección IP se describe usando una cosa llamada
_máscara de red_, a la que le hacés un AND bit a bit con la dirección IP
para conseguir su número de red. La máscara de red suele tener una pinta
como `255.255.255.0` (por ejemplo, con esa máscara, si tu IP es
`192.0.2.12`, entonces tu red es `192.0.2.12` AND `255.255.255.0`, que
da `192.0.2.0`).

Lamentablemente, resultó que esto no daba todo el control y precisión
necesario para las eventuales necesidades de la Internet; nos estábamos
quedando sin redes Clase C bastante rápido, y ya nos habíamos quedado
sin Clases A hacía rato, así que ni preguntes. Para remediarlo, Los
Poderes permitieron que la máscara de red sea de cualquier cantidad
arbitraria de bits, no sólo 8, 16 o 24. Entonces podés tener una máscara
de, digamos, `255.255.255.252`, que serían 30 bits de red y 2 bits de
host, permitiendo 4 hosts en la red (notá que la máscara de red
_SIEMPRE_ es un montón de bits 1 seguido por otro montón de bits 0).

Pero es un plomazo usar un choclo de números como `255.192.0.0` como
máscara de red. Primero que nada, la gente no tiene una idea intuitiva
de cuántos bits representa eso, y, segundo, no es muy compacto. Así que
ahí apareció el Nuevo Estilo, y es mucho más feliz. Simplemente ponés
una barra al final de la dirección IP, seguida del número de bits, en
decimal, que componen la parte de red. Así: `192.0.2.12/30`.

O, para IPv6, algo como esto: `2001:db8::/32` o
`2001:db8:5413:4028::9db9/64`.


### Números de puerto

Si hacés memoria, antes te presenté el [Modelo de Red en
Capas](#lowlevel), que tiene a la capa Internet (IP) separada de la Capa
de Transporte Host-a-Host (TCP y UDP). Refrescá eso antes de pasar al
próximo párrafo.

Resulta que además de una dirección IP (usada por la capa IP), hay otra
dirección que necesita TCP (sockets stream) y, coincidentemente, UDP
(sockets datagrama). Es el _número de puerto_. Es un número de 16 bits
que es como la dirección local para la conexión.

Pensá en la dirección IP como la dirección postal de un hotel, y el
número de puerto como el número de habitación. Es una analogía decente;
quizá en algún momento se me ocurra alguna con la industria automotriz.

Digmaos que querés tener una computadora que maneje emails entrantes Y
servicios web - ¿cómo distinguís a ambos en una computadora con una
única dirección IP?

Bueno, los distintos servicios en la Internet tienen distintos números
"conocidos" de puertos. Podés verlos en [fl[la gran Lista de Puertos de
IANA|https://www.iana.org/assignments/port-numbers]] o, si estás en una
máquina Unix, en tu archivo `/etc/servicies`. HTTP (la web) es el puerto
80, telnet es el puerto 23, SMTP es el puerto 25, el juego
[fl[DOOM|https://en.wikipedia.org/wiki/Doom_(1993_video_game)]] usaba el
puerto 666, etc. Los puertos debajo del 1024 suelen ser considerados
especiales y suelen requerir privilegios especiales en el sistema
operativo para usarlos.

¡Y eso es todo!


## Orden de los bytes

[ix[byte ordering]] ¡Por Orden del Reino! ¡Han de haber dos órdenes de
bytes posibles, de aquí en más conocidos como el Flojo y el Magnífico!

Ah re, pero igual sí - uno realmente es mejor que el otro. `:-)`

Realmente no existe manera sencilla de decir esto, así que te lo voy a
vomitar de una: puede que tu computadora te haya estado almacenando los
bytes en orden inverso a escondidas. ¡Lo sé! Nadie quería ser quien te
lo tuviera que decir.

La cosa es que tod@s en la Internet más o menos nos pusimos de acuerdo
en que para representar el número hexadecimal de dos bytes (ponele)
`b34f`, lo almacenás en dos bytes consecutivos: `b3`, seguido del `4f`.
Tiene sentido, y, como diría [fl[Wilford
Brimley|https://en.wikipedia.org/wiki/Wilford_Brimley]], Es Lo Correcto.
Este número, almacenado con su extremo mayor primero, se llama
_Big-Endian_.

Lamentablemente, _algunas_ de las computadoras que hay por ahí en el
mundo (por nombrar, cualquiera con un procesador Intel o compatible)
almacena los bytes invertidos, por lo que `b34f` se guarda en memoria
como el byte `4f` seguido del byte `b3`. Este método de almacenamiento
se llama _Little-Endian_.

Pero esperá, ¡aún no terminé con la terminología! El más sensato
_Big-Endian_ también se llama _Orden (de bytes) de Red_, porque ese es
el orden que nos gusta a quienes nos gustan las redes.

Tu computadora almacena los números en _Orden de Host_. Si es un Intel
80x86, el Orden de Host es Little-Endian. Si es un Motorola 68k, el
Orden de Host es Big-Endian. Si es un PowerPC, el Orden de Host es...
Bueno, ¡depende!

Muchas veces, al construir paquetes o completar estructuras de datos, te
vas a tener que encargar de que tus números de dos o cuatro bytes estén
en el Orden de Red. Pero ¿cómo podés hacer esto si no sabés cuál es tu
Orden de Host nativo?

¡Buenas noticias! Simplemente asumís que tu Orden de Host no es
correcto, y siempre pasás los valores por una función que los convierte
a Orden de Red. La función va a hacer la conversión mágica si necesita
hacerla, y de esta manera tu código puede ser portado a máquinas con
distinto endianness.

Joya. Hay dos tipos de números que podés convertir: `short` (dos bytes)
y `long` (cuatro bytes). Estas funciones también sirven con las
variaciones `unsigned`. Digamos que querés convertir un `short` de Orden
de Host a Orden de Red. Empezá con una "h" de "host", seguí con "to"
(_a_, en inglés), luego una "n" de "network" ("red"), y una "s" de
"short": h-to-n-s, o `htons()` (se lee "Host to Network Short", o "Host
a short de red").

Es casi que muy sencillo (en inglés, al menos)...

Podés usar cualquier combinación de "n", "h", "s" y "l" que quieras,
excepto las realmente estúpidas. Por ejemplo, NO HAY función `stolh()`
("Short a Long de Host") - al menos no para esto. Pero existen:

[ixtt[htons()]] [ixtt[htonl()]] [ixtt[ntohs()]] [ixtt[ntohl()]]

| Función   | Descripción                         |
|-----------|-------------------------------------|
| `htons()` | `h`ost `to` `n`etwork (red) `s`hort |
| `htonl()` | `h`ost `to` `n`etwork (red) `l`ong  |
| `ntohs()` | `n`etwork (red) `to` `h`ost `s`hort |
| `ntohl()` | `n`etwork (red) `to` `h`ost `l`ong  |

Básicamente, vas a querer convertir los números al Orden de Red antes de
enviarlos por el cable, y convertirlos a Orden de Host a medida que los
recibís de la red.

No conozco variante de 64 bits, perdón. Y, si quisieras trabajar con
punto flotante, mirá la sección de [Serialización](#serialization),
bastante más abajo.

Asumí que los números en este documento están en el Orden de Host a
menos que aclare lo contrario.


## Estructuras (`struct`s) {#structs}

Bueno, finalmente llegamos. Es hora de hablar de programación. En esta
sección voy a cubrir varios tipos de datos que usa la interfaz de
sockets, dado que algunos de ellos son realmente complejos de entender.

Primero, el sencillito: un [ix[socket descriptor]] descriptor de socket.
Un descriptor de socket tiene el siguiente tipo:

```{.c}
int
```

Un `int` común.

A partir de acá, las cosas se ponen raras, así que seguí leyendo y
seguime el hilo.

Mi Primer Struct™ - `struct addrinfo`. [ixtt[struct addrinfo]] Esta
estructura es una invención más reciente, y se usa para preparar las
estructuras de direcciones de sockets para su uso subsiguiente. También
se la usa para hacer búsquedas de nombres de hosts ("host name lookups")
y búsquedas de nombres de servicio ("service name lookups"). Todo eso va
a cobrar más sentido más adelante cuando lleguemos a su uso real; por
ahora sabé que es una de las primeras cosas que vas a usar cuando crees
una conexión.

```{.c}
struct addrinfo {
    int              ai_flags;     // AI_PASSIVE, AI_CANONNAME, etc.
    int              ai_family;    // AF_INET, AF_INET6, AF_UNSPEC
    int              ai_socktype;  // SOCK_STREAM, SOCK_DGRAM
    int              ai_protocol;  // usá 0 para "cualquiera"
    size_t           ai_addrlen;   // tamaño de ai_addr en bytes
    struct sockaddr *ai_addr;      // struct sockaddr_in o _in6
    char            *ai_canonname; // hostname canónico completo

    struct addrinfo *ai_next;      // lista enlazada, próximo nodo
};
```

Vas a configurar un poco esta estructura, y después llamar a 
[ixtt[getaddrinfo()]] `getaddrinfo()`. La función te va a devolver un
puntero a una nueva lista enlazada de esas estructuras, llenas con todos
los datos que necesites.

Podés forzar el uso de IPv4 o IPv6 con el campo `ai_family`, o dejarlo
como `AF_UNSPEC` para usar cualquiera de ambos. Esto está bueno porque
te permite escribir código agnóstico de la versión de IP.

Notá que esta es una lista enlazada: `ai_next` apunta al próximo
elemento - podría haber varios resultados de los que tengas que elegir.
Yo usaría el primero que haya funcionado, pero puede que tengas tu
dominio necesite algo distinto; ¡no puedo saber todo, che!

Notarás que el campo `ai_addr` en el `struct addrinfo` es un puntero a
un [ixtt[struct sockaddr]] `struct sockaddr`. Acá es donde empezamos a
meternos en los detallitos meticulosos de qué hay dentro de una
estructura de dirección IP.

Usualmente no vas a necesitar escribir vos estas estructuras; en general
te va a alcanzar con llamar a `getaddrinfo()` para que te complete toda
tu `struct addrinfo`. Lo que _sí_ vas a hacer es revisar estas `struct`s
para sacarles los valores, y por eso te las presento acá.

(Además, en los programas escritos antes de que inventaran el `struct
addrinfo` escribíamos estos valores a mano, así que vas a ver montones
de código IPv4 ahí afuera que lo hagan así. Ya sabés, versiones viejas
de esta guía y demás.)

Algunas `struct`s son para IPv4, otras son para IPv6, y algunas son para
ambas. Iré mencionando cuáles con qué.

Como sea, el `struct sockaddr` guarda información de direcciones para
varios tipos de sockets.

```{.c}
struct sockaddr {
    unsigned short    sa_family;    // familia de direcciones, AF_xxx
    char              sa_data[14];  // 14 bytes de dirección de protocolo
}; 
```

`sa_family` soporta varios valores distintos, pero siempre va a ser
[ixtt[AF\_INET]] `AF_INET` (IPv4) o [ixtt[AF\_INET6]] `AF_INET6` (IPv6)
para todo lo que hagamos en este documento. `sa_data` contiene una
dirección de destino y un número de puerto para el socket. Esto es
bastante un plomo, porque no querés andar metiendo la dirección y puerto
en el `sa_data` a mano.

Para lidiar mejor con `struct sockaddr`, l@s programadores crearon una
estructura paralela para usar con IPv4: [ixtt[struct sockaddr]] `struct
sockaddr_in` ("in" por "Internet").

Y _esta es la parte importante_: un puntero a una `struct sockaddr_in`
puede castearse a un puntero a `struct sockaddr`, y vice-versa.
Entonces, por más que `connect()` espera un `struct sockaddr*`, igual
podés usar un `struct sockaddr_in` ¡y castearlo a último momento!

```{.c}
// (Sólo IPv4 - mirá struct sockaddr_in6 para IPv6)

struct sockaddr_in {
    short int          sin_family;  // Familia de direcciones, AF_INET
    unsigned short int sin_port;    // Número de puerto
    struct in_addr     sin_addr;    // Dirección de Internet
    unsigned char      sin_zero[8]; // Mismo tamaño que struct sockaddr
};
```

Esta estructura te facilita referenciar elementos de la dirección de
socket. Notá que `sin_zero` (al que se incluye para que la estructura
mida lo mismo que un `struct sockaddr` debería ponerse todo en cero con
la función `memset()`. Además, fijate que `sin_family` corresponde al
`sin_family` de la `struct sockaddr`, y tiene que valer "`AF_INET`".
Finalmente, el `sin_port` tiene que estar en [ix[byte ordering]] _Orden
de bytes de Red_ (¡usando [ixtt[htons()]] `htons()`!).

¡Sigamos ahondando! Verás que el campo `sin_addr` es un `struct
in_addr`. ¿Qué es esa cosa? Bueno, no quiero hacer mucho drama, pero es
una de las uniones (_unions_) más aterradoras de la historia:

```{.c}
// (Sólo IPv4 - mirá struct in6_addr para IPv6)

// Dirección de Internet (una estructura por motivos históricos)
struct in_addr {
    uint32_t s_addr; // eso es un int de 32 bits (4 bytes)
};
```

¡Wow! Bueno, _solía ser_ una unión, pero ahora esos días quedaron en el
pasado. ¡Buen viaje! Así que si declaraste una `ina` de tipo `struct
sockaddr_in`, entonces `ina.sin_addr.s_addr` referencia a la dirección
IP de 4 bytes (en orden de red). Notá que, incluso si tu sistema todavía
usa la horrenda unión para `struct in_addr`, igualmente podés
referenciar la dirección IP de 4 bytes de exactamente la misma forma que
lo hice ahí arriba (por varios `#define`s que hay).

¿Y qué con [ix[IPv6]] IPv6? Hay `struct`s similares:

```{.c}
// (Sólo IPv6 - mirá struct sockaddr_in y struct in_addr para IPv4)

struct sockaddr_in6 {
    u_int16_t       sin6_family;   // Familia de direcciones, AF_INET6
    u_int16_t       sin6_port;     // Número de puerto, en Orden de Red
    u_int32_t       sin6_flowinfo; // Información de flujo (flow) de IPv6
    struct in6_addr sin6_addr;     // Dirección IPv6
    u_int32_t       sin6_scope_id; // ID de ámbito (scope)
};

struct in6_addr {
    unsigned char   s6_addr[16];   // Dirección IPv6
};
```

Notá que IPv6 tiene una dirección IPv6 y un número de puerto, tal como
IPv4 tiene una dirección IPv4 y un número de puerto.

También sabé que no voy a hablar mucho de los campos de Información de
Flujo o ID de ámbito de IPv6 por el momento... Esto es sólo una guía
para principiantes. `:-)`

Última, pero no menos importante, esta esta otra estructura simple,
`struct sockaddr_storage`, diseñada lo suficientemente grande para
almacenar tanto estructuras IPv4 como IPv6. Para algunas llamadas, a
veces no sabés de antemano si te va a completar tu `struct sockaddr` con
una dirección IPv4 o una IPv6. Entonces le pasás esta estructura
paralela, similar a `struct sockaddr` pero más grande, y después la
casteás al tipo que necesites:

```{.c}
struct sockaddr_storage {
    sa_family_t  ss_family;     // familia de la dirección

    // todo esto es relleno, específico de la implementación, ignoralo
    char      __ss_pad1[_SS_PAD1SIZE];
    int64_t   __ss_align;
    char      __ss_pad2[_SS_PAD2SIZE];
};
```

Lo importante es que puedas ver la familia de la dirección en el campo
`ss_family` - con esto te fijás si es `AF_INET` o `AF_INET6` (para IPv4
o IPv6). Ahí ya podés castearla a `struct sockaddr_in` o `struct
sockaddr_in6` si quisieras.


## Direcciones IP, Parte Dos

Por suerte para vos, hay un puñado de funciones que te ayudan a
manipular [ix[IP]] direcciones IP. No hace interpretarlas manualmente y
meter todo en un `long` con el operador `<<`.

Primero, digamos que tenés una `struct sockaddr_in ina`, y tenés una
dirección IP "`10.12.110.57`" o "`2001:db8:63b3:1::3490`" que querés
almacenar en ella. La función que querés usar, [ixtt[inet\_pton()]]
`inet_pton()`, convierte una dirección IP en notación números-y-puntos a
una `struct in_addr` o `struct in6_addr` dependiendo de si le
especificás `AF_INET` o `AF_INET6` ("`pton`" significa "presentación a
red" - podés llamarla "imprimible (_printable_) a red") si te es más
fácil de recordar). La conversión se puede hacer así:

```{.c}
struct sockaddr_in sa; // IPv4
struct sockaddr_in6 sa6; // IPv6

inet_pton(AF_INET, "10.12.110.57", &(sa.sin_addr)); // IPv4
inet_pton(AF_INET6, "2001:db8:63b3:1::3490", &(sa6.sin6_addr)); // IPv6
```

(Comentario rápido: la vieja forma de hacer esto era con una función
llamada [ixtt[inet\_addr()]] `inet_addr()` u otra llamada
[ixtt[inet\_aton()]] `inet_aton()`; ambas quedaron obsoletas y no
funcionan con IPv6)

Ahora, el fragmento de código acá arriba no es muy robusto, porque no
tiene manejo de errores. Fijate, `inet_pton()` devuelve `-1` si hubo un
error, o 0 si la dirección está en mal estado. ¡Así que revisá que el
resultado sea mayor a 0 antes de usarlo!

Bien, ya podés convertir direcciones IP _textuales_ a su representación
binaria. ¿Y al revés? ¿Qué onda si tenés un `struct in_addr` y querés
imprimirla en su notación números-y-puntos? (o una `struct in6_addr` que
quieras en notación, ehm, "hexa-y-dos-puntos") En este caso querrás usar
la función [ixtt[inet\_ntop()]] `inet_ntop()` ("ntop" significa "red
(network) a presentación" - podés llamarla "red a imprimible
(printeable)" si te es más sencillo), así:

```{.c .numberLines}
// IPv4:

char ip4[INET_ADDRSTRLEN];  // espacio para guardar el string IPv4
struct sockaddr_in sa;      // supongamos que esto tiene valores cargados

inet_ntop(AF_INET, &(sa.sin_addr), ip4, INET_ADDRSTRLEN);

printf("La direccion IPv4 es: %s\n", ip4);


// IPv6:

char ip6[INET6_ADDRSTRLEN]; // espacio para guardar el string IPv6
struct sockaddr_in6 sa6;    // supongamos que esto tiene valores cargados

inet_ntop(AF_INET6, &(sa6.sin6_addr), ip6, INET6_ADDRSTRLEN);

printf("La direccion IPv6 es: %s\n", ip6);
```

Cuando la llames, le vas a pasar el tipo de dirección (IPv4 o IPv6), la
dirección, un puntero a un string en que guardar el resultado, y la
longitud máxima de ese string (hay dos macros que convenientemente
representan el tamaño del string más grande que puedas necesitar para
almacenar una dirección IPv4 o una IPv6: `INET_ADDRSTRLEN` y
`INET6_ADDRSTRLEN`.)

(Otro comentario rápido para volver a mencionar cómo se hacía antes: la
función histórica para esta conversión se llamaba [ixtt[inet\_ntoa()]]
`inet_ntoa()`. También quedó obsoleta y no funciona con IPv6.)

Por último, estas funciones sólo trabajan con direcciones IP numéricas -
no hacen ningúna búsqueda de DNS de un nombre de host como
"`www.example.com`". Más adelante vamos a usar `getaddrinfo()` para eso.


### Redes privadas (o desconectadas)

[ix[private networks]] Muchos lugares tienen un [ix[firewall]] firewall
que oculta a su red del resto del mundo para protegerla. Y, en general,
el firewall traduce las direcciones IP "internas" a direcciones IP
"externas" (que el resto del mundo conoce) usando un proceso llamado
_Traducción de Direcciones de Red_ ("Network Address Translation"), o
[ix[NAT]] NAT (por sus siglas en inglés).

¿Van subiendo los nervios? "¿A dónde va con todo esto?"

Buen, relajate y preparáte una bebida no-alcohólica (o alcohólica,
buen), porque, como principiante, ni siquiera necesitás preocuparte por
el NAT, dado que te lo resuelven de manera transparente. Pero quería
hablarte de las redes detrás de un firewall para cuando te empieces a
confundir por los números de red que estés viendo.

Por ejemplo, yo tengo un firewall en mi casa. Tengo dos direcciones IPv4
estáticas asignadas por mi compañía de DSL, y aún así tengo siete
computadoras en la red. ¿Cómo es posible esto? Dos computadoras no
pueden compartir la misma dirección IP, ¡porque si no los datos no
sabrían a cuál de las dos ir!

La respuesta es: no comparten la misma dirección IP. Están en una red
privada que tiene disponibles 24 millones de direcciones IP para ella.
Son todas para mí. Bueno, al menos _así parece_ para cualquiera que
mire. Lo que sucede es esto:

Si inicio sesión en una computadora remota, me dice que estoy iniciando
sesión desde 192.0.2.33, que es la dirección IP pública que mi proveedor
de Internet (ISP, por sus siglas en inglés) me asignó. Pero si le
pregunto a mi computadora local cuál es su dirección IP, me dice
10.0.0.5. ¿Quién está traduciendo de una dirección IP a la otra? Así es:
¡el firewall! ¡Está NATeando!

`10.x.x.x` es una de las pocas redes reservadas que sólo pueden usarse
en redes totalmente desconectadas de Internet, o en redes detrás de un
firewall. Los detalles de qué números de red privadas tenés disponibles
para usar están en la [flrfc[RFC 1918|1918]], pero algunas comunes que
vas a ver son [ix[10.x.x.x]] `10.x.x.x` y [ix[192.168.x.x]]
`192.168.x.x`, donde `x` suele estar en el intervalo 0-255. Un poco
menos común es `172.y.x.x`, donde `y` varía entre 16 y 31.

Las redes detrás de un firewall que NATea no _necesitan_ estar en una de
esas redes reservadas, pero suelen estarlo.

(Como dato de color, mi dirección IP externa no es realmente
`192.0.2.33`. La red `192.0.2.x` es una red reservada para simular
direcciones IP "reales" al escribir documentación, ¡como esta guía!)

[ix[IPv6]] IPv6 también tiene redes privadas, por así decirlo. Empiezan
con `fdXX:` (o quizá en el futuro `fcXX:`) según la [flrfc[RFC
4193|4193]]. NAT e IPv6 no suelen mezclarse, igual (a menos que estés
haciendo eso de interconectar IPv6 con IPv4, que está fuera del alcance
de esta guía) - en teoría tenés tantas direcciones disponibles que ya no
deberías necesitar NAT. Pero si querés asignarte direcciones en una red
que no routee hacia el exterior, este es el modo de hacerlo.


# Saltando de IPv4 a IPv6

[ix[IPv6]] ¡Pero yo sólo quiero saber qué cambiar en mi código para
hacerlo andar con IPv6! ¡Decime!

¡Ok! ¡Ok!

Casi todo en esta sección es algo que ya cubrí más arriba, pero es la
versión corta para quienes sean impacientes (obviamente, hay mucho más
que esto, pero esto es lo que aplica a esta guía).

1. Primero que nada, tratá de usar [ixtt[getaddrinfo()]]
   [`getaddrinfo()`](#structs) para conseguir toda la información de las
   `struct sockaddr` en lugar de completar las estructuras a mano. Esto
   te va a mantener agnóstico de la versión de IP, y eliminar varios de
   los problemas de los pasos siguientes.

2. En cualquier lado en que estés escribiendo algo relacionado a alguna
   versión de IP, tratá de wrappearla dentro de una función helper.

3. Cambiá `AF_INET` a `AF_INET6`.

4. Cambiá `PF_INET` a `PF_INET6`.

5. Cambiá las asignaciones de `INADDR_ANY` a asignaciones de
   `in6addr_any`, que son un poco diferentes:

   ```{.c}
   struct sockaddr_in sa;
   struct sockaddr_in6 sa6;
   
   sa.sin_addr.s_addr = INADDR_ANY;  // usa mi dirección IPv4
   sa6.sin6_addr = in6addr_any; // usa mi dirección IPv6
   ```

   También podés usar el valor `IN6ADDR_ANY_INIT` como inicializador
   cuando declarás la `struct in6_addr`, así:

   ```{.c}
   struct in6_addr ia6 = IN6ADDR_ANY_INIT;
   ```

6. En vez de `struct sockaddr_in`, usá `struct sockaddr_in6`,
   asegurándote de agregarle "6" a los campos que corresponda (mirá
   [`struct`s](#structs) más arriba). No hay campo `sin6_zero`.

7. En vez de `struct in_addr`, usá `struct in6_addr`, asegurándote de
   agregarle "6" a los campos que corresponda (mirá
   [`struct`s](#structs) más arriba).

8. En vez de `inet_aton()` o `inet_addr()`, usá `inet_pton()`.

9. En vez de `inet_ntoa()`, usá `inet_ntop()`.

10. En vez de `gethostbyname()`, usá la superior `getaddrinfo()`.

11. En vez de `gethostbyaddr()`, usá la superior
    [ixtt[getnameinfo()]] `getnameinfo()` (aunque `gethostbyaddr()`
    igual funciona con IPv6).

12. `INADDR_BROADCAST` no funciona más. Usá el multicast de IPv6 en su
    lugar.

_¡Et voila!_


# Llamadas a sistema ("System calls") o reventar

Esta es la sección en que nos metemos en las llamadas a sistema (y
otras llamadas a bibliotecas) que te permiten acceder a la funcionalidad
de red de una máquina Unix, o cualquier máquina que soporte la API de
sockets, al caso (BSD, Windows, Linux, Mac, el que se te ocurra). Cuando
llamás a una de estas funciones, el kernel toma el control y hace todo
el trabajo por vos automágicamente.

La parte más complicada para la mayoría de la gente es en qué orden
llamar a estas cosas. Las páginas del `man` no ayudan mucho con eso,
como puede que ya hayas descubierto. Bueno, para ayudar con ese problema
espantoso, traté de ordenar las llamadas a sistema de las secciones
siguientes en (aproximadamente) _exactamente_ el mismo orden en que vas
a necesitar llamarlas en tus programas.

Eso, junto con algunos ejemplos de código por aquí y por allá, un café
(que, me temo, te vas a tener que conseguir vos), algo de instinto y
coraje, y ¡vas a estar mandando datos a través de la Internet como el
hijo de Jon Postel!

_(Por favor notá que, en pos de la brevedad, la mayoría de los
fragmentos de código debajo no incluyen el necesario chequeo de errores.
Y en general asumen que las llamadas a `getaddrinfo()` dan resultados
exitosos y devuelven una entrada válida en la lista enlazada. Ambas
situaciones están manejadas como corresponde en los programas completos,
igual, así que usá esos como modelo.)_


## `getaddrinfo()` - ¡Preparándose para arrancar!

[ixtt[getaddrinfo()]] Esta función es un verdadero caballito de batalla
con un montón de opciones, pero usarla es bastante sencillo, en
realidad. Te ayuda a configurar los `struct`s que vas a necesitar luego.

Un poco de historia: antes se solía usar una función llamada
`gethostbyname()` para hacer búsquedas de DNS. Luego cargabas esa
información a mano en un `struct sockaddr_in`, y usabas eso para tus
llamadas.

Por suerte, eso ya no es necesario (ni recomendable, si querés que tu
código funcione tanto con IPv4 como con IPv6). En estos tiempos modernos
tenemos la función `getaddrinfo()` que te resuelve un montón de cosas
geniales, incluyendo búsquedas de DNS y nombres de servicio, ¡y encima
llena todos los `struct`s que necesitás!

¡Hechémosle un vistazo!

```{.c}
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>

int getaddrinfo(const char *node,     // e.g. "www.example.com" o IP
                const char *service,  // e.g. "http" o número de puerto
                const struct addrinfo *hints,
                struct addrinfo **res);
```

Le das tres parámetros de entrada a la función, y te devuelve un puntero
a una lista enlazada, `res`, de resultados.

El parámetro `node` ("nodo") es el nombre de host al que conectarse, o
una dirección IP.

El parámetro siguiente, `service` ("servicio"), puede ser un número de
puerto, como "80", o el nombre de un servicio en particular (presente en
la [fl[Lista de puertos de
IANA|https://www.iana.org/assignments/port-numbers]] o el archivo
`/etc/services` de tu Unix) como "http" o "ftp" o "telnet" o "smtp" o lo
que sea.

Finalmente, el parámetro "hints" ("indicios") apunta a un `struct
addrinfo` que hayas llenado previamente con la información relevante.

Acá hay una llamada de ejemplo si sos un servidor que quiere escuchar en
la dirección IP de tu host, en el puerto 3490. Notá que esto no se pone
propiamente a escuchar conexiones o configurar la red; simplemente
prepara las estructuras que vamos a usar luego:

```{.c .numberLines}
int status;
struct addrinfo hints;
struct addrinfo *servinfo;  // va a apuntar a los resultados

memset(&hints, 0, sizeof hints); // aseguramos que la estructura esté vacía
hints.ai_family = AF_UNSPEC;     // no importa si IPv4 o IPv6
hints.ai_socktype = SOCK_STREAM; // sockets stream TCP
hints.ai_flags = AI_PASSIVE;     // completá mi IP por mí

if ((status = getaddrinfo(NULL, "3490", &hints, &servinfo)) != 0) {
    fprintf(stderr, "getaddrinfo error: %s\n", gai_strerror(status));
    exit(1);
}

// servinfo ahora apunta a una lista enlazada de 1 o más struct addrinfo's

// ... hace todo hasta dejar de necesitar el servinfo ....

freeaddrinfo(servinfo); // liberá la lista enlazada
```

Notá que puse el `ai_family` en `AF_UNSPEC`, declarando que no me
importa si voy a usar IPv4 o IPv6. Podés ponerlo en `AF_INET` o
`AF_INET6` si querés uno u otro específicamente.

Además, verás el flag `AI_PASSIVE` ahí; esto le dice a `getaddrinfo()`
que asigne la dirección de mi máquina local a las estructuras de socket.
Esto está bueno porque así no tengo que hardcodearla (o bien podrías
poner una dirección específica en el primer parámetro de `getaddrinfo()`
donde yo puse un `NULL` ahí arriba).

Luego hacemos el llamado. Si hay un error (`getaddrinfo()` devuelve
distinto de cero), podemos imprimirlo con la función `gai_strerror()`,
como podrás ver. Si todo funciona normalmente, en cambio, `servinfo` va
a apuntar a una lista enlazada de `struct addrinfo`s, cada uno de los
cuales contiene una `struct sockaddr` de algún tipo que vamos a poder
usar luego. ¡Cheto!

Finalmente, cuando eventualmente hayamos terminado de usar la lista
enlazada que `getaddrinfo()` tan gentilmente nos reservó, podemos (y
debemos) liberarla con una llamada a `freeaddrinfo()`.

Acá hay una llamada de ejemplo si sos un cliente que se quiere conectar
a un servidor particular, digamos "www.example.net" en el puerto 3490.
De nuevo, esto no hace la conexión propiamente dicha, si no que prepara
las estructuras que vamos a necesitar luego:

```{.c .numberLines}
int status;
struct addrinfo hints;
struct addrinfo *servinfo;  // va a apuntar a los resultados

memset(&hints, 0, sizeof hints); // aseguramos que la estructura esté vacía
hints.ai_family = AF_UNSPEC;     // no importa si IPv4 o IPv6
hints.ai_socktype = SOCK_STREAM; // sockets stream TCP

// nos preparamos para conectarnos
status = getaddrinfo("www.example.net", "3490", &hints, &servinfo);

// servinfo ahora apunta a una lista enlazada de 1 o más struct addrinfo's

// etc.
```

Sigo insistiendo con que `servinfo` es una lista enlazada con distintos
tipos de información de direcciones. Escribamos rápidamente un programa
de ejemplo para mostrar esta información. [flx[Este pequeño
programa|showip.c]] va a imprimir las direcciones IP del host que sea
que especifiques en la línea de comandos:

```{.c .numberLines}
/*
** showip.c -- muestra las direcciones IP de un host pasado por línea de comandos
*/

#include <stdio.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>
#include <arpa/inet.h>
#include <netinet/in.h>

int main(int argc, char *argv[])
{
    struct addrinfo hints, *res, *p;
    int status;
    char ipstr[INET6_ADDRSTRLEN];

    if (argc != 2) {
        fprintf(stderr,"usage: showip hostname\n");
        return 1;
    }

    memset(&hints, 0, sizeof hints);
    hints.ai_family = AF_UNSPEC; // AF_INET o AF_INET6 para forzar una versión
    hints.ai_socktype = SOCK_STREAM;

    if ((status = getaddrinfo(argv[1], NULL, &hints, &res)) != 0) {
        fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(status));
        return 2;
    }

    printf("Direcciones IP de %s:\n\n", argv[1]);

    for(p = res;p != NULL; p = p->ai_next) {
        void *addr;
        char *ipver;

        // consigo el puntero a la dirección propiamente dicha,
        // campos distintos en IPv4 e IPv6:
        if (p->ai_family == AF_INET) { // IPv4
            struct sockaddr_in *ipv4 = (struct sockaddr_in *)p->ai_addr;
            addr = &(ipv4->sin_addr);
            ipver = "IPv4";
        } else { // IPv6
            struct sockaddr_in6 *ipv6 = (struct sockaddr_in6 *)p->ai_addr;
            addr = &(ipv6->sin6_addr);
            ipver = "IPv6";
        }

        // convierto la IP a string y lo imprimo:
        inet_ntop(p->ai_family, addr, ipstr, sizeof ipstr);
        printf("  %s: %s\n", ipver, ipstr);
    }

    freeaddrinfo(res); // libero la lista enlazada

    return 0;
}
```

Como se puede ver, el código llama a `getaddrinfo()` con lo que sea que
le pases en la línea de comandos, lo que llena la lista enlazada
apuntada por `res`, y luego iteramos la lista imprimiendo su contenido o
haciendo lo que queramos con eso.

(Quedó un poco feo el código para revisar los distintos tipos de `struct
sockaddr`s dependiendo de la versión de IP. ¡Perdón por eso! No se me
ocurre alternativa mejor.)

¡Ejecución de muestra! ¿Quién no ama las capturas de pantalla?

```
$ showip www.example.net
Direcciones IP de www.example.net:

  IPv4: 192.0.2.88

$ showip ipv6.example.com
Direcciones IP de ipv6.example.com:

  IPv4: 192.0.2.101
  IPv6: 2001:db8:8c00:22::171
```

Ahora que tenemos eso bajo control, vamos a usar los resultados que nos
da `getaddrinfo()` para pasarlos a otras funciones de sockets y, por
fin, ¡conseguir establecer nuestra conexión de red! ¡Seguí leyendo!


## `socket()`- ¡Conseguí el descriptor de archivo! {#socket}

Creo que ya no puedo demorarlo más - tengo que hablar de la llamada a
sistema [ixtt[socket()]] `socket()`. Este es el detalle:

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol); 
```

Pero, ¿qué son estos parámetros? Te permiten decir qué tipo de socket
querés (IPv4 o IPv6, stream o datagram, y TCP o UDP).

La gente solía hardcodear estos valores, y de hecho todavía podés
hacerlo (`domain` es `PF_INET` o `PF_INET6`, `type` es `SOCK_STREAM` o
`SOCK_DGRAM`, y `protocol` puede ir en `0` para elegir el protocolo
correcto para el `type` determinado. O podés llamar a `getprotobyname()`
para buscar el protocolo que querés, "tcp" o "udp").

(Esta cosa `PF_INET` es pariente cercana del [ixtt[AF\_INET]] `AF_INET`
que podés usar al inicializar el campo `sin_family` de un `struct
sockaddr_in`. De hecho, están tan emparentados que incluso tienen el
mismo valor, y much@s programadores llaman a `socket()` pasándole
`AF_INET` como primer parámetro en lugar de `PF_INET`. Pero, acomodate y
escuchame, porque es tiempo de una historia. Una vez, hace mucho, mucho
tiempo, estaba la idea de que una familia de direcciones (lo que "AF"
significa en "AF_INET": "Address Family") podría soportar varios
protocolos que fueran referenciados por su familia de protocolos (lo que
"PF" significa en "PF_INET": "Protocol Family"). Eso nunca sucedió. Y
vivieron felices por siempre, FIN. Así que lo más correcto es usar
`AF_INET` en tu `struct sockaddr_in` y `PF_INET` en tu llamada a
`socket()`.)

Como sea, suficiente. Lo que realmente querés hacer es usar los valores
de los resultados de la llamada a `getaddrinfo()` y dárselos directo a
`socket()`, así:

```{.c .numberLines}
int s;
struct addrinfo hints, *res;

// hago la búsqueda
// [hacé de cuenta que ya llenamos la estructura "hints"]
getaddrinfo("www.example.com", "http", &hints, &res);

// de nuevo, deberías chequear errores de getaddrinfo(), y recorrer la
// lista enlazada "res" buscando entradas válidas en lugar de
// simplemente asumir que la primera es buena (como hacen muchos de
// estos ejemplos). Mirá la sección de cliente/servidor para ejemplos
// reales.

s = socket(res->ai_family, res->ai_socktype, res->ai_protocol);
```

`socket()` simplemente te devuelve un _descriptor de socket_ que podés
usar en llamadas a sistema futuras, o un `-1` si hubo errores. La
variable global `errno` toma el valor del error (mirá la página del
manual de [`errno`](#errnoman) para más detalles, y un comentario sobre
el uso de `errno` en programas multi-hilo).

Bien, bien, bien, pero... ¿Para qué sirve este socket? La respuesta es
que no sirve para nada por sí mismo, y vas a tener que seguir leyendo y
haciendo más llamadas a sistema para que tenga sentido.


## `bind()` - ¿En qué puerto estoy? {#bind}

[ixtt[bind()]] Una vez que tenés un socket, puede que tengas que asociar
ese socket a un [ix[port]] puerto en tu máquina local (esto lo hacés
generalmente cuando vas a hacer [ixtt[listen()]] `listen()` para
_escuchar_ conexiones entrantes en un puerto específico - esto es lo que
hacen los juegos en red cuando te dicen que te "conectes a 192.168.5.10
en el puerto 3490"). El kernel usa el número de puerto para encontrar a
qué descriptor de sockets de qué proceso enviar un paquete entrante. Si
únicamente vas a hacer [ixtt[connect()]] `connect()` (porque sos el
cliente, y no el servidor), probablemente no necesites hacer esto. Pero
leelo igual, por las dudas.

Acá está la sinopsis de la llamada a sistema `bind()`:

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int bind(int sockfd, struct sockaddr *my_addr, int addrlen);
```

`sockfd` es el descriptor de archivo socket que te devolvió `socket()`.
`my_addr` es un puntero a un `struct sockaddr` que contiene información
sobre tu dirección, principalmente puerto y [ix[IP]] dirección IP.
`addrlen` es la longitud en bytes de esa dirección.

Uff, eso fue un montón para absorber de una. Veamos un ejemplo que
asocia (_bindea_) el socket al host en que está corriendo el programa,
en el puerto 3490:

```{.c .numberLines}
struct addrinfo hints, *res;
int sockfd;

// primero, cargamos las estructuras con getaddrinfo():

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;  // no importa si IPv4 o IPv6
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_PASSIVE;     // completá mi IP por mí

getaddrinfo(NULL, "3490", &hints, &res);

// creo un socket:

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

// lo asocio al puerto que le pasamos a getaddrinfo():

bind(sockfd, res->ai_addr, res->ai_addrlen);
```

Al usar el flag `AI_PASSIVE` le estoy diciendo al programa que bindee a
la IP del host en que está corriendo. Si querés bindearte a una
dirección IP local específica, descartá el `AI_PASSIVE` y poné esa
dirección IP en el primer parámetro de `getaddrinfo()`.

`bind()` también devuelve `-1` cuando falla y completa `errno` con el
valor del error.

Hay mucho código viejo que completa el `struct sockaddr_in` a mano antes
de llamar a `bind()`. Obviamente, esto es específico de IPv4, pero nada
evita que hagas lo mismo para IPv6, excepto que, en general, te va a
resultar más sencillo usar `getaddrinfo()`. Como sea, la vieja forma de
hacerlo tenía esta pinta:

```{.c .numberLines}
// ¡¡¡ ESTA ES LA FORMA VIEJA !!!

int sockfd;
struct sockaddr_in my_addr;

sockfd = socket(PF_INET, SOCK_STREAM, 0);

my_addr.sin_family = AF_INET;
my_addr.sin_port = htons(MYPORT);     // short, en orden de red
my_addr.sin_addr.s_addr = inet_addr("10.12.110.57");
memset(my_addr.sin_zero, '\0', sizeof my_addr.sin_zero);

bind(sockfd, (struct sockaddr *)&my_addr, sizeof my_addr);
```

En ese código también podrías asignar `INADDR_ANY` al campo `s_addr` si
quisieras bindear a tu dirección IP local (como el flag `AI_PASSIVE` que
vimos antes). La versión IPv6 de `INADDR_ANY` es una variable global
`in6addr_any` que tenés que asignar al campo `sin6_addr` de tu `struct
sockaddr_in6` (también hay una macro `IN6ADDR_ANY_INIT` que podés usar
al inicializar variables).

Otra cosa a la que prestar atención cuando llamás a `bind()`: no uses
puertos bajos. [ix[port]] Los puertos debajo del 1024 son RESERVADOS (¡a
menos que seas root!). Podés usar cualquier puerto arriba de ese, hasta
el 65535 (siempre y cuando no esté siendo usado por otro programa).

A veces, quizá lo notes, intentás volver a ejecutar un servidor y
`bind()` falla, diciendo que [ix[Address already in use]] "Address
already in use" (_"Dirección en uso"_). ¿Qué significa esto? Bueno,
alguna partecita de un socket que estuvo conectado todavía queda dando
vueltas por el kernel, manteniendo el puerto ocupado. Bien podés esperar
a que lo libere (un minuto, más o menos), o agregar código a tu programa
para que reuse el puerto, así:

[ixtt[setsockopt()]] [ixtt[SO\_REUSEADDR]]

```{.c .numberLines}
int yes=1;
//char yes='1'; // La gente de Solaris use esto

// evitar el mensaje de error molesto de "Address already in use"
if (setsockopt(listener,SOL_SOCKET,SO_REUSEADDR,&yes,sizeof yes) == -1) {
    perror("setsockopt");
    exit(1);
} 
```

[ixtt[bind()]] Un último comentario sobre `bind()`: hay veces en que no
necesitás llamarla. Si vas a hacer [ixtt[connect()]] `connect()` a una
máquina remota y no te importa desde qué puerto local (como el caso de
`telnet`, en que sólo te importa el puerto remoto), podés llamar a
`connect()` directamente, que mirará que el socket no está bindeado y lo
`bind()`eará a un puerto local libre si fuera necesario.


## `connect()` - ¡Hey, vos! {#connect}

[ixtt[connect()]] Hagamos de cuenta por unos minutos que sos una
aplicación telnet. Tu usuari@ te ordena (como en la película [ix[TRON]]
_TRON_) que consigas un descriptor de archivo socket. Vos cumplís y
llamás a `socket()`. Luego, tu usuari@ te dice que te conectes a
"`10.12.110.57`" en el puerto "`23`" (el puerto estándar de telnet).
¡Oh! ¿Y ahora qué hacés?

Por suerte para vos, programa, ahora estás leyendo la sección sobre
`connect()` - cómo conectarse a un host remoto. ¡Así que seguí leyendo!
¡No hay tiempo que perder!

La llamada `connect()` tiene esta pinta:

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int connect(int sockfd, struct sockaddr *serv_addr, int addrlen); 
```

`sockfd` es nuestro ya amigo descriptor de archivo socket, devuelto por
la llamada `socket()`; `serv_addr` es un `struct sockaddr` que contiene
la dirección IP y puerto destino, y `addrlen` es la longitud en bytes de
esa estructura con la dirección del servidor.

A toda esta información la podemos sacar de los resultados de llamar a
`getaddrinfo()`, así que buenísimo.

¿Está empezando a tener sentido todo esto? No te puedo escuchar desde
acá, así que sólo me queda confiar en que sí. Veamos un ejemplo en el
que hacemos una conexión de sockets a "`www.example.com`" en el puerto
`3490`:

```{.c .numberLines}
struct addrinfo hints, *res;
int sockfd;

// primero, cargamos las estructuras con getaddrinfo():

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;
hints.ai_socktype = SOCK_STREAM;

getaddrinfo("www.example.com", "3490", &hints, &res);

// creo un socket:

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

// ¡a conectarse!

connect(sockfd, res->ai_addr, res->ai_addrlen);
```

Una vez más, los programas viejos completaban sus `struct sockaddr_in`s
a mano para pasarselas a `connect()`. Podés hacerlo si querés. Fijate el
comentario similar en [la sección de `bind()`](#bind) más arriba.

Asegurate de verificar el valor de retorno de `connect()` - va a
devolver `-1` cuando falle, y asignar la variable `errno`.

<!-- índice de latex index acá para poder conseguir la entrada del -->
<!-- subíndice luego del tt -->
[ix[bind()@\texttt{bind()}!implicit]]

Además, fijate que no llamamos a `bind()`. Básicamente, no nos importa
nuestro número de puerto local; sólo nos importa a dónde estamos yendo
(el puerto remoto). El kernel nos va a elegir un puerto local, y el
sitio al que nos conectemos va a conseguir esta información
automáticamente de nuestra parte. Sin problemas.


## `listen()` - ¿Alguien quiere llamarme, porfa? {#listen}

[ixtt[listen()]] OK, hora de cambiar el ritmo. ¿Qué pasa si no querés
conectarte a un host remoto? Digamos, sólo por diversión, que querés
esperar conexiones entrantes y manejarlas de algún modo. Ese proceso
tiene dos pasos: primero hacés `listen()` (_escuchás_), y después
[ixtt[accept()]] `accept()` (_aceptás_; mirá más abajo).

La llamada `listen()` es bastante sencilla, pero requiere algo de
explicación:

```{.c}
int listen(int sockfd, int backlog); 
```

`sockfd` es el descriptor de archivo socket de siempre, que nos devuelve
la llamada a sistema `socket()`.
[ix[listen()@\texttt{listen()}!backlog]] `backlog` es el número de
conexiones que permitimos tener en la cola de entrada. ¿Qué significa
eso? Bueno, las conexiones entrantes van a esperar en esta cola hasta
que les hagas `accept()` (mirá más abajo), y este es el límite de
cuántas pueden encolarse. La mayoría de los sistemas limitan este número
alrededor de 20; probablemente puedas andar bien poniéndolo en `5` o
`10`.

Una vez más, como de costumbre, `listen()` devuelve `-1` y asigna
`errno` cuando hay errores.

Bueno, como te podrás imaginar, necesitamos llamar a `bind()` antes de
llamar a `listen()` para que el servidor esté corriendo en un puerto
específico (¡tenés que poder decirle al resto a qué puerto conectarse!).
Así que si vas a estar aceptando conexiones entrantes, la secuencia de
llamadas a sistema que vas a hacer es:

```{.c .numberLines}
getaddrinfo();
socket();
bind();
listen();
/* acá va accept() */ 
```

Sólo voy a dejar esto en lugar del código de ejemplo, porque es bastante
auto-explicativa (el código en la sección de `accept()`, acá abajo, es
más completo). La parte realmente complicada de todo este baile es la
llamada a `accept()`.


## `accept()` - "Gracias por llamar al puerto 3490"

[ixtt[accept()]] ¡Preparate - la llamada `accept()` es un tanto extraña!
Lo que va a pasar es esto: alguien desde algún lugar va a tratar de
hacer `connect()` a tu máquina en un puerto en el que estés haciendo
`listen()`. Su conexión se va a encolar esperando a que le hagas
`accept()`. Vos llamás a `accept()` y le pedís que te consiga la
conexión pendiente. ¡Y te va a devolver _un nuevo descriptor de archivo
socket_ para usar específicamente para esta conexión! Así es, de repente
¡vas a tener _dos descriptores de archivo socket_ por el precio de uno!
El original va a seguir escuchando nuevas conexiones, y el nuevo está
finalmente listo para hacer `send()` y `recv()`. ¡Ya llegamos!

La llamada es así:

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen); 
```

`sockfd` es el descriptor de socket al que le hiciste `listen()`. Fácil.
`addr` suele ser el puntero a un `struct sockaddr_storage` local. Acá es
donde se va a guardar la información sobre la conexión entrante (y con
ella vas a poder determinar qué host te está llamando desde qué puerto).
`addrlen` es una variable entera local que deberías asignar con
`sizeof(struct sockaddr_storage)` antes de pasarle esa dirección a
`accept()`. `accept()` no va a escribir más que esa cantidad de bytes en
`addr`. Si escribe menos, va a cambiar el valor de `addrlen` para
reflejarlo.

Adiviná qué. `accept()` devuelve `-1` y escribe `errno` si ocurre un
error. No te la esperabas, ¿eh?

Como antes, es un montón para absorber de una, así que acá hay un
fragmento de código de ejemplo para tu deleite:

```{.c .numberLines}
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>

#define MYPORT "3490"  // el puerto al que se van a conectar l@s usuari@s
#define BACKLOG 10     // cuántas conexiones pendientes encolar

int main(void)
{
    struct sockaddr_storage their_addr;
    socklen_t addr_size;
    struct addrinfo hints, *res;
    int sockfd, new_fd;

    // ¡¡ no te olvides de chequear errores en estas llamadas !!

    // primero, cargamos las estructuras con getaddrinfo():

    memset(&hints, 0, sizeof hints);
    hints.ai_family = AF_UNSPEC;  // no importa si IPv4 o IPv6
    hints.ai_socktype = SOCK_STREAM;
    hints.ai_flags = AI_PASSIVE;     // completá mi IP por mí

    getaddrinfo(NULL, MYPORT, &hints, &res);

    // creo un socket, lo bindeo, y escucho:

    sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);
    bind(sockfd, res->ai_addr, res->ai_addrlen);
    listen(sockfd, BACKLOG);

    // ahora acepto una conexión entrante:

    addr_size = sizeof their_addr;
    new_fd = accept(sockfd, (struct sockaddr *)&their_addr, &addr_size);

    // ¡todo listo para comunicarse por el descriptor de socket new_fd!
    .
    .
    .
```

Otra vez, notá que vamos a usar el descriptor de socket `new_fd` para
todas las llamadas a `send()` y `recv()`. Si sólo querés aceptar una
única conexión entrante, podés _cerrar_ (hacerle `close()`) al `sockfd`
que estaba escuchando, para evitar más conexiones entrantes en el mismo
puerto, si quisieras.


## Enviar (`send()`) y recibir (`recv()`) - ¡Hablame, bebé! {#sendrecv}

Estas dos funciones sirven para comunicarse por sockets stream o sockets
datagram conectados. Si querés usar los sockets datagram comunes,
no conectados, vas a tener que ver la sección sobre [`sendto()` y
`recvfrom()`](#sendtorecv) más abajo.

[ixtt[send()]] La llamada `send()`:

```{.c}
int send(int sockfd, const void *msg, int len, int flags); 
```

`sockfd` es el descriptor de socket por el que querés mandar los datos
(ya sea el que te devolvió `socket()` o el que conseguiste con
`accept()`). `msg` es un puntero a los datos que querés enviar, y `len`
es la longitud en bytes de esos datos. `flags` ponelo en `0` (mirá la
página del manual de `send()` para más información sobre los flags).

Un ejemplo de código podría ser así:

```{.c .numberLines}
char *msg = "¡Beej estuvo acá!";
int len, bytes_sent;
.
.
.
len = strlen(msg);
bytes_sent = send(sockfd, msg, len, 0);
.
.
. 
```

`send()` devuelve la cantidad de bytes que efectivamente envió - _¡puede
ser menor que la cantidad que le pediste que envíe!_ Pasa que a veces le
podés llegar a pedir que mande un choclazo de datos y no simplemente no
pueda manejar todo eso. Va a enviar tantos datos como pueda, y confiar
en que vos mandes el resto después. Acordate, si el valor que te
devuelve `send()` no coincide con el valor en `len`, es tema tuyo mandar
el resto de los datos. La buena noticia es esta: si el paquete es
relativamente corto (menos de 1K, ponele) _probablemente_ pueda mandarlo
todo de una. De nuevo, si hay errores, devuelve `-1` y setea `errno` con
su número de error.

[ixtt[recv()]] La llamada `recv()` se le parece bastante:

```{.c}
int recv(int sockfd, void *buf, int len, int flags);
```

`sockfd` es el descriptor de socket del que leer, `buf` es el buffer en
que guardar la información leída, `len` es la longitud máxima del
buffer, y `flags` otra vez podés simplemente ponerlo en `0` (mirá la
página del manual de `recv()` para información de los flags).

`recv()` devuelve el número de bytes que realmente logró leer al buffer,
o `-1` cuando falla (y escribe `errno` de manera acorde).

¡Momento! `recv()` puede devolver `0`. Eso sólo significa una única
cosa: ¡el extremo remoto cerró la conexión con tu socket! Un valor de
retorno `0` es la forma de `recv()` de avisarte que sucedió esto.

Ahí está: fue fácil, ¿eh? ¡Ya podés enviar y recibir información usando
sockets stream! ¡Wiiiiii! ¡Ya sos un@ Programador@ de Redes Unix!


## `sendto()` y `recvfrom()` - Hablame, a-la-DGRAM {#sendtorecv}

[ixtt[SOCK\_DGRAM]] "Muy lindo todo," te escucho decir, "¿pero qué onda
con los sockets datagram no conectados?". Tranqui, panchi. Tenemos un
_cosito_ para eso.

Como los sockets datagram no se conectan a un host remoto, adiviná qué
cachito de información tenemos que darle antes de enviar un paquete.
¡Claro! ¡La dirección de destino! Esta es la firma:

```{.c}
int sendto(int sockfd, const void *msg, int len, unsigned int flags,
           const struct sockaddr *to, socklen_t tolen); 
```

Como verás, la llamada es básicamente la misma que la de `send()`, pero
con dos datos extra. `to` es un puntero a una `struct sockaddr` (que
probablemente sea otra `struct sockaddr_in` o `struct sockaddr_in6` o
`struct sockaddr_storage` que casteás justo antes de usarla) que
contiene la [ix[IP]] dirección IP y el [ix[port]] puerto de destino.
A `tolen`, que en el fondo no es más que un `int`, le podés asignar el
`sizeof *to` o `sizeof(struct sockaddr_storage)`.

La estructura de dirección de destino probablemente la saques de
`getaddrinfo()`, o de `recvfrom()`, explicada más abajo, o la llenes a
mano.

Igual que `send()`, `sendto()` devuelve la cantidad de bytes que
realmente logró enviar (que, otra vez, podría ser menor al número que le
pediste que envíe), o `-1` si hay un error.

Igualmente similares son `recv()` y [ixtt[recvfrom()]] `recvfrom()`. La
sinopsis de `recvfrom()` es:

```{.c}
int recvfrom(int sockfd, void *buf, int len, unsigned int flags,
             struct sockaddr *from, int *fromlen); 
```

De nuevo: es igual a `recv()`, pero con un par de campos extra. `from`
es un puntero a una [ixtt[struct sockaddr]] `struct sockaddr_storage`
local que va a llenarse con la dirección IP y el puerto de la máquina de
origen. `fromlen` es un puntero a un `int` local que deberías
inicializar a `sizeof *from` o `sizeof(struct sockaddr_storage)`. Cuando
la función retorne, `fromlen` tendrá la longitud de la dirección
realmente almacenada en `from`.

`recvfrom()` retorna el número de bytes recibidos, o `-1` si hay un
error (y escribe `errno`).

Así que, pregunta: ¿por qué usamos `struct sockaddr_storage` como tipo
de socket? ¿Por qué no un `struct sockaddr_in`? Porque, verás, queremos
evitar atarnos a IPv4 o IPv6. Así que usamos la estructura genérica
`struct sockaddr_storage`, que sabemos que es lo suficientemente grande
para cualquiera de las dos.

(Así que... Otra pregunta: ¿por qué no es la propia `struct sockaddr` lo
suficientemente grande para cualquier dirección? ¡Incluso casteamos la 
genérica `struct sockaddr_storage` a la genérica `struct sockaddr`!
Parece extraño y redundante, eh. La respuesta es que simplemente no es
lo suficientemente grande, y supongo que haberla cambiado a esta altura
de la historia hubiera sido Problemático™. Así que hicieron una nueva.)

Acordate: si hacés [ix[connect()@\texttt{connect()}!on datagram sockets]]
`connect()` a un socket datagram, podés simplemente usar `send()` y
`recv()` para todas tus transacciones. El socket sigue siendo un socket
datagram, y los paquetes van a seguir usando UDP, pero la interfaz de
sockets va a agregar automáticamente la información de origen y destino
por vos.


## `close()` y `shutdown()` - ¡Volá de acá!

¡Fiú! Estuviste haciendo `send()` y `recv()` de datos todo el día, y ya
fue suficiente. Ya estás para cerrar la conexión en tu socket
descriptor. Es fácil. Podés simplemente usar la función [ixtt[close()]]
`close()` estándar de los descriptores de archivos Unix:

```{.c}
close(sockfd); 
```

Esto evitará cualquier otra lectura o escritura en ese socket.
Cualquiera que intente leer o escribir al socket en el extremo remoto va
a recibir un error.

Si quisieras tener un poco más de control sobre cómo se cierra el
socket, podés usar la función [ixtt[shutdown()]] `shutdown()`. Esta te
permite cortar la comunicación en una dirección dada, o en ambas
direcciones (como hace `close()`). Sinopsis:

```{.c}
int shutdown(int sockfd, int how); 
```

`sockfd` es el descriptor de socket que querés cerrar, y `how` es uno de
estos:

| `how` | Efecto                                            |
|:-----:|---------------------------------------------------|
|  `0`  | No permite recibir más                            |
|  `1`  | No permite enviar más                             |
|  `2`  | No permite enviar ni recibir más (como `close()`) |

`shutdown()` devuelve `0` cuando funciona, y `-1` al fallar (y escribe
`errno`).

Si llegás a usar `shutdown()` en un socket datagram no conectado,
simplemente hará que el socket no esté disponible para futuros `send()`
y `recv()`s (acordate que podés usarlas si le hacés `connect()` a tu
socket datagram).

Es importante notar que `shutdown()` no hace el cierre propiamente dicho
del descriptor de archivo - simplemente cambia su usabilidad. Para
liberar al descriptor de socket, tenés que usar `close()`.

Nada más.

(Excepto recordar que si estás usando [ix[Windows]] Windows y
[ix[Winsock]] Winsock deberías llamar a [ixtt[closesocket()]]
`closesocket()` en vez de a `close()`.)


## `getpeername()` - ¿Y vos quién sos?

[ixtt[getpeername()]] Esta función es muy fácil.

Es tan fácil que casi no le doy su propia sección. Pero, ¡hey! Acá está,
de todos modos.

La función `getpeername()` te va a decir quién está del otro lado de un
socket stream conectado. La sinopsis:

```{.c}
#include <sys/socket.h>

int getpeername(int sockfd, struct sockaddr *addr, int *addrlen); 
```

`sockfd` es el descriptor del socket stream conectado, `addr` es un
puntero a un `struct sockaddr` (o un `struct sockaddr_in`) que va a
contener la información sobre el otro extremo de la conexión, y
`addrlen` es un puntero a un `int`, que debería ser inicializado a
`sizeof *addr` o `sizeof(struct sockaddr)`.

Si falla, la función devuelve `-1` y escribe `errno`.

Una vez que tenés su dirección, podés usar [ixtt[inet\_ntop()]]
`inet_ntop()`, [ixtt[getnameinfo()]] `getnameinfo()`, o
[ixtt[gethostbyaddr()]] `gethostbyaddr()` para imprimirla o conseguir
más información. No, no podés conseguir su nombre de usuario. (Ok, ok.
Si la otra computadora está corriendo un daemon ident, sería posible.
Pero, de todos modos, eso está fuera del alcance de este documento. Mirá
la [flrfc[RFC 1413|1413]] para más info.)


## `gethostname()` - ¿Quién soy yo?

[ixtt[gethostname()]] Aún más fácil que `getpeername()` es la función
`gethostname()`. Devuelve el nombre de red de la computadora en que está
corriendo tu programa. Ese nombre lo podés usar con
[ixtt[gethostbyname()]] `gethostbyname()`, debajo, para determinar la
[ix[IP]] dirección IP de tu máquina local.

¿Hay algo más divertido que esto? Se me ocurren algunas cosas, pero no
tienen nada que ver con programación de sockets. Como sea, así es la
función:

```{.c}
#include <unistd.h>

int gethostname(char *hostname, size_t size); 
```

Los parámetros son sencillos: `hostname` es un puntero a un array de
caracteres que va a contener el nombre del host cuando la función
retorne, y `size` es la longitud en bytes del array `hostname`.

La función devuelve `0` cuando resuelve exitosamente, y `-1` al haber un
error, escribiendo `errno` como de costumbre.


# Trasfondo sobre Cliente-Servidor

[ix[client/server]] Es un mundo cliente-servidor, che. Prácticamente
todo en las redes trata de procesos clientes hablando a procesos
servidores, y vice-versa. Fijate `telnet`, por ejemplo. Cuando te
conectás a un equipo remoto en el puerto 23 con telnet (el cliente), un
programa en ese equipo (llamado `telnetd`, el servidor) cobra vida. Ese
proceso maneja tu conexión telnet entrante, te pregunta el login, etc.

![Interacción Cliente-Servidor.](cs.pdf "[Diagrama de interacción Cliente-Servidor]")

El intercambio de información entre cliente y servidor se resume en este
diagrama.

Notá que el par cliente-servidor puede hablar `SOCK_STREAM`,
`SOCK_DGRAM`, o cualquier otra cosa - siempre y cuando hablen ambos la
misma cosa. Algunos buenos ejemplos de pares cliente-servidor son
`telnet`/`telnetd`, `ftp`/`ftpd`, o `Firefox`/`Apache`. Cada vez que
usás `ftp`, hay un programa remoto, `ftpd`, que te sirve.

A menudo habrá un único servidor en una máquina, y ese servidor manejará
múltiples clientes usando [ixtt[fork()]] `fork()`. La rutina básica es:
el servidor espera una conexión, la acepta (`accept()`), y hace `fork()`
a un proceso hijo que la maneje. Eso es lo que hace nuestro servidor de
ejemplo en la próxima sección.


## Un servidor stream sencillo

[ix[server!stream]] Todo lo que hace este servidor es enviar la cadena
"`Hello, world!`" por una conexión stream. Todo lo que tenés que hacer
para probarlo es correrlo en una ventana, y conectártele por telnet
desde otra con:

```
$ telnet remotehostname 3490
```

donde `remotehostname` es el nombre de la máquina en que lo estás
corriendo.

[flx[El código del servidor|server.c]]:

```{.c .numberLines}
/*
** server.c -- demostración de un servidor socket stream
*/

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <netdb.h>
#include <arpa/inet.h>
#include <sys/wait.h>
#include <signal.h>

#define PORT "3490"  // el puerto al que se van a conectar los usuarios

#define BACKLOG 10   // cuantas conexiones pendientes mantener en cola

void sigchld_handler(int s)
{
    // waitpid() puede pisar errno, asi que lo guardamos y luego lo restauramos
    int saved_errno = errno;

    while(waitpid(-1, NULL, WNOHANG) > 0);

    errno = saved_errno;
}


// consigue el sockaddr, IPv4 o IPv6:
void *get_in_addr(struct sockaddr *sa)
{
    if (sa->sa_family == AF_INET) {
        return &(((struct sockaddr_in*)sa)->sin_addr);
    }

    return &(((struct sockaddr_in6*)sa)->sin6_addr);
}

int main(void)
{
    int sockfd, new_fd;  // en sock_fd escucha, new_fd son las nuevas conexiones
    struct addrinfo hints, *servinfo, *p;
    struct sockaddr_storage their_addr; // informacion de direccion de quien se conecte
    socklen_t sin_size;
    struct sigaction sa;
    int yes=1;
    char s[INET6_ADDRSTRLEN];
    int rv;

    memset(&hints, 0, sizeof hints);
    hints.ai_family = AF_UNSPEC;
    hints.ai_socktype = SOCK_STREAM;
    hints.ai_flags = AI_PASSIVE; // usa mi IP

    if ((rv = getaddrinfo(NULL, PORT, &hints, &servinfo)) != 0) {
        fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(rv));
        return 1;
    }

    // recorre todos los resultados y hace bind al primero que pueda
    for(p = servinfo; p != NULL; p = p->ai_next) {
        if ((sockfd = socket(p->ai_family, p->ai_socktype,
                p->ai_protocol)) == -1) {
            perror("servidor: socket");
            continue;
        }

        if (setsockopt(sockfd, SOL_SOCKET, SO_REUSEADDR, &yes,
                sizeof(int)) == -1) {
            perror("setsockopt");
            exit(1);
        }

        if (bind(sockfd, p->ai_addr, p->ai_addrlen) == -1) {
            close(sockfd);
            perror("servidor: bind");
            continue;
        }

        break;
    }

    freeaddrinfo(servinfo); // listo con esta estructura

    if (p == NULL)  {
        fprintf(stderr, "servidor: no se pudo hacer bind\n");
        exit(1);
    }

    if (listen(sockfd, BACKLOG) == -1) {
        perror("listen");
        exit(1);
    }

    sa.sa_handler = sigchld_handler; // termina todos los subprocesos muertos
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = SA_RESTART;
    if (sigaction(SIGCHLD, &sa, NULL) == -1) {
        perror("sigaction");
        exit(1);
    }

    printf("servidor: esperando por conexiones...\n");

    while(1) {  // bucle principal de accept()
        sin_size = sizeof their_addr;
        new_fd = accept(sockfd, (struct sockaddr *)&their_addr, &sin_size);
        if (new_fd == -1) {
            perror("accept");
            continue;
        }

        inet_ntop(their_addr.ss_family,
            get_in_addr((struct sockaddr *)&their_addr),
            s, sizeof s);
        printf("servidor: se obtuvo una conexion desde %s\n", s);

        if (!fork()) { // este es el proceso hijo
            close(sockfd); // el hijo no necesita el socket que escucha
            if (send(new_fd, "Hello, world!", 13, 0) == -1)
                perror("send");
            close(new_fd);
            exit(0);
        }
        close(new_fd);  // el padre no necesita el socket nuevo
    }

    return 0;
}
```

Si te interesa, tengo todo el código en una gran función `main()` por
(lo que yo considero) claridad sintáctica. Sentite libre de dividirlo en
funciones más chicas si eso te hace sentir mejor.

(Además, todo esto del [ixtt[sigaction()]] `sigaction()` puede que te
sea nuevo - todo bien. El código que está ahí se encarga de terminar los
[ix[zombie process]] procesos zombies que aparecen a medida que los
procesos hijos `fork()`eados finalizan. Si creás muchos procesos zombies
y no los terminás, tu administrador@ del sistema se va a poner
nervios@).

Podés leer los datos que envía este servidor usando el cliente que está
en la siguiente sección.


## Un cliente stream sencillo

[ix[client!stream]] Este amigo es todavía más fácil que el servidor.
Todo lo que hace este cliente es conectarse al servidor que especifiques
por línea de comando, al puerto 3490, y lee el string que le envía el
servidor.

[flx[El código del cliente|client.c]]:

```{.c .numberLines}
/*
** client.c -- demostracion de un cliente socket stream
*/

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <netdb.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>

#include <arpa/inet.h>

#define PORT "3490" // el puerto al que se va a conectar el cliente

#define MAXDATASIZE 100 // cantidad maxima de bytes que podemos recibir por vez

// consigue la sockaddr, IPv4 o IPv6:
void *get_in_addr(struct sockaddr *sa)
{
    if (sa->sa_family == AF_INET) {
        return &(((struct sockaddr_in*)sa)->sin_addr);
    }

    return &(((struct sockaddr_in6*)sa)->sin6_addr);
}

int main(int argc, char *argv[])
{
    int sockfd, numbytes;  
    char buf[MAXDATASIZE];
    struct addrinfo hints, *servinfo, *p;
    int rv;
    char s[INET6_ADDRSTRLEN];

    if (argc != 2) {
        fprintf(stderr,"uso: client hostname\n");
        exit(1);
    }

    memset(&hints, 0, sizeof hints);
    hints.ai_family = AF_UNSPEC;
    hints.ai_socktype = SOCK_STREAM;

    if ((rv = getaddrinfo(argv[1], PORT, &hints, &servinfo)) != 0) {
        fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(rv));
        return 1;
    }

    // recorre todos los resultados y hace bind al primero que pueda
    for(p = servinfo; p != NULL; p = p->ai_next) {
        if ((sockfd = socket(p->ai_family, p->ai_socktype,
                p->ai_protocol)) == -1) {
            perror("cliente: socket");
            continue;
        }

        if (connect(sockfd, p->ai_addr, p->ai_addrlen) == -1) {
            close(sockfd);
            perror("cliente: connect");
            continue;
        }

        break;
    }

    if (p == NULL) {
        fprintf(stderr, "cliente: no se pudo hacer connect\n");
        return 2;
    }

    inet_ntop(p->ai_family, get_in_addr((struct sockaddr *)p->ai_addr),
            s, sizeof s);
    printf("cliente: conectandose a %s\n", s);

    freeaddrinfo(servinfo); // listo con esta estructura

    if ((numbytes = recv(sockfd, buf, MAXDATASIZE-1, 0)) == -1) {
        perror("recv");
        exit(1);
    }

    buf[numbytes] = '\0';

    printf("cliente: se recibio '%s'\n",buf);

    close(sockfd);

    return 0;
}
```

Fijate que si no corrés el servidor antes que el cliente, `connect()`
devuelve [ix[Connection refused]] "Connection refused" (conexión
rechazada). Muy útil.


## Datagram Sockets {#datagram}

We've already covered the basics of UDP datagram sockets with our
discussion of `sendto()` and `recvfrom()`, above, so I'll just present a
couple of sample programs: `talker.c` and `listener.c`.

[ix[server!datagram]] `listener` sits on a machine waiting for an
incoming packet on port 4950. `talker` sends a packet to that port, on
the specified machine, that contains whatever the user enters on the
command line.

Because datagram sockets are connectionless and just fire packets off
into the ether with callous disregard for success, we are going to tell
the client and server to use specifically IPv6. This way we avoid the
situation where the server is listening on IPv6 and the client sends on
IPv4; the data simply would not be received. (In our connected TCP
stream sockets world, we might still have the mismatch, but the error on
`connect()` for one address family would cause us to retry for the
other.)

Here is the [flx[source for `listener.c`|listener.c]]:

```{.c .numberLines}
/*
** listener.c -- a datagram sockets "server" demo
*/

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>

#define MYPORT "4950"	// the port users will be connecting to

#define MAXBUFLEN 100

// get sockaddr, IPv4 or IPv6:
void *get_in_addr(struct sockaddr *sa)
{
	if (sa->sa_family == AF_INET) {
		return &(((struct sockaddr_in*)sa)->sin_addr);
	}

	return &(((struct sockaddr_in6*)sa)->sin6_addr);
}

int main(void)
{
	int sockfd;
	struct addrinfo hints, *servinfo, *p;
	int rv;
	int numbytes;
	struct sockaddr_storage their_addr;
	char buf[MAXBUFLEN];
	socklen_t addr_len;
	char s[INET6_ADDRSTRLEN];

	memset(&hints, 0, sizeof hints);
	hints.ai_family = AF_INET6; // set to AF_INET to use IPv4
	hints.ai_socktype = SOCK_DGRAM;
	hints.ai_flags = AI_PASSIVE; // use my IP

	if ((rv = getaddrinfo(NULL, MYPORT, &hints, &servinfo)) != 0) {
		fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(rv));
		return 1;
	}

	// loop through all the results and bind to the first we can
	for(p = servinfo; p != NULL; p = p->ai_next) {
		if ((sockfd = socket(p->ai_family, p->ai_socktype,
				p->ai_protocol)) == -1) {
			perror("listener: socket");
			continue;
		}

		if (bind(sockfd, p->ai_addr, p->ai_addrlen) == -1) {
			close(sockfd);
			perror("listener: bind");
			continue;
		}

		break;
	}

	if (p == NULL) {
		fprintf(stderr, "listener: failed to bind socket\n");
		return 2;
	}

	freeaddrinfo(servinfo);

	printf("listener: waiting to recvfrom...\n");

	addr_len = sizeof their_addr;
	if ((numbytes = recvfrom(sockfd, buf, MAXBUFLEN-1 , 0,
		(struct sockaddr *)&their_addr, &addr_len)) == -1) {
		perror("recvfrom");
		exit(1);
	}

	printf("listener: got packet from %s\n",
		inet_ntop(their_addr.ss_family,
			get_in_addr((struct sockaddr *)&their_addr),
			s, sizeof s));
	printf("listener: packet is %d bytes long\n", numbytes);
	buf[numbytes] = '\0';
	printf("listener: packet contains \"%s\"\n", buf);

	close(sockfd);

	return 0;
}
```

Notice that in our call to `getaddrinfo()` we're finally using
`SOCK_DGRAM`.  Also, note that there's no need to `listen()` or
`accept()`. This is one of the perks of using unconnected datagram
sockets!

[ix[client!datagram]] Next comes the [flx[source for
`talker.c`|talker.c]]:

```{.c .numberLines}
/*
** talker.c -- a datagram "client" demo
*/

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>

#define SERVERPORT "4950"	// the port users will be connecting to

int main(int argc, char *argv[])
{
	int sockfd;
	struct addrinfo hints, *servinfo, *p;
	int rv;
	int numbytes;

	if (argc != 3) {
		fprintf(stderr,"usage: talker hostname message\n");
		exit(1);
	}

	memset(&hints, 0, sizeof hints);
	hints.ai_family = AF_INET6; // set to AF_INET to use IPv4
	hints.ai_socktype = SOCK_DGRAM;

	if ((rv = getaddrinfo(argv[1], SERVERPORT, &hints, &servinfo)) != 0) {
		fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(rv));
		return 1;
	}

	// loop through all the results and make a socket
	for(p = servinfo; p != NULL; p = p->ai_next) {
		if ((sockfd = socket(p->ai_family, p->ai_socktype,
				p->ai_protocol)) == -1) {
			perror("talker: socket");
			continue;
		}

		break;
	}

	if (p == NULL) {
		fprintf(stderr, "talker: failed to create socket\n");
		return 2;
	}

	if ((numbytes = sendto(sockfd, argv[2], strlen(argv[2]), 0,
			 p->ai_addr, p->ai_addrlen)) == -1) {
		perror("talker: sendto");
		exit(1);
	}

	freeaddrinfo(servinfo);

	printf("talker: sent %d bytes to %s\n", numbytes, argv[1]);
	close(sockfd);

	return 0;
}
```

And that's all there is to it! Run `listener` on some machine, then run
`talker` on another. Watch them communicate! Fun G-rated excitement for
the entire nuclear family!

You don't even have to run the server this time! You can run `talker` by
itself, and it just happily fires packets off into the ether where they
disappear if no one is ready with a `recvfrom()` on the other side.
Remember: data sent using UDP datagram sockets isn't guaranteed to
arrive!

Except for one more tiny detail that I've mentioned many times in the
past: [ix[connect()@\texttt{connect()}!on datagram sockets]] connected
datagram sockets. I need to talk about this here, since we're in the
datagram section of the document. Let's say that `talker` calls
`connect()` and specifies the `listener`'s address. From that point on,
`talker` may only sent to and receive from the address specified by
`connect()`. For this reason, you don't have to use `sendto()` and
`recvfrom()`; you can simply use `send()` and `recv()`.


# Slightly Advanced Techniques

These aren't _really_ advanced, but they're getting out of the more
basic levels we've already covered. In fact, if you've gotten this far,
you should consider yourself fairly accomplished in the basics of Unix
network programming!  Congratulations!

So here we go into the brave new world of some of the more esoteric
things you might want to learn about sockets. Have at it!


## Blocking {#blocking}

[ix[blocking]] Blocking. You've heard about it---now what the heck is
it? In a nutshell, "block" is techie jargon for "sleep". You probably
noticed that when you run `listener`, above, it just sits there until a
packet arrives. What happened is that it called `recvfrom()`, there was
no data, and so `recvfrom()` is said to "block" (that is, sleep there)
until some data arrives.

Lots of functions block. `accept()` blocks. All the `recv()` functions
block.  The reason they can do this is because they're allowed to. When
you first create the socket descriptor with `socket()`, the kernel sets
it to blocking.  [ix[non-blocking sockets]] If you don't want a socket
to be blocking, you have to make a call to [ixtt[fcntl()]] `fcntl()`:

```{.c .numberLines}
#include <unistd.h>
#include <fcntl.h>
.
.
.
sockfd = socket(PF_INET, SOCK_STREAM, 0);
fcntl(sockfd, F_SETFL, O_NONBLOCK);
.
.
. 
```

By setting a socket to non-blocking, you can effectively "poll" the
socket for information. If you try to read from a non-blocking socket
and there's no data there, it's not allowed to block---it will return
`-1` and `errno` will be set to [ixtt[EAGAIN@]] `EAGAIN` or
[ixtt[EWOULDBLOCK]] `EWOULDBLOCK`.

(Wait---it can return [ixtt[EAGAIN]] `EAGAIN` _or_ [ixtt[EWOULDBLOCK]]
`EWOULDBLOCK`? Which do you check for?  The specification doesn't
actually specify which your system will return, so for portability,
check them both.)

Generally speaking, however, this type of polling is a bad idea. If you
put your program in a busy-wait looking for data on the socket, you'll
suck up CPU time like it was going out of style. A more elegant solution
for checking to see if there's data waiting to be read comes in the
following section on [ixtt[poll()]] `poll()`.


## `poll()`---Synchronous I/O Multiplexing {#poll}

[ixtt[poll()]] What you really want to be able to do is somehow monitor
a _bunch_ of sockets at once and then handle the ones that have data
ready. This way you don't have to continously poll all those sockets to
see which are ready to read.

> _A word of warning: `poll()` is horribly slow when it comes to giant
> numbers of connections. In those circumstances, you'll get better
> performance out of an event library such as
> [fl[libevent|https://libevent.org/]] that attempts to use the fastest
> possible method availabile on your system._

So how can you avoid polling? Not slightly ironically, you can avoid
polling by using the `poll()` system call. In a nutshell, we're going to
ask the operating system to do all the dirty work for us, and just let
us know when some data is ready to read on which sockets. In the
meantime, our process can go to sleep, saving system resources.

The general gameplan is to keep an array of `struct pollfd`s with
information about which socket descriptors we want to monitor, and what
kind of events we want to monitor for. The OS will block on the `poll()`
call until one of those events occurs (e.g. "socket ready to read!") or
until a user-specified timeout occurs.

Usefully, a `listen()`ing socket will return "ready to read" when a new
incoming connection is ready to be `accept()`ed.

That's enough banter. How do we use this?

``` {.c}
#include <poll.h>

int poll(struct pollfd fds[], nfds_t nfds, int timeout);
```

`fds` is our array of information (which sockets to monitor for what),
`nfds` is the count of elements in the array, and `timeout` is a timeout
in milliseconds. It returns the number of elements in the array that
have had an event occur.

Let's have a look at that `struct`:

[ixtt[struct pollfd]]

``` {.c}
struct pollfd {
    int fd;         // the socket descriptor
    short events;   // bitmap of events we're interested in
    short revents;  // when poll() returns, bitmap of events that occurred
};
```

So we're going to have an array of those, and we'll see the `fd` field
for each element to a socket descriptor we're interested in monitoring.
And then we'll set the `events` field to indicate the type of events
we're interested in.

The `events` field is the bitwise-OR of the following:

| Macro     | Description                                                  |
|-----------|--------------------------------------------------------------|
| `POLLIN`  | Alert me when data is ready to `recv()` on this socket.      |
| `POLLOUT` | Alert me when I can `send()` data to this socket without blocking.|

Once you have your array of `struct pollfd`s in order, then you can pass
it to `poll()`, also passing the size of the array, as well as a timeout
value in milliseconds. (You can specify a negative timeout to wait
forever.)

After `poll()` returns, you can check the `revents` field to see if
`POLLIN` or `POLLOUT` is set, indicating that event occurred.

(There's actually more that you can do with the `poll()` call. See the
[`poll()` man page, below](#pollman), for more details.)

Here's [flx[an example|poll.c]] where we'll wait 2.5 seconds for data to
be ready to read from standard input, i.e. when you hit `RETURN`:

``` {.c .numberLines}
#include <stdio.h>
#include <poll.h>

int main(void)
{
    struct pollfd pfds[1]; // More if you want to monitor more

    pfds[0].fd = 0;          // Standard input
    pfds[0].events = POLLIN; // Tell me when ready to read

    // If you needed to monitor other things, as well:
    //pfds[1].fd = some_socket; // Some socket descriptor
    //pfds[1].events = POLLIN;  // Tell me when ready to read

    printf("Hit RETURN or wait 2.5 seconds for timeout\n");

    int num_events = poll(pfds, 1, 2500); // 2.5 second timeout

    if (num_events == 0) {
        printf("Poll timed out!\n");
    } else {
        int pollin_happened = pfds[0].revents & POLLIN;

        if (pollin_happened) {
            printf("File descriptor %d is ready to read\n", pfds[0].fd);
        } else {
            printf("Unexpected event occurred: %d\n", pfds[0].revents);
        }
    }

    return 0;
}
```

Notice again that `poll()` returns the number of elements in the `pfds`
array for which events have occurred. It doesn't tell you _which_
elements in the array (you still have to scan for that), but it does
tell you how many entries have a non-zero `revents` field (so you can
stop scanning after you find that many).

A couple questions might come up here: how to add new file descriptors
to the set I pass to `poll()`? For this, simply make sure you have
enough space in the array for all you need, or `realloc()` more space as
needed.

What about deleting items from the set? For this, you can copy the last
element in the array over-top the one you're deleting. And then pass in
one fewer as the count to `poll()`. Another option is that you can set
any `fd` field to a negative number and `poll()` will ignore it.

How can we put it all together into a chat server that you can `telnet`
to?

What we'll do is start a listener socket, and add it to the set of file
descriptors to `poll()`. (It will show ready-to-read when there's an
incoming connection.)

Then we'll add new connections to our `struct pollfd` array. And we'll
grow it dynamically if we run out of space.

When a connection is closed, we'll remove it from the array.

And when a connection is ready-to-read, we'll read the data from it and
send that data to all the other connections so they can see what the
other users typed.

So give [flx[this poll server|pollserver.c]] a try. Run it in one
window, then `telnet localhost 9034` from a number of other terminal
windows. You should be able to see what you type in one window in the
other ones (after you hit RETURN).

Not only that, but if you hit `CTRL-]` and type `quit` to exit `telnet`,
the server should detect the disconnection and remove you from the array
of file descriptors.

``` {.c .numberLines}
/*
** pollserver.c -- a cheezy multiperson chat server
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>
#include <poll.h>

#define PORT "9034"   // Port we're listening on

// Get sockaddr, IPv4 or IPv6:
void *get_in_addr(struct sockaddr *sa)
{
    if (sa->sa_family == AF_INET) {
        return &(((struct sockaddr_in*)sa)->sin_addr);
    }

    return &(((struct sockaddr_in6*)sa)->sin6_addr);
}

// Return a listening socket
int get_listener_socket(void)
{
    int listener;     // Listening socket descriptor
    int yes=1;        // For setsockopt() SO_REUSEADDR, below
    int rv;

    struct addrinfo hints, *ai, *p;

    // Get us a socket and bind it
    memset(&hints, 0, sizeof hints);
    hints.ai_family = AF_UNSPEC;
    hints.ai_socktype = SOCK_STREAM;
    hints.ai_flags = AI_PASSIVE;
    if ((rv = getaddrinfo(NULL, PORT, &hints, &ai)) != 0) {
        fprintf(stderr, "selectserver: %s\n", gai_strerror(rv));
        exit(1);
    }
    
    for(p = ai; p != NULL; p = p->ai_next) {
        listener = socket(p->ai_family, p->ai_socktype, p->ai_protocol);
        if (listener < 0) { 
            continue;
        }
        
        // Lose the pesky "address already in use" error message
        setsockopt(listener, SOL_SOCKET, SO_REUSEADDR, &yes, sizeof(int));

        if (bind(listener, p->ai_addr, p->ai_addrlen) < 0) {
            close(listener);
            continue;
        }

        break;
    }

    freeaddrinfo(ai); // All done with this

    // If we got here, it means we didn't get bound
    if (p == NULL) {
        return -1;
    }

    // Listen
    if (listen(listener, 10) == -1) {
        return -1;
    }

    return listener;
}

// Add a new file descriptor to the set
void add_to_pfds(struct pollfd *pfds[], int newfd, int *fd_count, int *fd_size)
{
    // If we don't have room, add more space in the pfds array
    if (*fd_count == *fd_size) {
        *fd_size *= 2; // Double it

        *pfds = realloc(*pfds, sizeof(**pfds) * (*fd_size));
    }

    (*pfds)[*fd_count].fd = newfd;
    (*pfds)[*fd_count].events = POLLIN; // Check ready-to-read

    (*fd_count)++;
}

// Remove an index from the set
void del_from_pfds(struct pollfd pfds[], int i, int *fd_count)
{
    // Copy the one from the end over this one
    pfds[i] = pfds[*fd_count-1];

    (*fd_count)--;
}

// Main
int main(void)
{
    int listener;     // Listening socket descriptor

    int newfd;        // Newly accept()ed socket descriptor
    struct sockaddr_storage remoteaddr; // Client address
    socklen_t addrlen;

    char buf[256];    // Buffer for client data

    char remoteIP[INET6_ADDRSTRLEN];

    // Start off with room for 5 connections
    // (We'll realloc as necessary)
    int fd_count = 0;
    int fd_size = 5;
    struct pollfd *pfds = malloc(sizeof *pfds * fd_size);

    // Set up and get a listening socket
    listener = get_listener_socket();

    if (listener == -1) {
        fprintf(stderr, "error getting listening socket\n");
        exit(1);
    }

    // Add the listener to set
    pfds[0].fd = listener;
    pfds[0].events = POLLIN; // Report ready to read on incoming connection

    fd_count = 1; // For the listener

    // Main loop
    for(;;) {
        int poll_count = poll(pfds, fd_count, -1);

        if (poll_count == -1) {
            perror("poll");
            exit(1);
        }

        // Run through the existing connections looking for data to read
        for(int i = 0; i < fd_count; i++) {

            // Check if someone's ready to read
            if (pfds[i].revents & POLLIN) { // We got one!!

                if (pfds[i].fd == listener) {
                    // If listener is ready to read, handle new connection

                    addrlen = sizeof remoteaddr;
                    newfd = accept(listener,
                        (struct sockaddr *)&remoteaddr,
                        &addrlen);

                    if (newfd == -1) {
                        perror("accept");
                    } else {
                        add_to_pfds(&pfds, newfd, &fd_count, &fd_size);

                        printf("pollserver: new connection from %s on "
                            "socket %d\n",
                            inet_ntop(remoteaddr.ss_family,
                                get_in_addr((struct sockaddr*)&remoteaddr),
                                remoteIP, INET6_ADDRSTRLEN),
                            newfd);
                    }
                } else {
                    // If not the listener, we're just a regular client
                    int nbytes = recv(pfds[i].fd, buf, sizeof buf, 0);

                    int sender_fd = pfds[i].fd;

                    if (nbytes <= 0) {
                        // Got error or connection closed by client
                        if (nbytes == 0) {
                            // Connection closed
                            printf("pollserver: socket %d hung up\n", sender_fd);
                        } else {
                            perror("recv");
                        }

                        close(pfds[i].fd); // Bye!

                        del_from_pfds(pfds, i, &fd_count);

                    } else {
                        // We got some good data from a client

                        for(int j = 0; j < fd_count; j++) {
                            // Send to everyone!
                            int dest_fd = pfds[j].fd;

                            // Except the listener and ourselves
                            if (dest_fd != listener && dest_fd != sender_fd) {
                                if (send(dest_fd, buf, nbytes, 0) == -1) {
                                    perror("send");
                                }
                            }
                        }
                    }
                } // END handle data from client
            } // END got ready-to-read from poll()
        } // END looping through file descriptors
    } // END for(;;)--and you thought it would never end!
    
    return 0;
}
```

In the next section, we'll look at a similar, older function called
`select()`. Both `select()` and `poll()` offer similar functionality and
performance, and only really differ in how they're used. `select()`
might be slightly more portable, but is perhaps a little clunkier in
use. Choose the one you like the best, as long as it's supported on your
system.


## `select()`---Synchronous I/O Multiplexing, Old School {#select}

[ixtt[select()]] This function is somewhat strange, but it's very
useful. Take the following situation: you are a server and you want to
listen for incoming connections as well as keep reading from the
connections you already have.

No problem, you say, just an `accept()` and a couple of `recv()`s. Not
so fast, buster! What if you're blocking on an `accept()` call? How are
you going to `recv()` data at the same time? "Use non-blocking sockets!"
No way! You don't want to be a CPU hog. What, then?

`select()` gives you the power to monitor several sockets at the same
time.  It'll tell you which ones are ready for reading, which are ready
for writing, and which sockets have raised exceptions, if you really
want to know that.

> _A word of warning: `select()`, though very portable, is terribly slow
> when it comes to giant numbers of connections. In those circumstances,
> you'll get better performance out of an event library such as
> [fl[libevent|https://libevent.org/]] that attempts to use the fastest
> possible method availabile on your system._

Without any further ado, I'll offer the synopsis of `select()`:

```{.c}
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

int select(int numfds, fd_set *readfds, fd_set *writefds,
           fd_set *exceptfds, struct timeval *timeout); 
```

The function monitors "sets" of file descriptors; in particular
`readfds`, `writefds`, and `exceptfds`. If you want to see if you can
read from standard input and some socket descriptor, `sockfd`, just add
the file descriptors `0` and `sockfd` to the set `readfds`. The
parameter `numfds` should be set to the values of the highest file
descriptor plus one. In this example, it should be set to `sockfd+1`,
since it is assuredly higher than standard input (`0`).

When `select()` returns, `readfds` will be modified to reflect which of
the file descriptors you selected which is ready for reading. You can
test them with the macro `FD_ISSET()`, below.

Before progressing much further, I'll talk about how to manipulate these
sets.  Each set is of the type `fd_set`. The following macros operate on
this type:

[ixtt[FD\_SET()]] [ixtt[FD\_CLR()]] [ixtt[FD\_ISSET()]] [ixtt[FD\_ZERO()]]

| Function                         | Description                          |
|----------------------------------|--------------------------------------|
| `FD_SET(int fd, fd_set *set);`   | Add `fd` to the `set`.               |
| `FD_CLR(int fd, fd_set *set);`   | Remove `fd` from the `set`.          |
| `FD_ISSET(int fd, fd_set *set);` | Return true if `fd` is in the `set`. |
| `FD_ZERO(fd_set *set);`          | Clear all entries from the `set`.    |

Finally, what is this weirded out [ixtt[struct timeval]] `struct
timeval`? Well, sometimes you don't want to wait forever for someone to
send you some data. Maybe every 96 seconds you want to print "Still
Going..." to the terminal even though nothing has happened. This time
structure allows you to specify a timeout period. If the time is
exceeded and `select()` still hasn't found any ready file descriptors,
it'll return so you can continue processing.

The `struct timeval` has the follow fields:

```{.c}
struct timeval {
    int tv_sec;     // seconds
    int tv_usec;    // microseconds
}; 
```

Just set `tv_sec` to the number of seconds to wait, and set `tv_usec` to
the number of microseconds to wait. Yes, that's _micro_seconds, not
milliseconds.  There are 1,000 microseconds in a millisecond, and 1,000
milliseconds in a second. Thus, there are 1,000,000 microseconds in a
second. Why is it "usec"?  The "u" is supposed to look like the Greek
letter μ (Mu) that we use for "micro". Also, when the function returns,
`timeout` _might_ be updated to show the time still remaining. This
depends on what flavor of Unix you're running.

Yay! We have a microsecond resolution timer! Well, don't count on it.
You'll probably have to wait some part of your standard Unix timeslice
no matter how small you set your `struct timeval`.

Other things of interest:  If you set the fields in your `struct
timeval` to `0`, `select()` will timeout immediately, effectively
polling all the file descriptors in your sets. If you set the parameter
`timeout` to NULL, it will never timeout, and will wait until the first
file descriptor is ready. Finally, if you don't care about waiting for a
certain set, you can just set it to NULL in the call to `select()`.

[flx[The following code snippet|select.c]] waits 2.5 seconds for
something to appear on standard input:

```{.c .numberLines}
/*
** select.c -- a select() demo
*/

#include <stdio.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

#define STDIN 0  // file descriptor for standard input

int main(void)
{
    struct timeval tv;
    fd_set readfds;

    tv.tv_sec = 2;
    tv.tv_usec = 500000;

    FD_ZERO(&readfds);
    FD_SET(STDIN, &readfds);

    // don't care about writefds and exceptfds:
    select(STDIN+1, &readfds, NULL, NULL, &tv);

    if (FD_ISSET(STDIN, &readfds))
        printf("A key was pressed!\n");
    else
        printf("Timed out.\n");

    return 0;
} 
```

If you're on a line buffered terminal, the key you hit should be RETURN
or it will time out anyway.

Now, some of you might think this is a great way to wait for data on a
datagram socket---and you are right: it _might_ be. Some Unices can use
select in this manner, and some can't. You should see what your local
man page says on the matter if you want to attempt it.

Some Unices update the time in your `struct timeval` to reflect the
amount of time still remaining before a timeout. But others do not.
Don't rely on that occurring if you want to be portable. (Use
[ixtt[gettimeofday()]] `gettimeofday()` if you need to track time
elapsed. It's a bummer, I know, but that's the way it is.)

What happens if a socket in the read set closes the connection? Well, in
that case, `select()` returns with that socket descriptor set as "ready
to read".  When you actually do `recv()` from it, `recv()` will return
`0`. That's how you know the client has closed the connection.

One more note of interest about `select()`: if you have a socket that is
[ix[select()@\texttt{select()}!with listen()@with \texttt{listen()}]]
[ix[listen()@\texttt{listen()}!with select()@with \texttt{select()}]]
`listen()`ing, you can check to see if there is a new connection by
putting that socket's file descriptor in the `readfds` set.

And that, my friends, is a quick overview of the almighty `select()`
function.

But, by popular demand, here is an in-depth example. Unfortunately, the
difference between the dirt-simple example, above, and this one here is
significant. But have a look, then read the description that follows it.

[flx[This program|selectserver.c]] acts like a simple multi-user chat
server. Start it running in one window, then `telnet` to it ("`telnet
hostname 9034`") from multiple other windows. When you type something in
one `telnet` session, it should appear in all the others.

```{.c .numberLines}
/*
** selectserver.c -- a cheezy multiperson chat server
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>

#define PORT "9034"   // port we're listening on

// get sockaddr, IPv4 or IPv6:
void *get_in_addr(struct sockaddr *sa)
{
    if (sa->sa_family == AF_INET) {
        return &(((struct sockaddr_in*)sa)->sin_addr);
    }

    return &(((struct sockaddr_in6*)sa)->sin6_addr);
}

int main(void)
{
    fd_set master;    // master file descriptor list
    fd_set read_fds;  // temp file descriptor list for select()
    int fdmax;        // maximum file descriptor number

    int listener;     // listening socket descriptor
    int newfd;        // newly accept()ed socket descriptor
    struct sockaddr_storage remoteaddr; // client address
    socklen_t addrlen;

    char buf[256];    // buffer for client data
    int nbytes;

    char remoteIP[INET6_ADDRSTRLEN];

    int yes=1;        // for setsockopt() SO_REUSEADDR, below
    int i, j, rv;

    struct addrinfo hints, *ai, *p;

    FD_ZERO(&master);    // clear the master and temp sets
    FD_ZERO(&read_fds);

    // get us a socket and bind it
    memset(&hints, 0, sizeof hints);
    hints.ai_family = AF_UNSPEC;
    hints.ai_socktype = SOCK_STREAM;
    hints.ai_flags = AI_PASSIVE;
    if ((rv = getaddrinfo(NULL, PORT, &hints, &ai)) != 0) {
        fprintf(stderr, "selectserver: %s\n", gai_strerror(rv));
        exit(1);
    }
    
    for(p = ai; p != NULL; p = p->ai_next) {
        listener = socket(p->ai_family, p->ai_socktype, p->ai_protocol);
        if (listener < 0) { 
            continue;
        }
        
        // lose the pesky "address already in use" error message
        setsockopt(listener, SOL_SOCKET, SO_REUSEADDR, &yes, sizeof(int));

        if (bind(listener, p->ai_addr, p->ai_addrlen) < 0) {
            close(listener);
            continue;
        }

        break;
    }

    // if we got here, it means we didn't get bound
    if (p == NULL) {
        fprintf(stderr, "selectserver: failed to bind\n");
        exit(2);
    }

    freeaddrinfo(ai); // all done with this

    // listen
    if (listen(listener, 10) == -1) {
        perror("listen");
        exit(3);
    }

    // add the listener to the master set
    FD_SET(listener, &master);

    // keep track of the biggest file descriptor
    fdmax = listener; // so far, it's this one

    // main loop
    for(;;) {
        read_fds = master; // copy it
        if (select(fdmax+1, &read_fds, NULL, NULL, NULL) == -1) {
            perror("select");
            exit(4);
        }

        // run through the existing connections looking for data to read
        for(i = 0; i <= fdmax; i++) {
            if (FD_ISSET(i, &read_fds)) { // we got one!!
                if (i == listener) {
                    // handle new connections
                    addrlen = sizeof remoteaddr;
                    newfd = accept(listener,
                        (struct sockaddr *)&remoteaddr,
                        &addrlen);

                    if (newfd == -1) {
                        perror("accept");
                    } else {
                        FD_SET(newfd, &master); // add to master set
                        if (newfd > fdmax) {    // keep track of the max
                            fdmax = newfd;
                        }
                        printf("selectserver: new connection from %s on "
                            "socket %d\n",
                            inet_ntop(remoteaddr.ss_family,
                                get_in_addr((struct sockaddr*)&remoteaddr),
                                remoteIP, INET6_ADDRSTRLEN),
                            newfd);
                    }
                } else {
                    // handle data from a client
                    if ((nbytes = recv(i, buf, sizeof buf, 0)) <= 0) {
                        // got error or connection closed by client
                        if (nbytes == 0) {
                            // connection closed
                            printf("selectserver: socket %d hung up\n", i);
                        } else {
                            perror("recv");
                        }
                        close(i); // bye!
                        FD_CLR(i, &master); // remove from master set
                    } else {
                        // we got some data from a client
                        for(j = 0; j <= fdmax; j++) {
                            // send to everyone!
                            if (FD_ISSET(j, &master)) {
                                // except the listener and ourselves
                                if (j != listener && j != i) {
                                    if (send(j, buf, nbytes, 0) == -1) {
                                        perror("send");
                                    }
                                }
                            }
                        }
                    }
                } // END handle data from client
            } // END got new incoming connection
        } // END looping through file descriptors
    } // END for(;;)--and you thought it would never end!
    
    return 0;
}
```

Notice I have two file descriptor sets in the code: `master` and
`read_fds`. The first, `master`, holds all the socket descriptors that
are currently connected, as well as the socket descriptor that is
listening for new connections.

The reason I have the `master` set is that `select()` actually _changes_
the set you pass into it to reflect which sockets are ready to read.
Since I have to keep track of the connections from one call of
`select()` to the next, I must store these safely away somewhere. At the
last minute, I copy the `master` into the `read_fds`, and then call
`select()`.

But doesn't this mean that every time I get a new connection, I have to
add it to the `master` set? Yup! And every time a connection closes, I
have to remove it from the `master` set? Yes, it does.

Notice I check to see when the `listener` socket is ready to read. When
it is, it means I have a new connection pending, and I `accept()` it and
add it to the `master` set. Similarly, when a client connection is ready
to read, and `recv()` returns `0`, I know the client has closed the
connection, and I must remove it from the `master` set.

If the client `recv()` returns non-zero, though, I know some data has
been received. So I get it, and then go through the `master` list and
send that data to all the rest of the connected clients.

And that, my friends, is a less-than-simple overview of the almighty
`select()` function.

Quick note to all you Linux fans out there: sometimes, in rare
circumstances, Linux's `select()` can return "ready-to-read" and then
not actually be ready to read! This means it will block on the `read()`
after the `select()` says it won't! Why you little---! Anyway, the
workaround solution is to set the [ixtt[O\_NONBLOCK]] `O_NONBLOCK` flag
on the receiving socket so it errors with `EWOULDBLOCK` (which you can
just safely ignore if it occurs). See the [`fcntl()` reference
page](#fcntlman) for more info on setting a socket to non-blocking.

In addition, here is a bonus afterthought: there is another function
called [ixtt[poll()]] `poll()` which behaves much the same way
`select()` does, but with a different system for managing the file
descriptor sets. [Check it out!](#pollman)


## Handling Partial `send()`s {#sendall}

Remember back in the [section about `send()`](#sendrecv), above, when I
said that `send()` might not send all the bytes you asked it to? That
is, you want it to send 512 bytes, but it returns 412. What happened to
the remaining 100 bytes?

Well, they're still in your little buffer waiting to be sent out. Due to
circumstances beyond your control, the kernel decided not to send all
the data out in one chunk, and now, my friend, it's up to you to get the
data out there.

[ixtt[sendall()]] You could write a function like this to do it, too:

```{.c .numberLines}
#include <sys/types.h>
#include <sys/socket.h>

int sendall(int s, char *buf, int *len)
{
    int total = 0;        // how many bytes we've sent
    int bytesleft = *len; // how many we have left to send
    int n;

    while(total < *len) {
        n = send(s, buf+total, bytesleft, 0);
        if (n == -1) { break; }
        total += n;
        bytesleft -= n;
    }

    *len = total; // return number actually sent here

    return n==-1?-1:0; // return -1 on failure, 0 on success
} 
```

In this example, `s` is the socket you want to send the data to, `buf`
is the buffer containing the data, and `len` is a pointer to an `int`
containing the number of bytes in the buffer.

The function returns `-1` on error (and `errno` is still set from the
call to `send()`). Also, the number of bytes actually sent is returned
in `len`. This will be the same number of bytes you asked it to send,
unless there was an error. `sendall()` will do it's best, huffing and
puffing, to send the data out, but if there's an error, it gets back to
you right away.

For completeness, here's a sample call to the function:

```{.c .numberLines}
char buf[10] = "Beej!";
int len;

len = strlen(buf);
if (sendall(s, buf, &len) == -1) {
    perror("sendall");
    printf("We only sent %d bytes because of the error!\n", len);
} 
```

What happens on the receiver's end when part of a packet arrives? If the
packets are variable length, how does the receiver know when one packet
ends and another begins? Yes, real-world scenarios are a royal pain in
the [ix[donkeys]] donkeys. You probably have to [ix[data encapsulation]]
_encapsulate_ (remember that from the [data encapsulation
section](#lowlevel) way back there at the beginning?)  Read on for
details!


## Serialization---How to Pack Data {#serialization}

[ix[serialization]] It's easy enough to send text data across the
network, you're finding, but what happens if you want to send some
"binary" data like `int`s or `float`s? It turns out you have a few
options.

1. Convert the number into text with a function like `sprintf()`, then
   send the text. The receiver will parse the text back into a number
   using a function like `strtol()`.

2. Just send the data raw, passing a pointer to the data to `send()`.

3. Encode the number into a portable binary form. The receiver will
   decode it.

Sneak preview! Tonight only!

[_Curtain raises_]

Beej says, "I prefer Method Three, above!"

[_THE END_]

(Before I begin this section in earnest, I should tell you that there
are libraries out there for doing this, and rolling your own and
remaining portable and error-free is quite a challenge. So hunt around
and do your homework before deciding to implement this stuff yourself. I
include the information here for those curious about how things like
this work.)

Actually all the methods, above, have their drawbacks and advantages,
but, like I said, in general, I prefer the third method. First, though,
let's talk about some of the drawbacks and advantages to the other two.

The first method, encoding the numbers as text before sending, has the
advantage that you can easily print and read the data that's coming over
the wire.  Sometimes a human-readable protocol is excellent to use in a
non-bandwidth-intensive situation, such as with [ix[IRC]] [fl[Internet
Relay Chat (IRC)|https://en.wikipedia.org/wiki/Internet_Relay_Chat]].
However, it has the disadvantage that it is slow to convert, and the
results almost always take up more space than the original number!

Method two: passing the raw data. This one is quite easy (but
dangerous!): just take a pointer to the data to send, and call send with
it.

```{.c}
double d = 3490.15926535;

send(s, &d, sizeof d, 0);  /* DANGER--non-portable! */
```

The receiver gets it like this:

```{.c}
double d;

recv(s, &d, sizeof d, 0);  /* DANGER--non-portable! */
```

Fast, simple---what's not to like? Well, it turns out that not all
architectures represent a `double` (or `int` for that matter) with the
same bit representation or even the same byte ordering! The code is
decidedly non-portable. (Hey---maybe you don't need portability, in
which case this is nice and fast.)

When packing integer types, we've already seen how the [ixtt[htons()]]
`htons()`-class of functions can help keep things portable by
transforming the numbers into [ix[byte ordering]] Network Byte Order,
and how that's the Right Thing to do. Unfortunately, there are no
similar functions for `float` types. Is all hope lost?

Fear not! (Were you afraid there for a second? No? Not even a little
bit?) There is something we can do: we can pack (or "marshal", or
"serialize", or one of a thousand million other names) the data into a
known binary format that the receiver can unpack on the remote side.

What do I mean by "known binary format"? Well, we've already seen the
`htons()` example, right? It changes (or "encodes", if you want to think
of it that way) a number from whatever the host format is into Network
Byte Order. To reverse (unencode) the number, the receiver calls
`ntohs()`.

But didn't I just get finished saying there wasn't any such function for
other non-integer types? Yes. I did. And since there's no standard way
in C to do this, it's a bit of a pickle (that a gratuitous pun there for
you Python fans).

The thing to do is to pack the data into a known format and send that
over the wire for decoding. For example, to pack `float`s, here's
[flx[something quick and dirty with plenty of room for
improvement|pack.c]]:

```{.c .numberLines}
#include <stdint.h>

uint32_t htonf(float f)
{
    uint32_t p;
    uint32_t sign;

    if (f < 0) { sign = 1; f = -f; }
    else { sign = 0; }
        
    p = ((((uint32_t)f)&0x7fff)<<16) | (sign<<31); // whole part and sign
    p |= (uint32_t)(((f - (int)f) * 65536.0f))&0xffff; // fraction

    return p;
}

float ntohf(uint32_t p)
{
    float f = ((p>>16)&0x7fff); // whole part
    f += (p&0xffff) / 65536.0f; // fraction

    if (((p>>31)&0x1) == 0x1) { f = -f; } // sign bit set

    return f;
}
```

The above code is sort of a naive implementation that stores a `float`
in a 32-bit number. The high bit (31) is used to store the sign of the
number ("1" means negative), and the next seven bits (30-16) are used to
store the whole number portion of the `float`. Finally, the remaining
bits (15-0) are used to store the fractional portion of the number.

Usage is fairly straightforward:

```{.c .numberLines}
#include <stdio.h>

int main(void)
{
    float f = 3.1415926, f2;
    uint32_t netf;

    netf = htonf(f);  // convert to "network" form
    f2 = ntohf(netf); // convert back to test

    printf("Original: %f\n", f);        // 3.141593
    printf(" Network: 0x%08X\n", netf); // 0x0003243F
    printf("Unpacked: %f\n", f2);       // 3.141586

    return 0;
}
```

On the plus side, it's small, simple, and fast. On the minus side, it's
not an efficient use of space and the range is severely restricted---try
storing a number greater-than 32767 in there and it won't be very happy!
You can also see in the above example that the last couple decimal
places are not correctly preserved.

What can we do instead? Well, _The_ Standard for storing floating point
numbers is known as [ix[IEEE-754]]
[fl[IEEE-754|https://en.wikipedia.org/wiki/IEEE_754]].  Most computers
use this format internally for doing floating point math, so in those
cases, strictly speaking, conversion wouldn't need to be done. But if
you want your source code to be portable, that's an assumption you can't
necessarily make. (On the other hand, if you want things to be fast, you
should optimize this out on platforms that don't need to do it! That's
what `htons()` and its ilk do.)

[flx[Here's some code that encodes floats and doubles into IEEE-754
format|ieee754.c]].  (Mostly---it doesn't encode NaN or Infinity, but it
could be modified to do that.)

```{.c .numberLines}
#define pack754_32(f) (pack754((f), 32, 8))
#define pack754_64(f) (pack754((f), 64, 11))
#define unpack754_32(i) (unpack754((i), 32, 8))
#define unpack754_64(i) (unpack754((i), 64, 11))

uint64_t pack754(long double f, unsigned bits, unsigned expbits)
{
    long double fnorm;
    int shift;
    long long sign, exp, significand;
    unsigned significandbits = bits - expbits - 1; // -1 for sign bit

    if (f == 0.0) return 0; // get this special case out of the way

    // check sign and begin normalization
    if (f < 0) { sign = 1; fnorm = -f; }
    else { sign = 0; fnorm = f; }

    // get the normalized form of f and track the exponent
    shift = 0;
    while(fnorm >= 2.0) { fnorm /= 2.0; shift++; }
    while(fnorm < 1.0) { fnorm *= 2.0; shift--; }
    fnorm = fnorm - 1.0;

    // calculate the binary form (non-float) of the significand data
    significand = fnorm * ((1LL<<significandbits) + 0.5f);

    // get the biased exponent
    exp = shift + ((1<<(expbits-1)) - 1); // shift + bias

    // return the final answer
    return (sign<<(bits-1)) | (exp<<(bits-expbits-1)) | significand;
}

long double unpack754(uint64_t i, unsigned bits, unsigned expbits)
{
    long double result;
    long long shift;
    unsigned bias;
    unsigned significandbits = bits - expbits - 1; // -1 for sign bit

    if (i == 0) return 0.0;

    // pull the significand
    result = (i&((1LL<<significandbits)-1)); // mask
    result /= (1LL<<significandbits); // convert back to float
    result += 1.0f; // add the one back on

    // deal with the exponent
    bias = (1<<(expbits-1)) - 1;
    shift = ((i>>significandbits)&((1LL<<expbits)-1)) - bias;
    while(shift > 0) { result *= 2.0; shift--; }
    while(shift < 0) { result /= 2.0; shift++; }

    // sign it
    result *= (i>>(bits-1))&1? -1.0: 1.0;

    return result;
}
```

I put some handy macros up there at the top for packing and unpacking
32-bit (probably a `float`) and 64-bit (probably a `double`) numbers,
but the `pack754()` function could be called directly and told to encode
`bits`-worth of data (`expbits` of which are reserved for the normalized
number's exponent).

Here's sample usage:

```{.c .numberLines}

#include <stdio.h>
#include <stdint.h> // defines uintN_t types
#include <inttypes.h> // defines PRIx macros

int main(void)
{
    float f = 3.1415926, f2;
    double d = 3.14159265358979323, d2;
    uint32_t fi;
    uint64_t di;

    fi = pack754_32(f);
    f2 = unpack754_32(fi);

    di = pack754_64(d);
    d2 = unpack754_64(di);

    printf("float before : %.7f\n", f);
    printf("float encoded: 0x%08" PRIx32 "\n", fi);
    printf("float after  : %.7f\n\n", f2);

    printf("double before : %.20lf\n", d);
    printf("double encoded: 0x%016" PRIx64 "\n", di);
    printf("double after  : %.20lf\n", d2);

    return 0;
}
```


The above code produces this output:

```
float before : 3.1415925
float encoded: 0x40490FDA
float after  : 3.1415925

double before : 3.14159265358979311600
double encoded: 0x400921FB54442D18
double after  : 3.14159265358979311600
```

Another question you might have is how do you pack `struct`s?
Unfortunately for you, the compiler is free to put padding all over the
place in a `struct`, and that means you can't portably send the whole
thing over the wire in one chunk.  (Aren't you getting sick of hearing
"can't do this", "can't do that"? Sorry! To quote a friend, "Whenever
anything goes wrong, I always blame Microsoft."  This one might not be
Microsoft's fault, admittedly, but my friend's statement is completely
true.)

Back to it: the best way to send the `struct` over the wire is to pack
each field independently and then unpack them into the `struct` when
they arrive on the other side.

That's a lot of work, is what you're thinking. Yes, it is. One thing you
can do is write a helper function to help pack the data for you. It'll
be fun! Really!

In the book [flr[_The Practice of Programming_|tpop]] by Kernighan and
Pike, they implement `printf()`-like functions called `pack()` and
`unpack()` that do exactly this. I'd link to them, but apparently those
functions aren't online with the rest of the source from the book.

(The Practice of Programming is an excellent read. Zeus saves a kitten
every time I recommend it.)

At this point, I'm going to drop a pointer to a [fl[Protocol Buffers
implementation in C|https://github.com/protobuf-c/protobuf-c]] which
I've never used, but looks completely respectable. Python and Perl
programmers will want to check out their language's `pack()` and
`unpack()` functions for accomplishing the same thing. And Java has a
big-ol' Serializable interface that can be used in a similar way.

But if you want to write your own packing utility in C, K&P's trick is
to use variable argument lists to make `printf()`-like functions to
build the packets.  [flx[Here's a version I cooked up|pack2.c]] on my
own based on that which hopefully will be enough to give you an idea of
how such a thing can work.

(This code references the `pack754()` functions, above. The `packi*()`
functions operate like the familiar `htons()` family, except they pack
into a `char` array instead of another integer.)

```{.c .numberLines}
#include <stdio.h>
#include <ctype.h>
#include <stdarg.h>
#include <string.h>

/*
** packi16() -- store a 16-bit int into a char buffer (like htons())
*/ 
void packi16(unsigned char *buf, unsigned int i)
{
    *buf++ = i>>8; *buf++ = i;
}

/*
** packi32() -- store a 32-bit int into a char buffer (like htonl())
*/ 
void packi32(unsigned char *buf, unsigned long int i)
{
    *buf++ = i>>24; *buf++ = i>>16;
    *buf++ = i>>8;  *buf++ = i;
}

/*
** packi64() -- store a 64-bit int into a char buffer (like htonl())
*/ 
void packi64(unsigned char *buf, unsigned long long int i)
{
    *buf++ = i>>56; *buf++ = i>>48;
    *buf++ = i>>40; *buf++ = i>>32;
    *buf++ = i>>24; *buf++ = i>>16;
    *buf++ = i>>8;  *buf++ = i;
}

/*
** unpacki16() -- unpack a 16-bit int from a char buffer (like ntohs())
*/ 
int unpacki16(unsigned char *buf)
{
    unsigned int i2 = ((unsigned int)buf[0]<<8) | buf[1];
    int i;

    // change unsigned numbers to signed
    if (i2 <= 0x7fffu) { i = i2; }
    else { i = -1 - (unsigned int)(0xffffu - i2); }

    return i;
}

/*
** unpacku16() -- unpack a 16-bit unsigned from a char buffer (like ntohs())
*/ 
unsigned int unpacku16(unsigned char *buf)
{
    return ((unsigned int)buf[0]<<8) | buf[1];
}

/*
** unpacki32() -- unpack a 32-bit int from a char buffer (like ntohl())
*/ 
long int unpacki32(unsigned char *buf)
{
    unsigned long int i2 = ((unsigned long int)buf[0]<<24) |
                           ((unsigned long int)buf[1]<<16) |
                           ((unsigned long int)buf[2]<<8)  |
                           buf[3];
    long int i;

    // change unsigned numbers to signed
    if (i2 <= 0x7fffffffu) { i = i2; }
    else { i = -1 - (long int)(0xffffffffu - i2); }

    return i;
}

/*
** unpacku32() -- unpack a 32-bit unsigned from a char buffer (like ntohl())
*/ 
unsigned long int unpacku32(unsigned char *buf)
{
    return ((unsigned long int)buf[0]<<24) |
           ((unsigned long int)buf[1]<<16) |
           ((unsigned long int)buf[2]<<8)  |
           buf[3];
}

/*
** unpacki64() -- unpack a 64-bit int from a char buffer (like ntohl())
*/ 
long long int unpacki64(unsigned char *buf)
{
    unsigned long long int i2 = ((unsigned long long int)buf[0]<<56) |
                                ((unsigned long long int)buf[1]<<48) |
                                ((unsigned long long int)buf[2]<<40) |
                                ((unsigned long long int)buf[3]<<32) |
                                ((unsigned long long int)buf[4]<<24) |
                                ((unsigned long long int)buf[5]<<16) |
                                ((unsigned long long int)buf[6]<<8)  |
                                buf[7];
    long long int i;

    // change unsigned numbers to signed
    if (i2 <= 0x7fffffffffffffffu) { i = i2; }
    else { i = -1 -(long long int)(0xffffffffffffffffu - i2); }

    return i;
}

/*
** unpacku64() -- unpack a 64-bit unsigned from a char buffer (like ntohl())
*/ 
unsigned long long int unpacku64(unsigned char *buf)
{
    return ((unsigned long long int)buf[0]<<56) |
           ((unsigned long long int)buf[1]<<48) |
           ((unsigned long long int)buf[2]<<40) |
           ((unsigned long long int)buf[3]<<32) |
           ((unsigned long long int)buf[4]<<24) |
           ((unsigned long long int)buf[5]<<16) |
           ((unsigned long long int)buf[6]<<8)  |
           buf[7];
}

/*
** pack() -- store data dictated by the format string in the buffer
**
**   bits |signed   unsigned   float   string
**   -----+----------------------------------
**      8 |   c        C         
**     16 |   h        H         f
**     32 |   l        L         d
**     64 |   q        Q         g
**      - |                               s
**
**  (16-bit unsigned length is automatically prepended to strings)
*/ 

unsigned int pack(unsigned char *buf, char *format, ...)
{
    va_list ap;

    signed char c;              // 8-bit
    unsigned char C;

    int h;                      // 16-bit
    unsigned int H;

    long int l;                 // 32-bit
    unsigned long int L;

    long long int q;            // 64-bit
    unsigned long long int Q;

    float f;                    // floats
    double d;
    long double g;
    unsigned long long int fhold;

    char *s;                    // strings
    unsigned int len;

    unsigned int size = 0;

    va_start(ap, format);

    for(; *format != '\0'; format++) {
        switch(*format) {
        case 'c': // 8-bit
            size += 1;
            c = (signed char)va_arg(ap, int); // promoted
            *buf++ = c;
            break;

        case 'C': // 8-bit unsigned
            size += 1;
            C = (unsigned char)va_arg(ap, unsigned int); // promoted
            *buf++ = C;
            break;

        case 'h': // 16-bit
            size += 2;
            h = va_arg(ap, int);
            packi16(buf, h);
            buf += 2;
            break;

        case 'H': // 16-bit unsigned
            size += 2;
            H = va_arg(ap, unsigned int);
            packi16(buf, H);
            buf += 2;
            break;

        case 'l': // 32-bit
            size += 4;
            l = va_arg(ap, long int);
            packi32(buf, l);
            buf += 4;
            break;

        case 'L': // 32-bit unsigned
            size += 4;
            L = va_arg(ap, unsigned long int);
            packi32(buf, L);
            buf += 4;
            break;

        case 'q': // 64-bit
            size += 8;
            q = va_arg(ap, long long int);
            packi64(buf, q);
            buf += 8;
            break;

        case 'Q': // 64-bit unsigned
            size += 8;
            Q = va_arg(ap, unsigned long long int);
            packi64(buf, Q);
            buf += 8;
            break;

        case 'f': // float-16
            size += 2;
            f = (float)va_arg(ap, double); // promoted
            fhold = pack754_16(f); // convert to IEEE 754
            packi16(buf, fhold);
            buf += 2;
            break;

        case 'd': // float-32
            size += 4;
            d = va_arg(ap, double);
            fhold = pack754_32(d); // convert to IEEE 754
            packi32(buf, fhold);
            buf += 4;
            break;

        case 'g': // float-64
            size += 8;
            g = va_arg(ap, long double);
            fhold = pack754_64(g); // convert to IEEE 754
            packi64(buf, fhold);
            buf += 8;
            break;

        case 's': // string
            s = va_arg(ap, char*);
            len = strlen(s);
            size += len + 2;
            packi16(buf, len);
            buf += 2;
            memcpy(buf, s, len);
            buf += len;
            break;
        }
    }

    va_end(ap);

    return size;
}

/*
** unpack() -- unpack data dictated by the format string into the buffer
**
**   bits |signed   unsigned   float   string
**   -----+----------------------------------
**      8 |   c        C         
**     16 |   h        H         f
**     32 |   l        L         d
**     64 |   q        Q         g
**      - |                               s
**
**  (string is extracted based on its stored length, but 's' can be
**  prepended with a max length)
*/
void unpack(unsigned char *buf, char *format, ...)
{
    va_list ap;

    signed char *c;              // 8-bit
    unsigned char *C;

    int *h;                      // 16-bit
    unsigned int *H;

    long int *l;                 // 32-bit
    unsigned long int *L;

    long long int *q;            // 64-bit
    unsigned long long int *Q;

    float *f;                    // floats
    double *d;
    long double *g;
    unsigned long long int fhold;

    char *s;
    unsigned int len, maxstrlen=0, count;

    va_start(ap, format);

    for(; *format != '\0'; format++) {
        switch(*format) {
        case 'c': // 8-bit
            c = va_arg(ap, signed char*);
            if (*buf <= 0x7f) { *c = *buf;} // re-sign
            else { *c = -1 - (unsigned char)(0xffu - *buf); }
            buf++;
            break;

        case 'C': // 8-bit unsigned
            C = va_arg(ap, unsigned char*);
            *C = *buf++;
            break;

        case 'h': // 16-bit
            h = va_arg(ap, int*);
            *h = unpacki16(buf);
            buf += 2;
            break;

        case 'H': // 16-bit unsigned
            H = va_arg(ap, unsigned int*);
            *H = unpacku16(buf);
            buf += 2;
            break;

        case 'l': // 32-bit
            l = va_arg(ap, long int*);
            *l = unpacki32(buf);
            buf += 4;
            break;

        case 'L': // 32-bit unsigned
            L = va_arg(ap, unsigned long int*);
            *L = unpacku32(buf);
            buf += 4;
            break;

        case 'q': // 64-bit
            q = va_arg(ap, long long int*);
            *q = unpacki64(buf);
            buf += 8;
            break;

        case 'Q': // 64-bit unsigned
            Q = va_arg(ap, unsigned long long int*);
            *Q = unpacku64(buf);
            buf += 8;
            break;

        case 'f': // float
            f = va_arg(ap, float*);
            fhold = unpacku16(buf);
            *f = unpack754_16(fhold);
            buf += 2;
            break;

        case 'd': // float-32
            d = va_arg(ap, double*);
            fhold = unpacku32(buf);
            *d = unpack754_32(fhold);
            buf += 4;
            break;

        case 'g': // float-64
            g = va_arg(ap, long double*);
            fhold = unpacku64(buf);
            *g = unpack754_64(fhold);
            buf += 8;
            break;

        case 's': // string
            s = va_arg(ap, char*);
            len = unpacku16(buf);
            buf += 2;
            if (maxstrlen > 0 && len >= maxstrlen) count = maxstrlen - 1;
            else count = len;
            memcpy(s, buf, count);
            s[count] = '\0';
            buf += len;
            break;

        default:
            if (isdigit(*format)) { // track max str len
                maxstrlen = maxstrlen * 10 + (*format-'0');
            }
        }

        if (!isdigit(*format)) maxstrlen = 0;
    }

    va_end(ap);
}
```

And [flx[here is a demonstration program|pack2.c]] of the above code
that packs some data into `buf` and then unpacks it into variables. Note
that when calling `unpack()` with a string argument (format specifier
"`s`"), it's wise to put a maximum length count in front of it to
prevent a buffer overrun, e.g. "`96s`". Be wary when unpacking data you
get over the network---a malicious user might send badly-constructed
packets in an effort to attack your system!

```{.c .numberLines}
#include <stdio.h>

// various bits for floating point types--
// varies for different architectures
typedef float float32_t;
typedef double float64_t;

int main(void)
{
    unsigned char buf[1024];
    int8_t magic;
    int16_t monkeycount;
    int32_t altitude;
    float32_t absurdityfactor;
    char *s = "Great unmitigated Zot! You've found the Runestaff!";
    char s2[96];
    int16_t packetsize, ps2;

    packetsize = pack(buf, "chhlsf", (int8_t)'B', (int16_t)0, (int16_t)37, 
            (int32_t)-5, s, (float32_t)-3490.6677);
    packi16(buf+1, packetsize); // store packet size in packet for kicks

    printf("packet is %" PRId32 " bytes\n", packetsize);

    unpack(buf, "chhl96sf", &magic, &ps2, &monkeycount, &altitude, s2,
        &absurdityfactor);

    printf("'%c' %" PRId32" %" PRId16 " %" PRId32
            " \"%s\" %f\n", magic, ps2, monkeycount,
            altitude, s2, absurdityfactor);

    return 0;
}
```

Whether you roll your own code or use someone else's, it's a good idea
to have a general set of data packing routines for the sake of keeping
bugs in check, rather than packing each bit by hand each time.

When packing the data, what's a good format to use? Excellent question.
Fortunately, [ix[XDR]] [flrfc[RFC 4506|4506]], the External Data
Representation Standard, already defines binary formats for a bunch of
different types, like floating point types, integer types, arrays, raw
data, etc. I suggest conforming to that if you're going to roll the data
yourself. But you're not obligated to. The Packet Police are not right
outside your door. At least, I don't _think_ they are.

In any case, encoding the data somehow or another before you send it is
the right way of doing things!


## Son of Data Encapsulation {#sonofdataencap}

What does it really mean to encapsulate data, anyway? In the simplest
case, it means you'll stick a header on there with either some
identifying information or a packet length, or both.

What should your header look like? Well, it's just some binary data that
represents whatever you feel is necessary to complete your project.

Wow. That's vague.

Okay. For instance, let's say you have a multi-user chat program that
uses `SOCK_STREAM`s. When a user types ("says") something, two pieces of
information need to be transmitted to the server: what was said and who
said it.

So far so good? "What's the problem?" you're asking.

The problem is that the messages can be of varying lengths. One person
named "tom" might say, "Hi", and another person named "Benjamin" might
say, "Hey guys what is up?"

So you `send()` all this stuff to the clients as it comes in. Your
outgoing data stream looks like this:

```
t o m H i B e n j a m i n H e y g u y s w h a t i s u p ?
```

And so on. How does the client know when one message starts and another
stops?  You could, if you wanted, make all messages the same length and
just call the [ixtt[sendall()]] `sendall()` we implemented,
[above](#sendall). But that wastes bandwidth! We don't want to `send()`
1024 bytes just so "tom" can say "Hi".

So we _encapsulate_ the data in a tiny header and packet structure. Both
the client and server know how to pack and unpack (sometimes referred to
as "marshal" and "unmarshal") this data. Don't look now, but we're
starting to define a _protocol_ that describes how a client and server
communicate!

In this case, let's assume the user name is a fixed length of 8
characters, padded with `'\0'`. And then let's assume the data is
variable length, up to a maximum of 128 characters. Let's have a look a
sample packet structure that we might use in this situation:

1. `len` (1 byte, unsigned)---The total length of the packet, counting
    the 8-byte user name and chat data.

2. `name` (8 bytes)---The user's name, NUL-padded if necessary.

3. `chatdata` (_n_-bytes)---The data itself, no more than 128 bytes. The
   length of the packet should be calculated as the length of this data
   plus 8 (the length of the name field, above).

Why did I choose the 8-byte and 128-byte limits for the fields? I pulled
them out of the air, assuming they'd be long enough. Maybe, though, 8
bytes is too restrictive for your needs, and you can have a 30-byte name
field, or whatever.  The choice is up to you.

Using the above packet definition, the first packet would consist of the
following information (in hex and ASCII):

```
   0A     74 6F 6D 00 00 00 00 00      48 69
(length)  T  o  m    (padding)         H  i
```

And the second is similar:

```
   18     42 65 6E 6A 61 6D 69 6E      48 65 79 20 67 75 79 73 20 77 ...
(length)  B  e  n  j  a  m  i  n       H  e  y     g  u  y  s     w  ...
```

(The length is stored in Network Byte Order, of course. In this case,
it's only one byte so it doesn't matter, but generally speaking you'll
want all your binary integers to be stored in Network Byte Order in your
packets.)

When you're sending this data, you should be safe and use a command
similar to [`sendall()`](#sendall), above, so you know all the data is
sent, even if it takes multiple calls to `send()` to get it all out.

Likewise, when you're receiving this data, you need to do a bit of extra
work.  To be safe, you should assume that you might receive a partial
packet (like maybe we receive "`18 42 65 6E 6A`" from Benjamin, above,
but that's all we get in this call to `recv()`). We need to call
`recv()` over and over again until the packet is completely received.

But how? Well, we know the number of bytes we need to receive in total
for the packet to be complete, since that number is tacked on the front
of the packet.  We also know the maximum packet size is 1+8+128, or 137
bytes (because that's how we defined the packet).

There are actually a couple things you can do here. Since you know every
packet starts off with a length, you can call `recv()` just to get the
packet length.  Then once you have that, you can call it again
specifying exactly the remaining length of the packet (possibly
repeatedly to get all the data) until you have the complete packet. The
advantage of this method is that you only need a buffer large enough for
one packet, while the disadvantage is that you need to call `recv()` at
least twice to get all the data.

Another option is just to call `recv()` and say the amount you're
willing to receive is the maximum number of bytes in a packet. Then
whatever you get, stick it onto the back of a buffer, and finally check
to see if the packet is complete. Of course, you might get some of the
next packet, so you'll need to have room for that.

What you can do is declare an array big enough for two packets. This is
your work array where you will reconstruct packets as they arrive.

Every time you `recv()` data, you'll append it into the work buffer and
check to see if the packet is complete. That is, the number of bytes in
the buffer is greater than or equal to the length specified in the
header (+1, because the length in the header doesn't include the byte
for the length itself). If the number of bytes in the buffer is less
than 1, the packet is not complete, obviously. You have to make a
special case for this, though, since the first byte is garbage and you
can't rely on it for the correct packet length.

Once the packet is complete, you can do with it what you will. Use it,
and remove it from your work buffer.

Whew! Are you juggling that in your head yet? Well, here's the second of
the one-two punch: you might have read past the end of one packet and
onto the next in a single `recv()` call. That is, you have a work buffer
with one complete packet, and an incomplete part of the next packet!
Bloody heck. (But this is why you made your work buffer large enough to
hold _two_ packets---in case this happened!)

Since you know the length of the first packet from the header, and
you've been keeping track of the number of bytes in the work buffer, you
can subtract and calculate how many of the bytes in the work buffer
belong to the second (incomplete) packet. When you've handled the first
one, you can clear it out of the work buffer and move the partial second
packet down the to front of the buffer so it's all ready to go for the
next `recv()`.

(Some of you readers will note that actually moving the partial second
packet to the beginning of the work buffer takes time, and the program
can be coded to not require this by using a circular buffer.
Unfortunately for the rest of you, a discussion on circular buffers is
beyond the scope of this article. If you're still curious, grab a data
structures book and go from there.)

I never said it was easy. Ok, I did say it was easy. And it is; you just
need practice and pretty soon it'll come to you naturally. By
[ix[Excalibur]] Excalibur I swear it!


## Broadcast Packets---Hello, World!

So far, this guide has talked about sending data from one host to one
other host. But it is possible, I insist, that you can, with the proper
authority, send data to multiple hosts _at the same time_!

With [ix[UDP]] UDP (only UDP, not TCP) and standard IPv4, this is done
through a mechanism called [ix[broadcast]] _broadcasting_. With IPv6,
broadcasting isn't supported, and you have to resort to the often
superior technique of _multicasting_, which, sadly I won't be discussing
at this time. But enough of the starry-eyed future---we're stuck in the
32-bit present.

But wait! You can't just run off and start broadcasting willy-nilly; You
have to [ixtt[setsockopt()]] set the socket option [ixtt[SO\_BROADCAST]]
`SO_BROADCAST` before you can send a broadcast packet out on the
network. It's like a one of those little plastic covers they put over
the missile launch switch! That's just how much power you hold in your
hands!

But seriously, though, there is a danger to using broadcast packets, and
that is: every system that receives a broadcast packet must undo all the
onion-skin layers of data encapsulation until it finds out what port the
data is destined to. And then it hands the data over or discards it. In
either case, it's a lot of work for each machine that receives the
broadcast packet, and since it is all of them on the local network, that
could be a lot of machines doing a lot of unnecessary work. When the
game Doom first came out, this was a complaint about its network code.

Now, there is more than one way to skin a cat... wait a minute. Is there
really more than one way to skin a cat? What kind of expression is that?
Uh, and likewise, there is more than one way to send a broadcast packet.
So, to get to the meat and potatoes of the whole thing: how do you
specify the destination address for a broadcast message? There are two
common ways:

1. Send the data to a specific subnet's broadcast address. This is the
   subnet's network number with all one-bits set for the host portion of
   the address. For instance, at home my network is `192.168.1.0`, my
   netmask is `255.255.255.0`, so the last byte of the address is my
   host number (because the first three bytes, according to the netmask,
   are the network number). So my broadcast address is `192.168.1.255`.
   Under Unix, the `ifconfig` command will actually give you all this
   data. (If you're curious, the bitwise logic to get your broadcast
   address is `network_number` OR (NOT `netmask`).) You can send this
   type of broadcast packet to remote networks as well as your local
   network, but you run the risk of the packet being dropped by the
   destination's router.  (If they didn't drop it, then some random
   smurf could start flooding their LAN with broadcast traffic.)

2. Send the data to the "global" broadcast address. This is
   [ix[255.255.255.255]] `255.255.255.255`, aka
   [ixtt[INADDR\_BROADCAST]] `INADDR_BROADCAST`. Many machines will
   automatically bitwise AND this with your network number to convert it
   to a network broadcast address, but some won't. It varies. Routers do
   not forward this type of broadcast packet off your local network,
   ironically enough.

So what happens if you try to send data on the broadcast address without
first setting the `SO_BROADCAST` socket option? Well, let's fire up good
old [`talker` and `listener`](#datagram) and see what happens.

```
$ talker 192.168.1.2 foo
sent 3 bytes to 192.168.1.2
$ talker 192.168.1.255 foo
sendto: Permission denied
$ talker 255.255.255.255 foo
sendto: Permission denied
```

Yes, it's not happy at all...because we didn't set the `SO_BROADCAST`
socket option. Do that, and now you can `sendto()` anywhere you want!

In fact, that's the _only difference_ between a UDP application that can
broadcast and one that can't. So let's take the old `talker` application
and add one section that sets the `SO_BROADCAST` socket option. We'll
call this program [flx[`broadcaster.c`|broadcaster.c]]:

```{.c .numberLines}
/*
** broadcaster.c -- a datagram "client" like talker.c, except
**                  this one can broadcast
*/

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>

#define SERVERPORT 4950 // the port users will be connecting to

int main(int argc, char *argv[])
{
    int sockfd;
    struct sockaddr_in their_addr; // connector's address information
    struct hostent *he;
    int numbytes;
    int broadcast = 1;
    //char broadcast = '1'; // if that doesn't work, try this

    if (argc != 3) {
        fprintf(stderr,"usage: broadcaster hostname message\n");
        exit(1);
    }

    if ((he=gethostbyname(argv[1])) == NULL) {  // get the host info
        perror("gethostbyname");
        exit(1);
    }

    if ((sockfd = socket(AF_INET, SOCK_DGRAM, 0)) == -1) {
        perror("socket");
        exit(1);
    }

    // this call is what allows broadcast packets to be sent:
    if (setsockopt(sockfd, SOL_SOCKET, SO_BROADCAST, &broadcast,
        sizeof broadcast) == -1) {
        perror("setsockopt (SO_BROADCAST)");
        exit(1);
    }

    their_addr.sin_family = AF_INET;     // host byte order
    their_addr.sin_port = htons(SERVERPORT); // short, network byte order
    their_addr.sin_addr = *((struct in_addr *)he->h_addr);
    memset(their_addr.sin_zero, '\0', sizeof their_addr.sin_zero);

    if ((numbytes=sendto(sockfd, argv[2], strlen(argv[2]), 0,
             (struct sockaddr *)&their_addr, sizeof their_addr)) == -1) {
        perror("sendto");
        exit(1);
    }

    printf("sent %d bytes to %s\n", numbytes,
        inet_ntoa(their_addr.sin_addr));

    close(sockfd);

    return 0;
}
```

What's different between this and a "normal" UDP client/server
situation?  Nothing! (With the exception of the client being allowed to
send broadcast packets in this case.) As such, go ahead and run the old
UDP [`listener`](#datagram) program in one window, and `broadcaster` in
another. You should be now be able to do all those sends that failed,
above.

```
$ broadcaster 192.168.1.2 foo
sent 3 bytes to 192.168.1.2
$ broadcaster 192.168.1.255 foo
sent 3 bytes to 192.168.1.255
$ broadcaster 255.255.255.255 foo
sent 3 bytes to 255.255.255.255
```

And you should see `listener` responding that it got the packets. (If
`listener` doesn't respond, it could be because it's bound to an IPv6
address. Try changing the `AF_UNSPEC` in `listener.c` to `AF_INET` to
force IPv4.)

Well, that's kind of exciting. But now fire up `listener` on another
machine next to you on the same network so that you have two copies
going, one on each machine, and run `broadcaster` again with your
broadcast address... Hey! Both `listener`s get the packet even though
you only called `sendto()` once! Cool!

If the `listener` gets data you send directly to it, but not data on the
broadcast address, it could be that you have a [ix[firewall]] firewall
on your local machine that is blocking the packets. (Yes, [ix[Pat]] Pat
and [ix[Bapper]] Bapper, thank you for realizing before I did that this
is why my sample code wasn't working. I told you I'd mention you in the
guide, and here you are. So _nyah_.)

Again, be careful with broadcast packets. Since every machine on the LAN
will be forced to deal with the packet whether it `recvfrom()`s it or
not, it can present quite a load to the entire computing network. They
are definitely to be used sparingly and appropriately.

 
# Common Questions

**Where can I get those header files?**

[ix[header files]] If you don't have them on your system already, you
probably don't need them. Check the manual for your particular platform.
If you're building for [ix[Windows]] Windows, you only need to `#include
<winsock.h>`.

**What do I do when `bind()` reports [ix[Address already in use]]
"Address already in use"?**

You have to use [ixtt[setsockopt()]] `setsockopt()` with the
[ixtt[SO\_REUSEADDR]] `SO_REUSEADDR` option on the listening socket.
Check out the [ixtt[bind()]] [section on `bind()`](#bind) and the
[ixtt[select()]] [section on `select()`](#select) for an example.

**How do I get a list of open sockets on the system?**

Use the [ix[netstat]] `netstat`. Check the `man` page for full details,
but you should get some good output just typing:

```
$ netstat
```

The only trick is determining which socket is associated with which
program.  `:-)`

**How can I view the routing table?**

Run the [ix[route]] `route` command (in `/sbin` on most Linuxes) or the
command [ix[netstat]] `netstat -r`.

**How can I run the client and server programs if I only have one
computer?  Don't I need a network to write network programs?**

Fortunately for you, virtually all machines implement a [ix[loopback
device]] loopback network "device" that sits in the kernel and pretends
to be a network card. (This is the interface listed as "`lo`" in the
routing table.)

Pretend you're logged into a machine named [ix[goat]] "`goat`". Run the
client in one window and the server in another. Or start the server in
the background ("`server &`") and run the client in the same window. The
upshot of the loopback device is that you can either `client goat` or
[ix[localhost]] `client localhost` (since "`localhost`" is likely
defined in your `/etc/hosts` file) and you'll have the client talking to
the server without a network!

In short, no changes are necessary to any of the code to make it run on
a single non-networked machine! Huzzah!

**How can I tell if the remote side has closed connection?**

You can tell because `recv()` will return `0`.

**How do I implement a [ixtt[ping]] "ping" utility? What is [ixtt[ICMP]]
ICMP?  Where can I find out more about [ix[raw sockets]] raw sockets and
`SOCK_RAW`?**

All your raw sockets questions will be answered in [W. Richard Stevens'
UNIX Network Programming books](#books). Also, look in the `ping/`
subdirectory in Stevens' UNIX Network Programming source code,
[fl[available online|http://www.unpbook.com/src.html]].

**How do I change or shorten the timeout on a call to `connect()`?**

Instead of giving you exactly the same answer that W. Richard Stevens
would give you, I'll just refer you to [fl[`lib/connect_nonb.c` in the
UNIX Network Programming source code|http://www.unpbook.com/src.html]].

The gist of it is that you make a socket descriptor with `socket()`,
[set it to non-blocking](#blocking), call `connect()`, and if all goes
well `connect()` will return `-1` immediately and `errno` will be set to
`EINPROGRESS`. Then you call [`select()`](#select) with whatever timeout
you want, passing the socket descriptor in both the read and write sets.
If it doesn't timeout, it means the `connect()` call completed. At this
point, you'll have to use `getsockopt()` with the `SO_ERROR` option to
get the return value from the `connect()` call, which should be zero if
there was no error.

Finally, you'll probably want to set the socket back to be blocking
again before you start transferring data over it.

Notice that this has the added benefit of allowing your program to do
something else while it's connecting, too. You could, for example, set
the timeout to something low, like 500 ms, and update an indicator
onscreen each timeout, then call `select()` again. When you've called
`select()` and timed-out, say, 20 times, you'll know it's time to give
up on the connection.

Like I said, check out Stevens' source for a perfectly excellent
example.

**How do I build for Windows?**

First, delete Windows and install Linux or BSD. `};-)`. No, actually,
just see the [section on building for Windows](#windows) in the
introduction.

**How do I build for Solaris/SunOS? I keep getting linker errors when I
try to compile!**

The linker errors happen because Sun boxes don't automatically compile
in the socket libraries. See the [section on building for
Solaris/SunOS](#solaris) in the introduction for an example of how to do
this.

**Why does `select()` keep falling out on a signal?**

Signals tend to cause blocked system calls to return `-1` with `errno`
set to `EINTR`. When you set up a signal handler with
[ixtt[sigaction()]] `sigaction()`, you can set the flag
[ixtt[SA\_RESTART]] `SA_RESTART`, which is supposed to restart the
system call after it was interrupted.

Naturally, this doesn't always work.

My favorite solution to this involves a [ix[goto]] `goto` statement. You
know this irritates your professors to no end, so go for it!

```{.c .numberLines}
select_restart:
if ((err = select(fdmax+1, &readfds, NULL, NULL, NULL)) == -1) {
    if (errno == EINTR) {
        // some signal just interrupted us, so restart
        goto select_restart;
    }
    // handle the real error here:
    perror("select");
} 
```

Sure, you don't _need_ to use `goto` in this case; you can use other
structures to control it. But I think the `goto` statement is actually
cleaner.

**How can I implement a timeout on a call to `recv()`?**

[ix[recv()@\texttt{recv()}!timeout]] Use [ixtt[select()]]
[`select()`](#select)! It allows you to specify a timeout parameter for
socket descriptors that you're looking to read from. Or, you could wrap
the entire functionality in a single function, like this:

```{.c .numberLines}
#include <unistd.h>
#include <sys/time.h>
#include <sys/types.h>
#include <sys/socket.h>

int recvtimeout(int s, char *buf, int len, int timeout)
{
    fd_set fds;
    int n;
    struct timeval tv;

    // set up the file descriptor set
    FD_ZERO(&fds);
    FD_SET(s, &fds);

    // set up the struct timeval for the timeout
    tv.tv_sec = timeout;
    tv.tv_usec = 0;

    // wait until timeout or data received
    n = select(s+1, &fds, NULL, NULL, &tv);
    if (n == 0) return -2; // timeout!
    if (n == -1) return -1; // error

    // data must be here, so do a normal recv()
    return recv(s, buf, len, 0);
}
.
.
.
// Sample call to recvtimeout():
n = recvtimeout(s, buf, sizeof buf, 10); // 10 second timeout

if (n == -1) {
    // error occurred
    perror("recvtimeout");
}
else if (n == -2) {
    // timeout occurred
} else {
    // got some data in buf
}
.
.
. 
```

Notice that [ixtt[recvtimeout()]] `recvtimeout()` returns `-2` in case
of a timeout. Why not return `0`? Well, if you recall, a return value of
`0` on a call to `recv()` means that the remote side closed the
connection. So that return value is already spoken for, and `-1` means
"error", so I chose `-2` as my timeout indicator.

**How do I [ix[encryption]] encrypt or compress the data before sending
it through the socket?**

One easy way to do encryption is to use [ix[SSL]] SSL (secure sockets
layer), but that's beyond the scope of this guide. [ix[OpenSSL]] (Check
out the [fl[OpenSSL project|https://www.openssl.org/]] for more info.)

But assuming you want to plug in or implement your own [ix[compression]]
compressor or encryption system, it's just a matter of thinking of your
data as running through a sequence of steps between both ends. Each step
changes the data in some way.

1. server reads data from file (or wherever)
2. server encrypts/compresses data  (you add this part)
3. server `send()`s encrypted data

Now the other way around:

1. client `recv()`s encrypted data
2. client decrypts/decompresses data  (you add this part)
3. client writes data to file (or wherever)

If you're going to compress and encrypt, just remember to compress
first. `:-)`

Just as long as the client properly undoes what the server does, the
data will be fine in the end no matter how many intermediate steps you
add.

So all you need to do to use my code is to find the place between where
the data is read and the data is sent (using `send()`) over the network,
and stick some code in there that does the encryption.

**What is this "`PF_INET`" I keep seeing? Is it related to `AF_INET`?**

[ixtt[PF\_INET]] [ixtt[AF\_INET]]

Yes, yes it is. See [the section on `socket()`](#socket) for details.

**How can I write a server that accepts shell commands from a client and
executes them?**

For simplicity, lets say the client `connect()`s, `send()`s, and
`close()`s the connection (that is, there are no subsequent system calls
without the client connecting again).

The process the client follows is this:

1. `connect()` to server
2. `send("/sbin/ls > /tmp/client.out")`
3. `close()` the connection

Meanwhile, the server is handling the data and executing it:

1. `accept()` the connection from the client
2. `recv(str)` the command string
3. `close()` the connection
4. `system(str)` to run the command

[ix[security]] _Beware!_  Having the server execute what the client says
is like giving remote shell access and people can do things to your
account when they connect to the server. For instance, in the above
example, what if the client sends "`rm -rf ~`"? It deletes everything in
your account, that's what!

So you get wise, and you prevent the client from using any except for a
couple utilities that you know are safe, like the `foobar` utility:

```{.c}
if (!strncmp(str, "foobar", 6)) {
    sprintf(sysstr, "%s > /tmp/server.out", str);
    system(sysstr);
} 
```

But you're still unsafe, unfortunately: what if the client enters
"`foobar; rm -rf ~`"? The safest thing to do is to write a little
routine that puts an escape ("`\`") character in front of all
non-alphanumeric characters (including spaces, if appropriate) in the
arguments for the command.

As you can see, security is a pretty big issue when the server starts
executing things the client sends.

**I'm sending a slew of data, but when I `recv()`, it only receives 536
bytes or 1460 bytes at a time. But if I run it on my local machine, it
receives all the data at the same time. What's going on?**

You're hitting the [ix[MTU]] MTU---the maximum size the physical medium
can handle. On the local machine, you're using the loopback device which
can handle 8K or more no problem. But on Ethernet, which can only handle
1500 bytes with a header, you hit that limit. Over a modem, with 576 MTU
(again, with header), you hit the even lower limit.

You have to make sure all the data is being sent, first of all. (See the
[`sendall()`](#sendall) function implementation for details.) Once
you're sure of that, then you need to call `recv()` in a loop until all
your data is read.

Read the section [Son of Data Encapsulation](#sonofdataencap) for
details on receiving complete packets of data using multiple calls to
`recv()`.

**I'm on a Windows box and I don't have the `fork()` system call or any
kind of `struct sigaction`. What to do?**

[ixtt[fork()]] If they're anywhere, they'll be in POSIX libraries that
may have shipped with your compiler. Since I don't have a Windows box, I
really can't tell you the answer, but I seem to remember that Microsoft
has a POSIX compatibility layer and that's where `fork()` would be. (And
maybe even `sigaction`.)

Search the help that came with VC++ for "fork" or "POSIX" and see if it
gives you any clues.

If that doesn't work at all, ditch the `fork()`/`sigaction` stuff and
replace it with the Win32 equivalent: [ixtt[CreateProcess()]]
`CreateProcess()`. I don't know how to use `CreateProcess()`---it takes
a bazillion arguments, but it should be covered in the docs that came
with VC++.

**[ix[firewall]] I'm behind a firewall---how do I let people outside the
firewall know my IP address so they can connect to my machine?**

Unfortunately, the purpose of a firewall is to prevent people outside
the firewall from connecting to machines inside the firewall, so
allowing them to do so is basically considered a breach of security.

This isn't to say that all is lost. For one thing, you can still often
`connect()` through the firewall if it's doing some kind of masquerading
or NAT or something like that. Just design your programs so that you're
always the one initiating the connection, and you'll be fine.

[ix[firewall!poking holes in]] If that's not satisfactory, you can ask
your sysadmins to poke a hole in the firewall so that people can connect
to you. The firewall can forward to you either through it's NAT
software, or through a proxy or something like that.

Be aware that a hole in the firewall is nothing to be taken lightly. You
have to make sure you don't give bad people access to the internal
network; if you're a beginner, it's a lot harder to make software secure
than you might imagine.

Don't make your sysadmin mad at me. `;-)`

**[ix[packet sniffer]] [ix[promiscuous mode]] How do I write a packet
sniffer? How do I put my Ethernet interface into promiscuous mode?**

For those not in the know, when a network card is in "promiscuous mode",
it will forward ALL packets to the operating system, not just those that
were addressed to this particular machine. (We're talking Ethernet-layer
addresses here, not IP addresses--but since ethernet is lower-layer than
IP, all IP addresses are effectively forwarded as well. See the section
[Low Level Nonsense and Network Theory](#lowlevel) for more info.)

This is the basis for how a packet sniffer works. It puts the interface
into promiscuous mode, then the OS gets every single packet that goes by
on the wire.  You'll have a socket of some type that you can read this
data from.

Unfortunately, the answer to the question varies depending on the
platform, but if you Google for, for instance, "windows promiscuous
[ixtt[ioctl()]] ioctl" you'll probably get somewhere.  For Linux,
there's what looks like a [fl[useful Stack Overflow
thread|https://stackoverflow.com/questions/21323023/]], as well.

**How can I set a custom [ix[timeout, setting]] timeout value for a TCP
or UDP socket?**

It depends on your system. You might search the net for
[ixtt[SO\_RCVTIMEO]] `SO_RCVTIMEO` and [ixtt[SO\_SNDTIMEO]]
`SO_SNDTIMEO` (for use with [ixtt[setsockopt()]] `setsockopt()`) to see
if your system supports such functionality.

The Linux man page suggests using `alarm()` or `setitimer()` as a
substitute.

**How can I tell which ports are available to use? Is there a list of
"official" port numbers?**

Usually this isn't an issue. If you're writing, say, a web server, then
it's a good idea to use the well-known port 80 for your software. If
you're writing just your own specialized server, then choose a port at
random (but greater than 1023) and give it a try.

If the port is already in use, you'll get an "Address already in use"
error when you try to `bind()`. Choose another port. (It's a good idea
to allow the user of your software to specify an alternate port either
with a config file or a command line switch.)

There is a [fl[list of official port
numbers|https://www.iana.org/assignments/port-numbers]] maintained by
the Internet Assigned Numbers Authority (IANA). Just because something
(over 1023) is in that list doesn't mean you can't use the port. For
instance, Id Software's DOOM uses the same port as "mdqs", whatever that
is. All that matters is that no one else _on the same machine_ is using
that port when you want to use it.


# Man Pages

[ix[man pages]] In the Unix world, there are a lot of manuals. They have
little sections that describe individual functions that you have at your
disposal.

Of course, `manual` would be too much of a thing to type. I mean, no one
in the Unix world, including myself, likes to type that much. Indeed I
could go on and on at great length about how much I prefer to be terse
but instead I shall be brief and not bore you with long-winded diatribes
about how utterly amazingly brief I prefer to be in virtually all
circumstances in their entirety.

_[Applause]_

Thank you. What I am getting at is that these pages are called "man
pages" in the Unix world, and I have included my own personal truncated
variant here for your reading enjoyment. The thing is, many of these
functions are way more general purpose than I'm letting on, but I'm only
going to present the parts that are relevant for Internet Sockets
Programming.

But wait! That's not all that's wrong with my man pages:

* They are incomplete and only show the basics from the guide.
* There are many more man pages than this in the real world.
* They are different than the ones on your system.
* The header files might be different for certain functions on your
  system.
* The function parameters might be different for certain functions on
  your system.

If you want the real information, check your local Unix man pages by
typing `man whatever`, where "whatever" is something that you're
incredibly interested in, such as "`accept`". (I'm sure Microsoft Visual
Studio has something similar in their help section. But "man" is better
because it is one byte more concise than "help". Unix wins again!)

So, if these are so flawed, why even include them at all in the Guide?
Well, there are a few reasons, but the best are that (a) these versions
are geared specifically toward network programming and are easier to
digest than the real ones, and (b) these versions contain examples!

Oh! And speaking of the examples, I don't tend to put in all the error
checking because it really increases the length of the code. But you
should absolutely do error checking pretty much any time you make any of
the system calls unless you're totally 100% sure it's not going to fail,
and you should probably do it even then!

[[pagebreak]]
## `accept()` {#acceptman}

Accept an incoming connection on a listening socket

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int accept(int s, struct sockaddr *addr, socklen_t *addrlen);
```

#### Description 

[ixtt[accept()]] Once you've gone through the trouble of getting a
[ixtt[SOCK\_STREAM]] `SOCK_STREAM` socket and setting it up for incoming
connections with `listen()`, then you call `accept()` to actually get
yourself a new socket descriptor to use for subsequent communication
with the newly connected client.

The old socket that you are using for listening is still there, and will
be used for further `accept()` calls as they come in.

| Parameter | Description                                                   |
|-----------|---------------------------------------------------------------|
| `s`       | The `listen()`ing socket descriptor.                          | 
| `addr`    | This is filled in with the address of the site that's connecting to you.|
| `addrlen` | This is filled in with the `sizeof()` the structure returned in the `addr` parameter. You can safely ignore it if you assume you're getting a [ixtt[struct sockaddr\_in]] `struct sockaddr_in` back, which you know you are, because that's the type you passed in for `addr`.|

`accept()` will normally block, and you can use `select()` to peek on
the listening socket descriptor ahead of time to see if it's "ready to
read". If so, then there's a new connection waiting to be `accept()`ed!
Yay! Alternatively, you could set the [ixtt[O\_NONBLOCK]] `O_NONBLOCK`
flag on the listening socket using [ixtt[fcntl()]] `fcntl()`, and then
it will never block, choosing instead to return `-1` with `errno` set to
[ixtt[EWOULDBLOCK]] `EWOULDBLOCK`.

The socket descriptor returned by `accept()` is a bona fide socket
descriptor, open and connected to the remote host. You have to `close()`
it when you're done with it.

#### Return Value

`accept()` returns the newly connected socket descriptor, or `-1` on
error, with `errno` set appropriately.

#### Example

```{.c .numberLines}
struct sockaddr_storage their_addr;
socklen_t addr_size;
struct addrinfo hints, *res;
int sockfd, new_fd;

// first, load up address structs with getaddrinfo():

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;  // use IPv4 or IPv6, whichever
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_PASSIVE;     // fill in my IP for me

getaddrinfo(NULL, MYPORT, &hints, &res);

// make a socket, bind it, and listen on it:

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);
bind(sockfd, res->ai_addr, res->ai_addrlen);
listen(sockfd, BACKLOG);

// now accept an incoming connection:

addr_size = sizeof their_addr;
new_fd = accept(sockfd, (struct sockaddr *)&their_addr, &addr_size);

// ready to communicate on socket descriptor new_fd!
```

#### See Also

[`socket()`](#socketman), [`getaddrinfo()`](#getaddrinfoman),
[`listen()`](#listenman), [`struct sockaddr_in`](#sockaddr_inman)


[[pagebreak]]
## `bind()` {#bindman}

Associate a socket with an IP address and port number

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int bind(int sockfd, struct sockaddr *my_addr, socklen_t addrlen);
```

#### Description 

[ixtt[bind()]] When a remote machine wants to connect to your server
program, it needs two pieces of information: the [ix[IP address]] IP
address and the [ix[port]] port number. The `bind()` call allows you to
do just that.

First, you call `getaddrinfo()` to load up a `struct sockaddr` with the
destination address and port information. Then you call `socket()` to
get a socket descriptor, and then you pass the socket and address into
`bind()`, and the IP address and port are magically (using actual magic)
bound to the socket!

If you don't know your IP address, or you know you only have one IP
address on the machine, or you don't care which of the machine's IP
addresses is used, you can simply pass the `AI_PASSIVE` flag in the
`hints` parameter to `getaddrinfo()`. What this does is fill in the IP
address part of the `struct sockaddr` with a special value that tells
`bind()` that it should automatically fill in this host's IP address.

What what? What special value is loaded into the `struct sockaddr`'s IP
address to cause it to auto-fill the address with the current host? I'll
tell you, but keep in mind this is only if you're filling out the
`struct sockaddr` by hand; if not, use the results from `getaddrinfo()`,
as per above. In IPv4, the `sin_addr.s_addr` field of the `struct
sockaddr_in` structure is set to `INADDR_ANY`. In IPv6, the `sin6_addr`
field of the `struct sockaddr_in6` structure is assigned into from the
global variable `in6addr_any`. Or, if you're declaring a new `struct
in6_addr`, you can initialize it to `IN6ADDR_ANY_INIT`.

Lastly, the `addrlen` parameter should be set to `sizeof my_addr`.

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
// modern way of doing things with getaddrinfo()

struct addrinfo hints, *res;
int sockfd;

// first, load up address structs with getaddrinfo():

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;  // use IPv4 or IPv6, whichever
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_PASSIVE;     // fill in my IP for me

getaddrinfo(NULL, "3490", &hints, &res);

// make a socket:
// (you should actually walk the "res" linked list and error-check!)

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

// bind it to the port we passed in to getaddrinfo():

bind(sockfd, res->ai_addr, res->ai_addrlen);
```

```{.c .numberLines}
// example of packing a struct by hand, IPv4

struct sockaddr_in myaddr;
int s;

myaddr.sin_family = AF_INET;
myaddr.sin_port = htons(3490);

// you can specify an IP address:
inet_pton(AF_INET, "63.161.169.137", &(myaddr.sin_addr));

// or you can let it automatically select one:
myaddr.sin_addr.s_addr = INADDR_ANY;

s = socket(PF_INET, SOCK_STREAM, 0);
bind(s, (struct sockaddr*)&myaddr, sizeof myaddr);
```

#### See Also

[`getaddrinfo()`](#getaddrinfoman), [`socket()`](#socketman), [`struct
sockaddr_in`](#sockaddr_inman), [`struct in_addr`](#sockaddr_inman)


[[pagebreak]]
## `connect()` {#connectman}

Connect a socket to a server

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int connect(int sockfd, const struct sockaddr *serv_addr,
            socklen_t addrlen);
```

#### Description 

[ixtt[connect()]] Once you've built a socket descriptor with the
`socket()` call, you can `connect()` that socket to a remote server
using the well-named `connect()` system call. All you need to do is pass
it the socket descriptor and the address of the server you're interested
in getting to know better. (Oh, and the length of the address, which is
commonly passed to functions like this.)

Usually this information comes along as the result of a call to
`getaddrinfo()`, but you can fill out your own `struct sockaddr` if you
want to.

If you haven't yet called `bind()` on the socket descriptor, it is
automatically bound to your IP address and a random local port. This is
usually just fine with you if you're not a server, since you really
don't care what your local port is; you only care what the remote port
is so you can put it in the `serv_addr` parameter. You _can_ call
`bind()` if you really want your client socket to be on a specific IP
address and port, but this is pretty rare.

Once the socket is `connect()`ed, you're free to `send()` and `recv()`
data on it to your heart's content.

[ix[connect()@\texttt{connect()}!on datagram sockets]] Special note: if
you `connect()` a `SOCK_DGRAM` UDP socket to a remote host, you can use
`send()` and `recv()` as well as `sendto()` and `recvfrom()`. If you
want.

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
// connect to www.example.com port 80 (http)

struct addrinfo hints, *res;
int sockfd;

// first, load up address structs with getaddrinfo():

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;  // use IPv4 or IPv6, whichever
hints.ai_socktype = SOCK_STREAM;

// we could put "80" instead on "http" on the next line:
getaddrinfo("www.example.com", "http", &hints, &res);

// make a socket:

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

// connect it to the address and port we passed in to getaddrinfo():

connect(sockfd, res->ai_addr, res->ai_addrlen);
```

#### See Also

[`socket()`](#socketman), [`bind()`](#bindman)


[[pagebreak]]
## `close()` {#closeman}

Close a socket descriptor

#### Synopsis

```{.c}
#include <unistd.h>

int close(int s);
```

#### Description 

[ixtt[close()]] After you've finished using the socket for whatever
demented scheme you have concocted and you don't want to `send()` or
`recv()` or, indeed, do _anything else_ at all with the socket, you can
`close()` it, and it'll be freed up, never to be used again.

The remote side can tell if this happens one of two ways. One: if the
remote side calls `recv()`, it will return `0`. Two: if the remote side
calls `send()`, it'll receive a signal [ixtt[SIGPIPE]] `SIGPIPE` and
send() will return `-1` and `errno` will be set to [ixtt[EPIPE]]
`EPIPE`.

[ix[Windows]] **Windows users**: the function you need to use is called
[ixtt[closesocket()]] `closesocket()`, not `close()`. If you try to use
`close()` on a socket descriptor, it's possible Windows will get
angry... And you wouldn't like it when it's angry.

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
s = socket(PF_INET, SOCK_DGRAM, 0);
.
.
.
// a whole lotta stuff...*BRRRONNNN!*
.
.
.
close(s);  // not much to it, really.
```

#### See Also

[`socket()`](#socketman), [`shutdown()`](#shutdownman)


[[pagebreak]]
## `getaddrinfo()`, `freeaddrinfo()`, `gai_strerror()` {#getaddrinfoman}

Get information about a host name and/or service and load up a `struct
sockaddr` with the result.

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>

int getaddrinfo(const char *nodename, const char *servname,
                const struct addrinfo *hints, struct addrinfo **res);

void freeaddrinfo(struct addrinfo *ai);

const char *gai_strerror(int ecode);

struct addrinfo {
  int     ai_flags;          // AI_PASSIVE, AI_CANONNAME, ...
  int     ai_family;         // AF_xxx
  int     ai_socktype;       // SOCK_xxx
  int     ai_protocol;       // 0 (auto) or IPPROTO_TCP, IPPROTO_UDP 

  socklen_t  ai_addrlen;     // length of ai_addr
  char   *ai_canonname;      // canonical name for nodename
  struct sockaddr  *ai_addr; // binary address
  struct addrinfo  *ai_next; // next structure in linked list
};
```

#### Description 

`getaddrinfo()` is an excellent function that will return information on
a particular host name (such as its IP address) and load up a `struct
sockaddr` for you, taking care of the gritty details (like if it's IPv4
or IPv6). It replaces the old functions `gethostbyname()` and
`getservbyname()`.The description, below, contains a lot of information
that might be a little daunting, but actual usage is pretty simple. It
might be worth it to check out the examples first.

The host name that you're interested in goes in the `nodename`
parameter. The address can be either a host name, like
"www.example.com", or an IPv4 or IPv6 address (passed as a string). This
parameter can also be `NULL` if you're using the `AI_PASSIVE` flag (see
below).

The `servname` parameter is basically the port number. It can be a port
number (passed as a string, like "80"), or it can be a service name,
like "http" or "tftp" or "smtp" or "pop", etc. Well-known service names
can be found in the [fl[IANA Port
List|https://www.iana.org/assignments/port-numbers]] or in your
`/etc/services` file.

Lastly, for input parameters, we have `hints`. This is really where you
get to define what the `getaddrinfo()` function is going to do. Zero the
whole structure before use with `memset()`. Let's take a look at the
fields you need to set up before use.

The `ai_flags` can be set to a variety of things, but here are a couple
important ones. (Multiple flags can be specified by bitwise-ORing them
together with the `|` operator). Check your man page for the complete
list of flags.

`AI_CANONNAME` causes the `ai_canonname` of the result to the filled out
with the host's canonical (real) name. `AI_PASSIVE` causes the result's
IP address to be filled out with `INADDR_ANY` (IPv4) or `in6addr_any`
(IPv6); this causes a subsequent call to `bind()` to auto-fill the IP
address of the `struct sockaddr` with the address of the current host.
That's excellent for setting up a server when you don't want to hardcode
the address.

If you do use the `AI_PASSIVE`, flag, then you can pass `NULL` in the
`nodename` (since `bind()` will fill it in for you later).

Continuing on with the input paramters, you'll likely want to set
`ai_family` to `AF_UNSPEC` which tells `getaddrinfo()` to look for both
IPv4 and IPv6 addresses. You can also restrict yourself to one or the
other with `AF_INET` or `AF_INET6`.

Next, the `socktype` field should be set to `SOCK_STREAM` or
`SOCK_DGRAM`, depending on which type of socket you want.

Finally, just leave `ai_protocol` at `0` to automatically choose your
protocol type.

Now, after you get all that stuff in there, you can _finally_ make the
call to `getaddrinfo()`!

Of course, this is where the fun begins. The `res` will now point to a
linked list of `struct addrinfo`s, and you can go through this list to
get all the addresses that match what you passed in with the hints.

Now, it's possible to get some addresses that don't work for one reason
or another, so what the Linux man page does is loops through the list
doing a call to `socket()` and `connect()` (or `bind()` if you're
setting up a server with the `AI_PASSIVE` flag) until it succeeds.

Finally, when you're done with the linked list, you need to call
`freeaddrinfo()` to free up the memory (or it will be leaked, and Some
People will get upset).

#### Return Value

Returns zero on success, or nonzero on error. If it returns nonzero, you
can use the function `gai_strerror()` to get a printable version of the
error code in the return value.

#### Example

```{.c .numberLines}
// code for a client connecting to a server
// namely a stream socket to www.example.com on port 80 (http)
// either IPv4 or IPv6

int sockfd;  
struct addrinfo hints, *servinfo, *p;
int rv;

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC; // use AF_INET6 to force IPv6
hints.ai_socktype = SOCK_STREAM;

if ((rv = getaddrinfo("www.example.com", "http", &hints, &servinfo)) != 0) {
    fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(rv));
    exit(1);
}

// loop through all the results and connect to the first we can
for(p = servinfo; p != NULL; p = p->ai_next) {
    if ((sockfd = socket(p->ai_family, p->ai_socktype,
            p->ai_protocol)) == -1) {
        perror("socket");
        continue;
    }

    if (connect(sockfd, p->ai_addr, p->ai_addrlen) == -1) {
        perror("connect");
        close(sockfd);
        continue;
    }

    break; // if we get here, we must have connected successfully
}

if (p == NULL) {
    // looped off the end of the list with no connection
    fprintf(stderr, "failed to connect\n");
    exit(2);
}

freeaddrinfo(servinfo); // all done with this structure
```

```{.c .numberLines}
// code for a server waiting for connections
// namely a stream socket on port 3490, on this host's IP
// either IPv4 or IPv6.

int sockfd;  
struct addrinfo hints, *servinfo, *p;
int rv;

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC; // use AF_INET6 to force IPv6
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_PASSIVE; // use my IP address

if ((rv = getaddrinfo(NULL, "3490", &hints, &servinfo)) != 0) {
    fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(rv));
    exit(1);
}

// loop through all the results and bind to the first we can
for(p = servinfo; p != NULL; p = p->ai_next) {
    if ((sockfd = socket(p->ai_family, p->ai_socktype,
            p->ai_protocol)) == -1) {
        perror("socket");
        continue;
    }

    if (bind(sockfd, p->ai_addr, p->ai_addrlen) == -1) {
        close(sockfd);
        perror("bind");
        continue;
    }

    break; // if we get here, we must have connected successfully
}

if (p == NULL) {
    // looped off the end of the list with no successful bind
    fprintf(stderr, "failed to bind socket\n");
    exit(2);
}

freeaddrinfo(servinfo); // all done with this structure
```

#### See Also

[`gethostbyname()`](#gethostbynameman), [`getnameinfo()`](#getnameinfoman)


[[pagebreak]]
## `gethostname()` {#gethostnameman}

Returns the name of the system

#### Synopsis

```{.c}
#include <sys/unistd.h>

int gethostname(char *name, size_t len);
```

#### Description 

[ixtt[gethostname()]] Your system has a name. They all do. This is a
slightly more Unixy thing than the rest of the networky stuff we've been
talking about, but it still has its uses.

For instance, you can get your host name, and then call
[ixtt[gethostbyname()]] `gethostbyname()` to find out your [ix[IP
address]] IP address.

The parameter `name` should point to a buffer that will hold the host
name, and `len` is the size of that buffer in bytes. `gethostname()`
won't overwrite the end of the buffer (it might return an error, or it
might just stop writing), and it will `NUL`-terminate the string if
there's room for it in the buffer.

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
char hostname[128];

gethostname(hostname, sizeof hostname);
printf("My hostname: %s\n", hostname);
```

#### See Also

[`gethostbyname()`](#gethostbynameman)


[[pagebreak]]
## `gethostbyname()`, `gethostbyaddr()` {#gethostbynameman}

Get an IP address for a hostname, or vice-versa

#### Synopsis

```{.c}
#include <sys/socket.h>
#include <netdb.h>

struct hostent *gethostbyname(const char *name); // DEPRECATED!
struct hostent *gethostbyaddr(const char *addr, int len, int type);
```

#### Description 

[ixtt[gethostbyname()]] [ixtt[gethostbyaddr()]] _PLEASE NOTE: these two
functions are superseded by `getaddrinfo()` and `getnameinfo()`!_  In
particular, `gethostbyname()` doesn't work well with IPv6.

These functions map back and forth between host names and IP addresses.
For instance, if you have "www.example.com", you can use
`gethostbyname()` to get its IP address and store it in a `struct
in_addr`.

Conversely, if you have a `struct in_addr` or a `struct in6_addr`, you
can use `gethostbyaddr()` to get the hostname back. `gethostbyaddr()`
_is_ IPv6 compatible, but you should use the newer shinier
`getnameinfo()` instead.

(If you have a string containing an IP address in dots-and-numbers
format that you want to look up the hostname of, you'd be better off
using `getaddrinfo()` with the `AI_CANONNAME` flag.)

`gethostbyname()` takes a string like "www.yahoo.com", and returns a
`struct hostent` which contains tons of information, including the
[ix[IP address]] IP address. (Other information is the official host
name, a list of aliases, the address type, the length of the addresses,
and the list of addresses---it's a general-purpose structure that's
pretty easy to use for our specific purposes once you see how.)

`gethostbyaddr()` takes a `struct in_addr` or `struct in6_addr` and
brings you up a corresponding host name (if there is one), so it's sort
of the reverse of `gethostbyname()`. As for parameters, even though
`addr` is a `char*`, you actually want to pass in a pointer to a `struct
in_addr`. `len` should be `sizeof(struct in_addr)`, and `type` should be
`AF_INET`.

So what is this [ixtt[struct hostent]] `struct hostent` that gets
returned? It has a number of fields that contain information about the
host in question.

| Field                | Description                                       |
|----------------------|---------------------------------------------------|
| `char *h_name`       | The real canonical host name.                     |
| `char **h_aliases`   | A list of aliases that can be accessed with arrays---the last element is `NULL` |
| `int h_addrtype`     | The result's address type, which really should be `AF_INET` for our purposes. |
| `int length`         | The length of the addresses in bytes, which is 4 for IP (version 4) addresses. |
| `char **h_addr_list` | A list of IP addresses for this host. Although this is a `char**`, it's really an array of `struct in_addr*`s in disguise. The last array element is `NULL`. |
| `h_addr`             | A commonly defined alias for `h_addr_list[0]`. If you just want any old IP address for this host (yeah, they can have more than one) just use this field. |

#### Return Value

Returns a pointer to a resultant `struct hostent` on success, or `NULL`
on error.

Instead of the normal `perror()` and all that stuff you'd normally use
for error reporting, these functions have parallel results in the
variable `h_errno`, which can be printed using the functions
[ixtt[herror()]] `herror()` or [ixtt[hstrerror()]] `hstrerror()`.  These
work just like the classic `errno`, `perror()`, and `strerror()`
functions you're used to.

#### Example

```{.c .numberLines}
// THIS IS A DEPRECATED METHOD OF GETTING HOST NAMES
// use getaddrinfo() instead!

#include <stdio.h>
#include <errno.h>
#include <netdb.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

int main(int argc, char *argv[])
{
    int i;
    struct hostent *he;
    struct in_addr **addr_list;

    if (argc != 2) {
        fprintf(stderr,"usage: ghbn hostname\n");
        return 1;
    }

    if ((he = gethostbyname(argv[1])) == NULL) {  // get the host info
        herror("gethostbyname");
        return 2;
    }

    // print information about this host:
    printf("Official name is: %s\n", he->h_name);
    printf("    IP addresses: ");
    addr_list = (struct in_addr **)he->h_addr_list;
    for(i = 0; addr_list[i] != NULL; i++) {
        printf("%s ", inet_ntoa(*addr_list[i]));
    }
    printf("\n");

    return 0;
}
```

```{.c .numberLines}
// THIS HAS BEEN SUPERCEDED
// use getnameinfo() instead!

struct hostent *he;
struct in_addr ipv4addr;
struct in6_addr ipv6addr;

inet_pton(AF_INET, "192.0.2.34", &ipv4addr);
he = gethostbyaddr(&ipv4addr, sizeof ipv4addr, AF_INET);
printf("Host name: %s\n", he->h_name);

inet_pton(AF_INET6, "2001:db8:63b3:1::beef", &ipv6addr);
he = gethostbyaddr(&ipv6addr, sizeof ipv6addr, AF_INET6);
printf("Host name: %s\n", he->h_name);
```

#### See Also

[`getaddrinfo()`](#getaddrinfoman), [`getnameinfo()`](#getnameinfoman),
[`gethostname()`](#gethostnameman), [`errno`](#errnoman),
[`perror()`](#perrorman), [`strerror()`](#perrorman), [`struct
in_addr`](#sockaddr_inman)


[[pagebreak]]
## `getnameinfo()` {#getnameinfoman}

Look up the host name and service name information for a given `struct
sockaddr`.

#### Synopsis

```{.c}
#include <sys/socket.h>
#include <netdb.h>

int getnameinfo(const struct sockaddr *sa, socklen_t salen,
                char *host, size_t hostlen,
                char *serv, size_t servlen, int flags);
```

#### Description 

This function is the opposite of `getaddrinfo()`, that is, this function
takes an already loaded `struct sockaddr` and does a name and service
name lookup on it. It replaces the old `gethostbyaddr()` and
`getservbyport()` functions.

You have to pass in a pointer to a `struct sockaddr` (which in actuality
is probably a `struct sockaddr_in` or `struct sockaddr_in6` that you've
cast) in the `sa` parameter, and the length of that `struct` in the
`salen`.

The resultant host name and service name will be written to the area
pointed to by the `host` and `serv` parameters. Of course, you have to
specify the max lengths of these buffers in `hostlen` and `servlen`.

Finally, there are several flags you can pass, but here a a couple good
ones.  `NI_NOFQDN` will cause the `host` to only contain the host name,
not the whole domain name. `NI_NAMEREQD` will cause the function to fail
if the name cannot be found with a DNS lookup (if you don't specify this
flag and the name can't be found, `getnameinfo()` will put a string
version of the IP address in `host` instead).

As always, check your local man pages for the full scoop.

#### Return Value

Returns zero on success, or non-zero on error. If the return value is
non-zero, it can be passed to `gai_strerror()` to get a human-readable
string. See `getaddrinfo` for more information.

#### Example

```{.c .numberLines}
struct sockaddr_in6 sa; // could be IPv4 if you want
char host[1024];
char service[20];

// pretend sa is full of good information about the host and port...

getnameinfo(&sa, sizeof sa, host, sizeof host, service, sizeof service, 0);

printf("   host: %s\n", host);    // e.g. "www.example.com"
printf("service: %s\n", service); // e.g. "http"
```

#### See Also

[`getaddrinfo()`](#getaddrinfoman), [`gethostbyaddr()`](#gethostbynameman)


[[pagebreak]]
## `getpeername()` {#getpeernameman}

Return address info about the remote side of the connection

#### Synopsis

```{.c}
#include <sys/socket.h>

int getpeername(int s, struct sockaddr *addr, socklen_t *len);
```

#### Description 

[ixtt[getpeername()]] Once you have either `accept()`ed a remote
connection, or `connect()`ed to a server, you now have what is known as
a _peer_. Your peer is simply the computer you're connected to,
identified by an [ix[IP address]] IP address and a [ix[port]] port.
So...

`getpeername()` simply returns a `struct sockaddr_in` filled with
information about the machine you're connected to.

Why is it called a "name"? Well, there are a lot of different kinds of
sockets, not just Internet Sockets like we're using in this guide, and
so "name" was a nice generic term that covered all cases. In our case,
though, the peer's "name" is it's IP address and port.

Although the function returns the size of the resultant address in
`len`, you must preload `len` with the size of `addr`.

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
// assume s is a connected socket

socklen_t len;
struct sockaddr_storage addr;
char ipstr[INET6_ADDRSTRLEN];
int port;

len = sizeof addr;
getpeername(s, (struct sockaddr*)&addr, &len);

// deal with both IPv4 and IPv6:
if (addr.ss_family == AF_INET) {
    struct sockaddr_in *s = (struct sockaddr_in *)&addr;
    port = ntohs(s->sin_port);
    inet_ntop(AF_INET, &s->sin_addr, ipstr, sizeof ipstr);
} else { // AF_INET6
    struct sockaddr_in6 *s = (struct sockaddr_in6 *)&addr;
    port = ntohs(s->sin6_port);
    inet_ntop(AF_INET6, &s->sin6_addr, ipstr, sizeof ipstr);
}

printf("Peer IP address: %s\n", ipstr);
printf("Peer port      : %d\n", port);
```

#### See Also

[`gethostname()`](#gethostnameman), [`gethostbyname()`](#gethostbynameman),
[`gethostbyaddr()`](#gethostbynameman)


[[pagebreak]]
## `errno` {#errnoman}

Holds the error code for the last system call

#### Synopsis

```{.c}
#include <errno.h>

int errno;
```

#### Description 

[ixtt[errno]] This is the variable that holds error information for a
lot of system calls. If you'll recall, things like `socket()` and
`listen()` return `-1` on error, and they set the exact value of `errno`
to let you know specifically which error occurred.

The header file `errno.h` lists a bunch of constant symbolic names for
errors, such as `EADDRINUSE`, `EPIPE`, `ECONNREFUSED`, etc. Your local
man pages will tell you what codes can be returned as an error, and you
can use these at run time to handle different errors in different ways.

Or, more commonly, you can call [ixtt[perror()]] `perror()` or
[ixtt[strerror()]] `strerror()` to get a human-readable version of the
error.

One thing to note, for you multithreading enthusiasts, is that on most
systems `errno` is defined in a threadsafe manner. (That is, it's not
actually a global variable, but it behaves just like a global variable
would in a single-threaded environment.)

#### Return Value

The value of the variable is the latest error to have transpired, which
might be the code for "success" if the last action succeeded.

#### Example

```{.c .numberLines}
s = socket(PF_INET, SOCK_STREAM, 0);
if (s == -1) {
    perror("socket"); // or use strerror()
}

tryagain:
if (select(n, &readfds, NULL, NULL) == -1) {
    // an error has occurred!!

    // if we were only interrupted, just restart the select() call:
    if (errno == EINTR) goto tryagain;  // AAAA! goto!!!

    // otherwise it's a more serious error:
    perror("select");
    exit(1);
}
```

#### See Also

[`perror()`](#perrorman), [`strerror()`](#perrorman)


[[pagebreak]]
## `fcntl()` {#fcntlman}

Control socket descriptors

#### Synopsis

```{.c}
#include <sys/unistd.h>
#include <sys/fcntl.h>

int fcntl(int s, int cmd, long arg);
```

#### Description 

[ixtt[fcntl()]] This function is typically used to do file locking and
other file-oriented stuff, but it also has a couple socket-related
functions that you might see or use from time to time.

Parameter `s` is the socket descriptor you wish to operate on, `cmd`
should be set to [ixtt[F\_SETFL]] `F_SETFL`, and `arg` can be one of the
following commands. (Like I said, there's more to `fcntl()` than I'm
letting on here, but I'm trying to stay socket-oriented.)

[ixtt[O\_NONBLOCK]] [ixtt[O\_ASYNC]] [ixtt[SIGIO]]

| `cmd`        | Description                                                |
|--------------|------------------------------------------------------------|
| `O_NONBLOCK` | Set the socket to be non-blocking. See the section on [blocking](#blocking) for more details.|
| `O_ASYNC`    | Set the socket to do asynchronous I/O. When data is ready to be `recv()`'d on the socket, the signal `SIGIO` will be raised. This is rare to see, and beyond the scope of the guide. And I think it's only available on certain systems.|

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

Different uses of the `fcntl()` system call actually have different
return values, but I haven't covered them here because they're not
socket-related. See your local `fcntl()` man page for more information.

#### Example

```{.c .numberLines}
int s = socket(PF_INET, SOCK_STREAM, 0);

fcntl(s, F_SETFL, O_NONBLOCK);  // set to non-blocking
fcntl(s, F_SETFL, O_ASYNC);     // set to asynchronous I/O
```

#### See Also

[Blocking](#blocking), [`send()`](#sendman)


[[pagebreak]]
## `htons()`, `htonl()`, `ntohs()`, `ntohl()` {#htonsman}

Convert multi-byte integer types from host byte order to network byte order

#### Synopsis

```{.c}
#include <netinet/in.h>

uint32_t htonl(uint32_t hostlong);
uint16_t htons(uint16_t hostshort);
uint32_t ntohl(uint32_t netlong);
uint16_t ntohs(uint16_t netshort);
```

#### Description 

[ixtt[htons()]] [ixtt[htonl()]] [ixtt[ntohs()]] [ixtt[ntohl()]] Just to
make you really unhappy, different computers use different byte
orderings internally for their multibyte integers (i.e. any integer
that's larger than a `char`).  The upshot of this is that if you
`send()` a two-byte `short int` from an Intel box to a Mac (before they
became Intel boxes, too, I mean), what one computer thinks is the number
`1`, the other will think is the number `256`, and vice-versa.

[ix[byte ordering]] The way to get around this problem is for everyone
to put aside their differences and agree that Motorola and IBM had it
right, and Intel did it the weird way, and so we all convert our byte
orderings to "big-endian" before sending them out. Since Intel is a
"little-endian" machine, it's far more politically correct to call our
preferred byte ordering "Network Byte Order". So these functions convert
from your native byte order to network byte order and back again.

(This means on Intel these functions swap all the bytes around, and on
PowerPC they do nothing because the bytes are already in Network Byte
Order. But you should always use them in your code anyway, since someone
might want to build it on an Intel machine and still have things work
properly.)

Note that the types involved are 32-bit (4 byte, probably `int`) and
16-bit (2 byte, very likely `short`) numbers. 64-bit machines might have
a `htonll()` for 64-bit `int`s, but I've not seen it. You'll just have
to write your own.

Anyway, the way these functions work is that you first decide if you're
converting _from_ host (your machine's) byte order or from network byte
order.  If "host", the the first letter of the function you're going to
call is "h".  Otherwise it's "n" for "network". The middle of the
function name is always "to" because you're converting from one "to"
another, and the penultimate letter shows what you're converting _to_.
The last letter is the size of the data, "s" for short, or "l" for long.
Thus:

| Function  | Description                   |
|-----------|-------------------------------|
| `htons()` | `h`ost `to` `n`etwork `s`hort |
| `htonl()` | `h`ost `to` `n`etwork `l`ong  |
| `ntohs()` | `n`etwork `to` `h`ost `s`hort |
| `ntohl()` | `n`etwork `to` `h`ost `l`ong  |

#### Return Value

Each function returns the converted value.

#### Example

```{.c .numberLines}
uint32_t some_long = 10;
uint16_t some_short = 20;

uint32_t network_byte_order;

// convert and send
network_byte_order = htonl(some_long);
send(s, &network_byte_order, sizeof(uint32_t), 0);

some_short == ntohs(htons(some_short)); // this expression is true
```


[[pagebreak]]
## `inet_ntoa()`, `inet_aton()`, `inet_addr` {#inet_ntoaman}

Convert IP addresses from a dots-and-number string to a `struct in_addr` and
back

#### Synopsis

```{.c}
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

// ALL THESE ARE DEPRECATED! Use inet_pton()  or inet_ntop() instead!!

char *inet_ntoa(struct in_addr in);
int inet_aton(const char *cp, struct in_addr *inp);
in_addr_t inet_addr(const char *cp);
```

#### Description 

_These functions are deprecated because they don't handle IPv6! Use
`inet_ntop()` or `inet_pton()` instead! They are included here because
they can still be found in the wild._

[ixtt[inet\_ntoa()]] [ixtt[inet\_aton()]] [ixtt[inet\_addr()]] All of
these functions convert from a `struct in_addr` (part of your `struct
sockaddr_in`, most likely) to a string in dots-and-numbers format (e.g.
"192.168.5.10") and vice-versa. If you have an IP address passed on the
command line or something, this is the easiest way to get a `struct
in_addr` to `connect()` to, or whatever. If you need more power, try
some of the DNS functions like `gethostbyname()` or attempt a _coup
d'État_ in your local country.

The function `inet_ntoa()` converts a network address in a `struct
in_addr` to a dots-and-numbers format string. The "n" in "ntoa" stands
for network, and the "a" stands for ASCII for historical reasons (so
it's "Network To ASCII"---the "toa" suffix has an analogous friend in
the C library called `atoi()` which converts an ASCII string to an
integer).

The function `inet_aton()` is the opposite, converting from a
dots-and-numbers string into a `in_addr_t` (which is the type of the
field `s_addr` in your `struct in_addr`).

Finally, the function `inet_addr()` is an older function that does
basically the same thing as `inet_aton()`. It's theoretically
deprecated, but you'll see it a lot and the police won't come get you if
you use it.

#### Return Value

`inet_aton()` returns non-zero if the address is a valid one, and it
returns zero if the address is invalid.

`inet_ntoa()` returns the dots-and-numbers string in a static buffer
that is overwritten with each call to the function.

`inet_addr()` returns the address as an `in_addr_t`, or `-1` if there's
an error. (That is the same result as if you tried to convert the string
[ix[255.255.255.255]] "`255.255.255.255`", which is a valid IP address.
This is why `inet_aton()` is better.)

#### Example

```{.c .numberLines}
struct sockaddr_in antelope;
char *some_addr;

inet_aton("10.0.0.1", &antelope.sin_addr); // store IP in antelope

some_addr = inet_ntoa(antelope.sin_addr); // return the IP
printf("%s\n", some_addr); // prints "10.0.0.1"

// and this call is the same as the inet_aton() call, above:
antelope.sin_addr.s_addr = inet_addr("10.0.0.1");
```

#### See Also

[`inet_ntop()`](#inet_ntopman), [`inet_pton()`](#inet_ntopman),
[`gethostbyname()`](#gethostbynameman), [`gethostbyaddr()`](#gethostbynameman)


[[pagebreak]]
## `inet_ntop()`, `inet_pton()` {#inet_ntopman}

Convert IP addresses to human-readable form and back.

#### Synopsis

```{.c}
#include <arpa/inet.h>

const char *inet_ntop(int af, const void *src,
                      char *dst, socklen_t size);

int inet_pton(int af, const char *src, void *dst);
```

#### Description 

These functions are for dealing with human-readable IP addresses and
converting them to their binary representation for use with various
functions and system calls. The "n" stands for "network", and "p" for
"presentation". Or "text presentation". But you can think of it as
"printable". "ntop" is "network to printable". See?

Sometimes you don't want to look at a pile of binary numbers when
looking at an IP address. You want it in a nice printable form, like
`192.0.2.180`, or `2001:db8:8714:3a90::12`. In that case, `inet_ntop()`
is for you.

`inet_ntop()` takes the address family in the `af` parameter (either
`AF_INET` or `AF_INET6`). The `src` parameter should be a pointer to
either a `struct in_addr` or `struct in6_addr` containing the address
you wish to convert to a string. Finally `dst` and `size` are the
pointer to the destination string and the maximum length of that string.

What should the maximum length of the `dst` string be? What is the
maximum length for IPv4 and IPv6 addresses? Fortunately there are a
couple of macros to help you out. The maximum lengths are:
`INET_ADDRSTRLEN` and `INET6_ADDRSTRLEN`.

Other times, you might have a string containing an IP address in
readable form, and you want to pack it into a `struct sockaddr_in` or a
`struct sockaddr_in6`.  In that case, the opposite funcion `inet_pton()`
is what you're after.

`inet_pton()` also takes an address family (either `AF_INET` or
`AF_INET6`) in the `af` parameter. The `src` parameter is a pointer to a
string containing the IP address in printable form. Lastly the `dst`
parameter points to where the result should be stored, which is probably
a `struct in_addr` or `struct in6_addr`.

These functions don't do DNS lookups---you'll need `getaddrinfo()` for
that.

#### Return Value

`inet_ntop()` returns the `dst` parameter on success, or `NULL` on
failure (and `errno` is set).

`inet_pton()` returns `1` on success. It returns `-1` if there was an
error (`errno` is set), or `0` if the input isn't a valid IP address.

#### Example

```{.c .numberLines}
// IPv4 demo of inet_ntop() and inet_pton()

struct sockaddr_in sa;
char str[INET_ADDRSTRLEN];

// store this IP address in sa:
inet_pton(AF_INET, "192.0.2.33", &(sa.sin_addr));

// now get it back and print it
inet_ntop(AF_INET, &(sa.sin_addr), str, INET_ADDRSTRLEN);

printf("%s\n", str); // prints "192.0.2.33"
```

```{.c .numberLines}
// IPv6 demo of inet_ntop() and inet_pton()
// (basically the same except with a bunch of 6s thrown around)

struct sockaddr_in6 sa;
char str[INET6_ADDRSTRLEN];

// store this IP address in sa:
inet_pton(AF_INET6, "2001:db8:8714:3a90::12", &(sa.sin6_addr));

// now get it back and print it
inet_ntop(AF_INET6, &(sa.sin6_addr), str, INET6_ADDRSTRLEN);

printf("%s\n", str); // prints "2001:db8:8714:3a90::12"
```

```{.c .numberLines}
// Helper function you can use:

//Convert a struct sockaddr address to a string, IPv4 and IPv6:

char *get_ip_str(const struct sockaddr *sa, char *s, size_t maxlen)
{
    switch(sa->sa_family) {
        case AF_INET:
            inet_ntop(AF_INET, &(((struct sockaddr_in *)sa)->sin_addr),
                    s, maxlen);
            break;

        case AF_INET6:
            inet_ntop(AF_INET6, &(((struct sockaddr_in6 *)sa)->sin6_addr),
                    s, maxlen);
            break;

        default:
            strncpy(s, "Unknown AF", maxlen);
            return NULL;
    }

    return s;
}
```

#### See Also

[`getaddrinfo()`](#getaddrinfoman)



[[pagebreak]]
## `listen()` {#listenman}

Tell a socket to listen for incoming connections

#### Synopsis

```{.c}
#include <sys/socket.h>

int listen(int s, int backlog);
```

#### Description 

[ixtt[listen()]] You can take your socket descriptor (made with the
`socket()` system call) and tell it to listen for incoming connections.
This is what differentiates the servers from the clients, guys.

The `backlog` parameter can mean a couple different things depending on
the system you on, but loosely it is how many pending connections you
can have before the kernel starts rejecting new ones. So as the new
connections come in, you should be quick to `accept()` them so that the
backlog doesn't fill. Try setting it to 10 or so, and if your clients
start getting "Connection refused" under heavy load, set it higher.

Before calling `listen()`, your server should call `bind()` to attach
itself to a specific port number. That port number (on the server's IP
address) will be the one that clients connect to.

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
struct addrinfo hints, *res;
int sockfd;

// first, load up address structs with getaddrinfo():

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;  // use IPv4 or IPv6, whichever
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_PASSIVE;     // fill in my IP for me

getaddrinfo(NULL, "3490", &hints, &res);

// make a socket:

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

// bind it to the port we passed in to getaddrinfo():

bind(sockfd, res->ai_addr, res->ai_addrlen);

listen(sockfd, 10); // set s up to be a server (listening) socket

// then have an accept() loop down here somewhere
```

#### See Also

[`accept()`](#acceptman), [`bind()`](#bindman), [`socket()`](#socketman)


[[pagebreak]]
## `perror()`, `strerror()` {#perrorman}

Print an error as a human-readable string

#### Synopsis

```{.c}
#include <stdio.h>
#include <string.h>   // for strerror()

void perror(const char *s);
char *strerror(int errnum);
```

#### Description 

[ixtt[perror()]] [ixtt[strerror()]] Since so many functions return `-1`
on error and set the value of the variable [ixtt[errno]] `errno` to be
some number, it would sure be nice if you could easily print that in a
form that made sense to you.

Mercifully, `perror()` does that. If you want more description to be
printed before the error, you can point the parameter `s` to it (or you
can leave `s` as `NULL` and nothing additional will be printed).

In a nutshell, this function takes `errno` values, like `ECONNRESET`,
and prints them nicely, like "Connection reset by peer."

The function `strerror()` is very similar to `perror()`, except it
returns a pointer to the error message string for a given value (you
usually pass in the variable `errno`).

#### Return Value

`strerror()` returns a pointer to the error message string.

#### Example

```{.c .numberLines}
int s;

s = socket(PF_INET, SOCK_STREAM, 0);

if (s == -1) { // some error has occurred
    // prints "socket error: " + the error message:
    perror("socket error");
}

// similarly:
if (listen(s, 10) == -1) {
    // this prints "an error: " + the error message from errno:
    printf("an error: %s\n", strerror(errno));
}
```

#### See Also

[`errno`](#errnoman)


[[pagebreak]]
## `poll()` {#pollman}

Test for events on multiple sockets simultaneously

#### Synopsis

```{.c}
#include <sys/poll.h>

int poll(struct pollfd *ufds, unsigned int nfds, int timeout);
```

#### Description 

[ixtt[poll()]] This function is very similar to `select()` in that they
both watch sets of file descriptors for events, such as incoming data
ready to `recv()`, socket ready to `send()` data to, out-of-band data
ready to `recv()`, errors, etc.

The basic idea is that you pass an array of `nfds` `struct pollfd`s in
`ufds`, along with a timeout in milliseconds (1000 milliseconds in a
second). The `timeout` can be negative if you want to wait forever. If
no event happens on any of the socket descriptors by the timeout,
`poll()` will return.

Each element in the array of `struct pollfd`s represents one socket
descriptor, and contains the following fields:

[ixtt[struct pollfd]]

```{.c}
struct pollfd {
    int fd;         // the socket descriptor
    short events;   // bitmap of events we're interested in
    short revents;  // when poll() returns, bitmap of events that occurred
};
```

Before calling `poll()`, load `fd` with the socket descriptor (if you
set `fd` to a negative number, this `struct pollfd` is ignored and its
`revents` field is set to zero) and then construct the `events` field by
bitwise-ORing the following macros:

| Macro     | Description                                                  |
|-----------|--------------------------------------------------------------|
| `POLLIN`  | Alert me when data is ready to `recv()` on this socket.      |
| `POLLOUT` | Alert me when I can `send()` data to this socket without blocking.|
| `POLLPRI` | Alert me when out-of-band data is ready to `recv()` on this socket.|

Once the `poll()` call returns, the `revents` field will be constructed
as a bitwise-OR of the above fields, telling you which descriptors
actually have had that event occur. Additionally, these other fields
might be present:

| Macro      | Description                                                 |
|------------|-------------------------------------------------------------|
| `POLLERR`  | An error has occurred on this socket.                       |
| `POLLHUP`  | The remote side of the connection hung up.                  |
| `POLLNVAL` | Something was wrong with the socket descriptor `fd`---maybe it's uninitialized?|

#### Return Value

Returns the number of elements in the `ufds` array that have had event
occur on them; this can be zero if the timeout occurred. Also returns
`-1` on error (and `errno` will be set accordingly).

#### Example

```{.c .numberLines}
int s1, s2;
int rv;
char buf1[256], buf2[256];
struct pollfd ufds[2];

s1 = socket(PF_INET, SOCK_STREAM, 0);
s2 = socket(PF_INET, SOCK_STREAM, 0);

// pretend we've connected both to a server at this point
//connect(s1, ...)...
//connect(s2, ...)...

// set up the array of file descriptors.
//
// in this example, we want to know when there's normal or out-of-band
// data ready to be recv()'d...

ufds[0].fd = s1;
ufds[0].events = POLLIN | POLLPRI; // check for normal or out-of-band

ufds[1].fd = s2;
ufds[1].events = POLLIN; // check for just normal data

// wait for events on the sockets, 3.5 second timeout
rv = poll(ufds, 2, 3500);

if (rv == -1) {
    perror("poll"); // error occurred in poll()
} else if (rv == 0) {
    printf("Timeout occurred! No data after 3.5 seconds.\n");
} else {
    // check for events on s1:
    if (ufds[0].revents & POLLIN) {
        recv(s1, buf1, sizeof buf1, 0); // receive normal data
    }
    if (ufds[0].revents & POLLPRI) {
        recv(s1, buf1, sizeof buf1, MSG_OOB); // out-of-band data
    }

    // check for events on s2:
    if (ufds[1].revents & POLLIN) {
        recv(s1, buf2, sizeof buf2, 0);
    }
}
```

#### See Also

[`select()`](#selectman)


[[pagebreak]]
## `recv()`, `recvfrom()` {#recvman}

Receive data on a socket

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

ssize_t recv(int s, void *buf, size_t len, int flags);
ssize_t recvfrom(int s, void *buf, size_t len, int flags,
                 struct sockaddr *from, socklen_t *fromlen);
```

#### Description 

[ixtt[recv()]] [ixtt[recvfrom()]] Once you have a socket up and
connected, you can read incoming data from the remote side using the
`recv()` (for TCP [ixtt[SOCK\_STREAM]] `SOCK_STREAM` sockets) and
`recvfrom()` (for UDP [ixtt[SOCK\_DGRAM]] `SOCK_DGRAM` sockets).

Both functions take the socket descriptor `s`, a pointer to the buffer
`buf`, the size (in bytes) of the buffer `len`, and a set of `flags`
that control how the functions work.

Additionally, the `recvfrom()` takes a [ixtt[struct sockaddr]] `struct
sockaddr*`, `from` that will tell you where the data came from, and will
fill in `fromlen` with the size of `struct sockaddr`. (You must also
initialize `fromlen` to be the size of `from` or `struct sockaddr`.)

So what wondrous flags can you pass into this function? Here are some of
them, but you should check your local man pages for more information and
what is actually supported on your system. You bitwise-or these
together, or just set `flags` to `0` if you want it to be a regular
vanilla `recv()`.

[ixtt[MSG\_OOB]] [ix[out-of-band data]] [ixtt[MSG\_PEEK]]
[ixtt[MSG\_WAITALL]]

| Macro         | Description                                              |
|---------------|----------------------------------------------------------|
| `MSG_OOB`     | Receive Out of Band data. This is how to get data that has been sent to you with the `MSG_OOB` flag in `send()`. As the receiving side, you will have had signal [ixtt[SIGURG]] `SIGURG` raised telling you there is urgent data. In your handler for that signal, you could call `recv()` with this `MSG_OOB` flag.|
| `MSG_PEEK`    | If you want to call `recv()` "just for pretend", you can call it with this flag. This will tell you what's waiting in the buffer for when you call `recv()` "for real" (i.e. _without_ the `MSG_PEEK` flag. It's like a sneak preview into the next `recv()` call.| 
| `MSG_WAITALL` | Tell `recv()` to not return until all the data you specified in the `len` parameter. It will ignore your wishes in extreme circumstances, however, like if a signal interrupts the call or if some error occurs or if the remote side closes the connection, etc. Don't be mad with it.| 

When you call `recv()`, it will block until there is some data to read.
If you want to not block, set the socket to non-blocking or check with
`select()` or `poll()` to see if there is incoming data before calling
`recv()` or `recvfrom()`.

#### Return Value

Returns the number of bytes actually received (which might be less than
you requested in the `len` parameter), or `-1` on error (and `errno`
will be set accordingly).

If the remote side has closed the connection, `recv()` will return `0`.
This is the normal method for determining if the remote side has closed
the connection. Normality is good, rebel!

#### Example

```{.c .numberLines}
// stream sockets and recv()

struct addrinfo hints, *res;
int sockfd;
char buf[512];
int byte_count;

// get host info, make socket, and connect it
memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;  // use IPv4 or IPv6, whichever
hints.ai_socktype = SOCK_STREAM;
getaddrinfo("www.example.com", "3490", &hints, &res);
sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);
connect(sockfd, res->ai_addr, res->ai_addrlen);

// all right! now that we're connected, we can receive some data!
byte_count = recv(sockfd, buf, sizeof buf, 0);
printf("recv()'d %d bytes of data in buf\n", byte_count);
```

```{.c .numberLines}
// datagram sockets and recvfrom()

struct addrinfo hints, *res;
int sockfd;
int byte_count;
socklen_t fromlen;
struct sockaddr_storage addr;
char buf[512];
char ipstr[INET6_ADDRSTRLEN];

// get host info, make socket, bind it to port 4950
memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;  // use IPv4 or IPv6, whichever
hints.ai_socktype = SOCK_DGRAM;
hints.ai_flags = AI_PASSIVE;
getaddrinfo(NULL, "4950", &hints, &res);
sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);
bind(sockfd, res->ai_addr, res->ai_addrlen);

// no need to accept(), just recvfrom():

fromlen = sizeof addr;
byte_count = recvfrom(sockfd, buf, sizeof buf, 0, &addr, &fromlen);

printf("recv()'d %d bytes of data in buf\n", byte_count);
printf("from IP address %s\n",
    inet_ntop(addr.ss_family,
        addr.ss_family == AF_INET?
            ((struct sockadd_in *)&addr)->sin_addr:
            ((struct sockadd_in6 *)&addr)->sin6_addr,
        ipstr, sizeof ipstr);
```

#### See Also

[`send()`](#sendman), [`sendto()`](#sendman), [`select()`](#selectman),
[`poll()`](#pollman), [Blocking](#blocking)


[[pagebreak]]
## `select()` {#selectman}

Check if sockets descriptors are ready to read/write

#### Synopsis

```{.c}
#include <sys/select.h>

int select(int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds,
           struct timeval *timeout);

FD_SET(int fd, fd_set *set);
FD_CLR(int fd, fd_set *set);
FD_ISSET(int fd, fd_set *set);
FD_ZERO(fd_set *set);
```

#### Description 

[ixtt[select()]] The `select()` function gives you a way to
simultaneously check multiple sockets to see if they have data waiting
to be `recv()`d, or if you can `send()` data to them without blocking,
or if some exception has occurred.

You populate your sets of socket descriptors using the macros, like
`FD_SET()`, above. Once you have the set, you pass it into the function
as one of the following parameters: `readfds` if you want to know when
any of the sockets in the set is ready to `recv()` data, `writefds` if
any of the sockets is ready to `send()` data to, and/or `exceptfds` if
you need to know when an exception (error) occurs on any of the sockets.
Any or all of these parameters can be `NULL` if you're not interested in
those types of events. After `select()` returns, the values in the sets
will be changed to show which are ready for reading or writing, and
which have exceptions.

The first parameter, `n` is the highest-numbered socket descriptor
(they're just `int`s, remember?) plus one.

Lastly, the [ixtt[struct timeval]] `struct timeval`, `timeout`, at the
end---this lets you tell `select()` how long to check these sets for.
It'll return after the timeout, or when an event occurs, whichever is
first. The `struct timeval` has two fields: `tv_sec` is the number of
seconds, to which is added `tv_usec`, the number of microseconds
(1,000,000 microseconds in a second).

The helper macros do the following:

| Macro                            | Description                           |
|----------------------------------|---------------------------------------|
| [ixtt[FD\_SET()]] `FD_SET(int fd, fd_set *set);` | Add `fd` to the `set`.|
| [ixtt[FD\_CLR()]] `FD_CLR(int fd, fd_set *set);` | Remove `fd` from the `set`.|
| [ixtt[FD\_ISSET()]] `FD_ISSET(int fd, fd_set *set);` | Return true if `fd` is in the `set`.|
| [ixtt[FD\_ZERO()]] `FD_ZERO(fd_set *set);` | Clear all entries from the `set`.|

Note for Linux users: Linux's `select()` can return "ready-to-read" and
then not actually be ready to read, thus causing the subsequent `read()`
call to block.  You can work around this bug by setting
[ixtt[O\_NONBLOCK]] `O_NONBLOCK` flag on the receiving socket so it
errors with `EWOULDBLOCK`, then ignoring this error if it occurs. See
the [`fcntl()` reference page](#fcntlman) for more info on setting a
socket to non-blocking.

#### Return Value

Returns the number of descriptors in the set on success, `0` if the
timeout was reached, or `-1` on error (and `errno` will be set
accordingly). Also, the sets are modified to show which sockets are
ready.

#### Example

```{.c .numberLines}
int s1, s2, n;
fd_set readfds;
struct timeval tv;
char buf1[256], buf2[256];

// pretend we've connected both to a server at this point
//s1 = socket(...);
//s2 = socket(...);
//connect(s1, ...)...
//connect(s2, ...)...

// clear the set ahead of time
FD_ZERO(&readfds);

// add our descriptors to the set
FD_SET(s1, &readfds);
FD_SET(s2, &readfds);

// since we got s2 second, it's the "greater", so we use that for
// the n param in select()
n = s2 + 1;

// wait until either socket has data ready to be recv()d (timeout 10.5 secs)
tv.tv_sec = 10;
tv.tv_usec = 500000;
rv = select(n, &readfds, NULL, NULL, &tv);

if (rv == -1) {
    perror("select"); // error occurred in select()
} else if (rv == 0) {
    printf("Timeout occurred! No data after 10.5 seconds.\n");
} else {
    // one or both of the descriptors have data
    if (FD_ISSET(s1, &readfds)) {
        recv(s1, buf1, sizeof buf1, 0);
    }
    if (FD_ISSET(s2, &readfds)) {
        recv(s2, buf2, sizeof buf2, 0);
    }
}
```

#### See Also

[`poll()`](#pollman)


[[pagebreak]]
## `setsockopt()`, `getsockopt()` {#setsockoptman}

Set various options for a socket

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int getsockopt(int s, int level, int optname, void *optval,
               socklen_t *optlen);
int setsockopt(int s, int level, int optname, const void *optval,
               socklen_t optlen);
```

#### Description 

[ixtt[getsockopt()]] [ixtt[setsockopt()]] Sockets are fairly
configurable beasts. In fact, they are so configurable, I'm not even
going to cover it all here. It's probably system-dependent anyway. But I
will talk about the basics.

Obviously, these functions get and set certain options on a socket. On a
Linux box, all the socket information is in the man page for socket in
section 7.  (Type: "`man 7 socket`" to get all these goodies.)

As for parameters, `s` is the socket you're talking about, level should
be set to [ixtt[SOL\_SOCKET]] `SOL_SOCKET`. Then you set the `optname`
to the name you're interested in. Again, see your man page for all the
options, but here are some of the most fun ones:

| `optname`         | Description                                          |
|-------------------|------------------------------------------------------|
| [ixtt[SO\_BINDTODEVICE]] `SO_BINDTODEVICE` | Bind this socket to a symbolic device name like `eth0` instead of using `bind()` to bind it to an IP address. Type the command `ifconfig` under Unix to see the device names.|
| [ixtt[SO\_REUSEADDR]] `SO_REUSEADDR` | Allows other sockets to `bind()` to this port, unless there is an active listening socket bound to the port already. This enables you to get around those "Address already in use" error messages when you try to restart your server after a crash.|
| [ixtt[SO\_BROADCAST]] `SOCK_DGRAM` | Allows UDP datagram [ixtt[SOCK\_DGRAM]] (`SOCK_DGRAM`) sockets to send and receive packets sent to and from the broadcast address. Does nothing---_NOTHING!!_---to TCP stream sockets! Hahaha!|

As for the parameter `optval`, it's usually a pointer to an `int`
indicating the value in question. For booleans, zero is false, and
non-zero is true. And that's an absolute fact, unless it's different on
your system. If there is no parameter to be passed, `optval` can be
`NULL`.

The final parameter, `optlen`, should be set to the length of `optval`,
probably `sizeof(int)`, but varies depending on the option. Note that in
the case of `getsockopt()`, this is a pointer to a `socklen_t`, and it
specifies the maximum size object that will be stored in `optval` (to
prevent buffer overflows). And `getsockopt()` will modify the value of
`optlen` to reflect the number of bytes actually set.

**Warning**: on some systems (notably [ix[SunOS]] [ix[Solaris]] Sun and
[ix[Windows]] Windows), the option can be a `char` instead of an `int`,
and is set to, for example, a character value of `'1'` instead of an
`int` value of `1`. Again, check your own man pages for more info with
"`man setsockopt`" and "`man 7 socket`"!

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
int optval;
int optlen;
char *optval2;

// set SO_REUSEADDR on a socket to true (1):
optval = 1;
setsockopt(s1, SOL_SOCKET, SO_REUSEADDR, &optval, sizeof optval);

// bind a socket to a device name (might not work on all systems):
optval2 = "eth1"; // 4 bytes long, so 4, below:
setsockopt(s2, SOL_SOCKET, SO_BINDTODEVICE, optval2, 4);

// see if the SO_BROADCAST flag is set:
getsockopt(s3, SOL_SOCKET, SO_BROADCAST, &optval, &optlen);
if (optval != 0) {
    print("SO_BROADCAST enabled on s3!\n");
}
```

#### See Also

[`fcntl()`](#fcntlman)


[[pagebreak]]
## `send()`, `sendto()` {#sendman}

Send data out over a socket

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

ssize_t send(int s, const void *buf, size_t len, int flags);
ssize_t sendto(int s, const void *buf, size_t len,
               int flags, const struct sockaddr *to,
               socklen_t tolen);
```

#### Description 

[ixtt[send()]] [ixtt[sendto()]] These functions send data to a socket.
Generally speaking, `send()` is used for TCP [ixtt[SOCK\_STREAM]]
`SOCK_STREAM` connected sockets, and `sendto()` is used for UDP
[ixtt[SOCK\_DGRAM]] `SOCK_DGRAM` unconnected datagram sockets. With the
unconnected sockets, you must specify the destination of a packet each
time you send one, and that's why the last parameters of `sendto()`
define where the packet is going.

With both `send()` and `sendto()`, the parameter `s` is the socket,
`buf` is a pointer to the data you want to send, `len` is the number of
bytes you want to send, and `flags` allows you to specify more
information about how the data is to be sent. Set `flags` to zero if you
want it to be "normal" data. Here are some of the commonly used flags,
but check your local `send()` man pages for more details:

| Macro           | Description                                            |
|-----------------|--------------------------------------------------------|
| [ixtt[MSG\_OOB]] `MSG_OOB` | Send as [ix[out-of-band data]] "out of band" data. TCP supports this, and it's a way to tell the receiving system that this data has a higher priority than the normal data. The receiver will receive the signal [ixtt[SIGURG]] `SIGURG` and it can then receive this data without first receiving all the rest of the normal data in the queue.|
| [ixtt[MSG\_DONTROUTE]] `MSG_DONTROUTE` | Don't send this data over a router, just keep it local.|
| [ixtt[MSG\_DONTWAIT]] `MSG_DONTWAIT` | If `send()` would block because outbound traffic is clogged, have it return [ixtt[EAGAIN]] `EAGAIN`.  This is like a "enable [ix[non-blocking sockets]] non-blocking just for this send."  See the section on [blocking](#blocking)  for more details.|
| [ixtt[MSG\_NOSIGNAL]] `MSG_NOSIGNAL` | If you `send()` to a remote host which is no longer `recv()`ing, you'll typically get the signal [ixtt[SIGPIPE]] `SIGPIPE`. Adding this flag prevents that signal from being raised.|

#### Return Value

Returns the number of bytes actually sent, or `-1` on error (and `errno`
will be set accordingly). Note that the number of bytes actually sent
might be less than the number you asked it to send! See the section on
[handling partial `send()`s](#sendall) for a helper function to get
around this.

Also, if the socket has been closed by either side, the process calling
`send()` will get the signal `SIGPIPE`. (Unless `send()` was called with
the `MSG_NOSIGNAL` flag.)

#### Example

```{.c .numberLines}
int spatula_count = 3490;
char *secret_message = "The Cheese is in The Toaster";

int stream_socket, dgram_socket;
struct sockaddr_in dest;
int temp;

// first with TCP stream sockets:

// assume sockets are made and connected
//stream_socket = socket(...
//connect(stream_socket, ...

// convert to network byte order
temp = htonl(spatula_count);
// send data normally:
send(stream_socket, &temp, sizeof temp, 0);

// send secret message out of band:
send(stream_socket, secret_message, strlen(secret_message)+1, MSG_OOB);

// now with UDP datagram sockets:
//getaddrinfo(...
//dest = ... // assume "dest" holds the address of the destination
//dgram_socket = socket(...

// send secret message normally:
sendto(dgram_socket, secret_message, strlen(secret_message)+1, 0, 
       (struct sockaddr*)&dest, sizeof dest);
```

#### See Also

[`recv()`](#recvman), [`recvfrom()`](#recvman)


[[pagebreak]]
## `shutdown()` {#shutdownman}

Stop further sends and receives on a socket

#### Synopsis

```{.c}
#include <sys/socket.h>

int shutdown(int s, int how);
```

#### Description 

[ixtt[shutdown()]] That's it! I've had it! No more `send()`s are allowed
on this socket, but I still want to `recv()` data on it! Or vice-versa!
How can I do this?

When you `close()` a socket descriptor, it closes both sides of the
socket for reading and writing, and frees the socket descriptor. If you
just want to close one side or the other, you can use this `shutdown()`
call.

As for parameters, `s` is obviously the socket you want to perform this
action on, and what action that is can be specified with the `how`
parameter. How can be `SHUT_RD` to prevent further `recv()`s, `SHUT_WR`
to prohibit further `send()`s, or `SHUT_RDWR` to do both.

Note that `shutdown()` doesn't free up the socket descriptor, so you
still have to eventually `close()` the socket even if it has been fully
shut down.

This is a rarely used system call.

#### Return Value

Returns zero on success, or `-1` on error (and `errno` will be set
accordingly).

#### Example

```{.c .numberLines}
int s = socket(PF_INET, SOCK_STREAM, 0);

// ...do some send()s and stuff in here...

// and now that we're done, don't allow any more sends()s:
shutdown(s, SHUT_WR);
```

#### See Also

[`close()`](#closeman)


[[pagebreak]]
## `socket()` {#socketman}

Allocate a socket descriptor

#### Synopsis

```{.c}
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
```

#### Description 

[ixtt[socket()]] Returns a new socket descriptor that you can use to do
sockety things with. This is generally the first call in the whopping
process of writing a socket program, and you can use the result for
subsequent calls to `listen()`, `bind()`, `accept()`, or a variety of
other functions.

In usual usage, you get the values for these parameters from a call to
`getaddrinfo()`, as shown in the example below. But you can fill them in
by hand if you really want to.

| Macro      | Description                                                 |
|------------|-------------------------------------------------------------|
| `domain` | `domain` describes what kind of socket you're interested in. This can, believe me, be a wide variety of things, but since this is a socket guide, it's going to be [ixtt[PF\_INET]] `PF_INET` for IPv4, and `PF_INET6` for IPv6.|
| `type` | Also, the `type` parameter can be a number of things, but you'll probably be setting it to either [ixtt[SOCK\_STREAM]] `SOCK_STREAM` for reliable [ix[TCP]] TCP sockets (`send()`, `recv()`) or [ixtt[SOCK\_DGRAM]] `SOCK_DGRAM` for unreliable fast [ix[UDP]] UDP sockets (`sendto()`, `recvfrom()`). (Another interesting socket type is [ixtt[SOCK\_RAW]] `SOCK_RAW` which can be used to construct packets by hand. It's pretty cool.)| 
| `protocol` | Finally, the `protocol` parameter tells which protocol to use with a certain socket type. Like I've already said, for instance, `SOCK_STREAM` uses TCP. Fortunately for you, when using `SOCK_STREAM` or `SOCK_DGRAM`, you can just set the protocol to 0, and it'll use the proper protocol automatically. Otherwise, you can use [ixtt[getprotobyname()]] `getprotobyname()` to look up the proper protocol number.|

#### Return Value

The new socket descriptor to be used in subsequent calls, or `-1` on
error (and `errno` will be set accordingly).

#### Example

```{.c .numberLines}
struct addrinfo hints, *res;
int sockfd;

// first, load up address structs with getaddrinfo():

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;     // AF_INET, AF_INET6, or AF_UNSPEC
hints.ai_socktype = SOCK_STREAM; // SOCK_STREAM or SOCK_DGRAM

getaddrinfo("www.example.com", "3490", &hints, &res);

// make a socket using the information gleaned from getaddrinfo():
sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);
```

#### See Also

[`accept()`](#acceptman), [`bind()`](#bindman),
[`getaddrinfo()`](#getaddrinfoman), [`listen()`](#listenman)


[[pagebreak]]
## `struct sockaddr` and pals {#structsockaddrman}

Structures for handling internet addresses

#### Synopsis

```{.c}
#include <netinet/in.h>

// All pointers to socket address structures are often cast to pointers
// to this type before use in various functions and system calls:

struct sockaddr {
    unsigned short    sa_family;    // address family, AF_xxx
    char              sa_data[14];  // 14 bytes of protocol address
};


// IPv4 AF_INET sockets:

struct sockaddr_in {
    short            sin_family;   // e.g. AF_INET, AF_INET6
    unsigned short   sin_port;     // e.g. htons(3490)
    struct in_addr   sin_addr;     // see struct in_addr, below
    char             sin_zero[8];  // zero this if you want to
};

struct in_addr {
    unsigned long s_addr;          // load with inet_pton()
};


// IPv6 AF_INET6 sockets:

struct sockaddr_in6 {
    u_int16_t       sin6_family;   // address family, AF_INET6
    u_int16_t       sin6_port;     // port number, Network Byte Order
    u_int32_t       sin6_flowinfo; // IPv6 flow information
    struct in6_addr sin6_addr;     // IPv6 address
    u_int32_t       sin6_scope_id; // Scope ID
};

struct in6_addr {
    unsigned char   s6_addr[16];   // load with inet_pton()
};


// General socket address holding structure, big enough to hold either
// struct sockaddr_in or struct sockaddr_in6 data:

struct sockaddr_storage {
    sa_family_t  ss_family;     // address family

    // all this is padding, implementation specific, ignore it:
    char      __ss_pad1[_SS_PAD1SIZE];
    int64_t   __ss_align;
    char      __ss_pad2[_SS_PAD2SIZE];
};
```

#### Description 

[ixtt[struct sockaddr\_in]] [ixtt[struct in\_addr]] These are the basic
structures for all syscalls and functions that deal with internet
addresses. Often you'll use `getaddrinfo()` to fill these structures
out, and then will read them when you have to.

In memory, the `struct sockaddr_in` and `struct sockaddr_in6` share the
same beginning structure as [ixtt[struct sockaddr]] `struct sockaddr`,
and you can freely cast the pointer of one type to the other without any
harm, except the possible end of the universe.

Just kidding on that end-of-the-universe thing...if the universe does
end when you cast a `struct sockaddr_in*` to a ` struct sockaddr*`, I
promise you it's pure coincidence and you shouldn't even worry about it.

So, with that in mind, remember that whenever a function says it takes a
`struct sockaddr*` you can cast your `struct sockaddr_in*`, `struct
sockaddr_in6*`, or `struct sockadd_storage*` to that type with ease and
safety.

`struct sockaddr_in` is the structure used with IPv4 addresses (e.g.
"192.0.2.10"). It holds an address family (`AF_INET`), a port in
`sin_port`, and an IPv4 address in `sin_addr`.

There's also this `sin_zero` field in `struct sockaddr_in` which some
people claim must be set to zero. Other people don't claim anything
about it (the Linux documentation doesn't even mention it at all), and
setting it to zero doesn't seem to be actually necessary. So, if you
feel like it, set it to zero using `memset()`.

Now, that `struct in_addr` is a weird beast on different systems.
Sometimes it's a crazy `union` with all kinds of `#define`s and other
nonsense. But what you should do is only use the `s_addr` field in this
structure, because many systems only implement that one.

`struct sockadd_in6` and `struct in6_addr` are very similar, except
they're used for IPv6.

`struct sockaddr_storage` is a struct you can pass to `accept()` or
`recvfrom()` when you're trying to write IP version-agnostic code and
you don't know if the new address is going to be IPv4 or IPv6. The
`struct sockaddr_storage` structure is large enough to hold both types,
unlike the original small `struct sockaddr`.

#### Example

```{.c .numberLines}
// IPv4:

struct sockaddr_in ip4addr;
int s;

ip4addr.sin_family = AF_INET;
ip4addr.sin_port = htons(3490);
inet_pton(AF_INET, "10.0.0.1", &ip4addr.sin_addr);

s = socket(PF_INET, SOCK_STREAM, 0);
bind(s, (struct sockaddr*)&ip4addr, sizeof ip4addr);
```

```{.c .numberLines}
// IPv6:

struct sockaddr_in6 ip6addr;
int s;

ip6addr.sin6_family = AF_INET6;
ip6addr.sin6_port = htons(4950);
inet_pton(AF_INET6, "2001:db8:8714:3a90::12", &ip6addr.sin6_addr);

s = socket(PF_INET6, SOCK_STREAM, 0);
bind(s, (struct sockaddr*)&ip6addr, sizeof ip6addr);
```

#### See Also

[`accept()`](#acceptman), [`bind()`](#bindman), [`connect()`](#connectman),
[`inet_aton()`](#inet_ntoaman), [`inet_ntoa()`](#inet_ntoaman)


# More References

You've come this far, and now you're screaming for more! Where else can
you go to learn more about all this stuff?

## Books {#books}

[ix[books]] [ix[references]] For old-school actual hold-it-in-your-hand
pulp paper books, try some of the following excellent books. These
redirect to affiliate links with a popular bookseller, giving me nice
kickbacks. If you're merely feeling generous, you can paypal a donation
to [`beej@beej.us`](mailto:beej@beej.us).  `:-)`

**Unix Network Programming, volumes 1-2**
by W. Richard Stevens. Published by Addison-Wesley Professional and
Prentice Hall.
ISBNs for volumes 1-2: [flr[978-0131411555|unixnet1]], 
[flr[978-0130810816|unixnet2]].

**Internetworking with TCP/IP, volume I**
by Douglas E. Comer. Published by Pearson.
ISBN [flr[978-0136085300|intertcp1]].

**TCP/IP Illustrated, volumes 1-3**
by W. Richard Stevens and Gary R. Wright. Published by Addison Wesley.
ISBNs for volumes 1, 2, and 3 (and a 3-volume set):
[flr[978-0201633467|tcpi1]], [flr[978-0201633542|tcpi2]], 
[flr[978-0201634952|tcpi3]], ([flr[978-0201776317|tcpi123]]).

**TCP/IP Network Administration**
by Craig Hunt. Published by O'Reilly & Associates, Inc.
ISBN [flr[978-0596002978|tcpna]].

**Advanced Programming in the UNIX Environment**
by W. Richard Stevens. Published by Addison Wesley.
ISBN [flr[978-0321637734|advunix]].


## Web References

[ix[references!web-based]] On the web:

**[fl[BSD Sockets: A Quick And Dirty
Primer|https://cis.temple.edu/~giorgio/old/cis307s96/readings/docs/sockets.html]]**
(Unix system programming info, too!)

**[fl[The Unix Socket FAQ|https://developerweb.net/?f=70]]**

**[fl[TCP/IP
FAQ|http://www.faqs.org/faqs/internet/tcp-ip/tcp-ip-faq/part1/]]**

**[fl[The Winsock FAQ|https://tangentsoft.net/wskfaq/]]**

And here are some relevant Wikipedia pages:

**[fl[Berkeley
Sockets|https://en.wikipedia.org/wiki/Berkeley_sockets]]**

**[fl[Internet Protocol
(IP)|https://en.wikipedia.org/wiki/Internet_Protocol]]**

**[fl[Transmission Control Protocol
(TCP)|https://en.wikipedia.org/wiki/Transmission_Control_Protocol]]**

**[fl[User Datagram Protocol
(UDP)|https://en.wikipedia.org/wiki/User_Datagram_Protocol]]**

**[fl[Client-Server|https://en.wikipedia.org/wiki/Client-server]]**

**[fl[Serialization|https://en.wikipedia.org/wiki/Serialization]]**
(packing and unpacking data)


## RFCs

[ix[RFCs]]

[fl[RFCs|https://www.rfc-editor.org/]]---the real dirt!  These are
documents that describe assigned numbers, programming APIs, and
protocols that are used on the Internet. I've included links to a few of
them here for your enjoyment, so grab a bucket of popcorn and put on
your thinking cap:

**[flrfc[RFC 1|1]]**
---The First RFC; this gives you an idea of what the "Internet" was like
just as it was coming to life, and an insight into how it was being
designed from the ground up. (This RFC is completely obsolete,
obviously!)

[ix[UDP]] **[flrfc[RFC 768|768]]**
---The User Datagram Protocol (UDP)

[ix[IP]] **[flrfc[RFC 791|791]]**
---The Internet Protocol (IP)

[ix[TCP]] **[flrfc[RFC 793|793]]**
---The Transmission Control Protocol (TCP)

[ix[telnet]] **[flrfc[RFC 854|854]]**
---The Telnet Protocol

[ix[FTP]] **[flrfc[RFC 959|959]]**
---File Transfer Protocol (FTP)

[ix[TFTP]] **[flrfc[RFC 1350|1350]]**
---The Trivial File Transfer Protocol (TFTP)

[ix[IRC]] **[flrfc[RFC 1459|1459]]**
---Internet Relay Chat Protocol (IRC)

**[flrfc[RFC 1918|1918]]**
---Address Allocation for Private Internets

[ix[DHCP]] **[flrfc[RFC 2131|2131]]**
---Dynamic Host Configuration Protocol (DHCP)

[ix[HTTP protocol]] **[flrfc[RFC 2616|2616]]**
---Hypertext Transfer Protocol (HTTP)

[ix[SMTP]] **[flrfc[RFC 2821|2821]]**
---Simple Mail Transfer Protocol (SMTP)

**[flrfc[RFC 3330|3330]]**
---Special-Use IPv4 Addresses

**[flrfc[RFC 3493|3493]]**
---Basic Socket Interface Extensions for IPv6

**[flrfc[RFC 3542|3542]]**
---Advanced Sockets Application Program Interface (API) for IPv6

**[flrfc[RFC 3849|3849]]**
---IPv6 Address Prefix Reserved for Documentation

[ix[XMPP]] **[flrfc[RFC 3920|3920]]**
---Extensible Messaging and Presence Protocol (XMPP)

[ix[NNTP]] **[flrfc[RFC 3977|3977]]**
---Network News Transfer Protocol (NNTP)

**[flrfc[RFC 4193|4193]]**
---Unique Local IPv6 Unicast Addresses

[ix[XDR]] **[flrfc[RFC 4506|4506]]**
---External Data Representation Standard (XDR)

The IETF has a nice online tool for [fl[searching and browsing
RFCs|https://tools.ietf.org/rfc/]].
