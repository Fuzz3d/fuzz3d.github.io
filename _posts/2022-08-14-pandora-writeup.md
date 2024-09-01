---
title: Pandora Writeup
author: Fuzz3d
date: 2022-08-12 
categories: [Blogging, Tutorial, Writeup]
tags: [ctf, hacking, oscp, htb]
render_with_liquid: false
---

# Fase de reconocimiento
La fase de reconocimiento la cual consiste en recolectar la mayor informacion posible como: 

- Recopilación de dominios/IPs/puertos/servicios
- Recopilación de metadatos
- Uso de Google Dorks
- Recopilación de información gracias a servicios de terceros

Para comprobar si está activa la máquina le lanzamos una traza ICMP
```bash
ping -c 1 X.X.X.X
```
Una vez nos responde la máquina podemos seguir con el escaneo, en esta ocasión usaremos la herramienta nmap, el cual es un programa de código abierto que sirve para efectuar rastreo de puertos escrito originalmente por Gordon Lyon y cuyo desarrollo se encuentra hoy a cargo de una comunidad. Fue creado originalmente para Linux aunque actualmente es multiplataforma.

Podemos escanear puertos de numerosas formas, sin embargo estas son las que yo utilizo
```bash
nmap -sC -sV -oA nmap/pandora (IP)

nmap -p- —open -sS —min-rate 5000 -vvv -n -Pn (IP) -oG allPorts
```

![image](https://user-images.githubusercontent.com/100166954/184571159-62a9e10a-cc58-4300-b7bd-56b405cc7c1f.png)

Ustedes pueden utilizar la que más les acomode.

# Foothold
Como pudimos ver, esta máquina tiene el puerto 80 abierto, esto significa que está alojando una página web.
Dentro de esta, lo primero que podemos ver es un dominio, y sin dudar lo vamos a meter en nuestro archivo /etc/hosts.

![image](https://user-images.githubusercontent.com/100166954/184571386-8c68fb78-a329-43f7-9a87-fb030e3a011d.png)
```bash
# Static table lookup for hostnames.
# See hosts(5) for details.
127.0.0.1   localhost
:11         localhost
127.0.0.1   fuzz3d-pc.localhost fuzz3d-pc

10.10.11.136  panda.htb
```
Luego de estar enumearndo subdominios, fuzzeando directorios e inspeccionando el código fuente no encontramos nada, así que toca probar otras cosas como por ejemplo un escaneo por el protocolo UDP

# Scanning por UDP

Para escanear por el protocolo UDP, usamos el siguiente comando con nmap
```bash
nmap -sU —top-ports 100 —open -T5 -v -n (IP) 
```
El escaneo nos arrojó un puerto, el 161, y dentro de este servicio está corriendo el servicio snmp

![image](https://user-images.githubusercontent.com/100166954/184572526-185c8d2f-28ad-4625-a5fd-46d5697371b5.png)

Para enumerar este servicio podemos usar la herramienta snmpbulkwalk y la llamamos con este comando
```bash
snmpbulkwalk -c public -v2c 10.10.11.136 > snmp.txt
```
- c = community string
- v2c = versión snmp 

Por qué guardamos el escaneo en un archivo txt? Porque es mucha la información que nos arroja la herramienta entonces así la podemos filtrar más fácilmente.

Para grepear la información simplemente lo hacemos así:
```bash
cat snmp.txt | grep "daniel" -i 
```
Y encontramos un usuario con una contraseña

![image](https://user-images.githubusercontent.com/100166954/184573480-e4c85f8e-9c30-4248-89fe-c01e10cafb28.png)

Y si recordamos, en el primer escaneo que hicimos, el puerto 22 estaba abierto, entonces podemos probar estas credenciales para conectarnos por SSH. 

# Escalada de privilegios

Ya en este punto de la máquina, como en cualquier CTF, nuestro objetivo además de la flag del usuario, es la flag de root, por ende debemos escalar privilegios para convertirnos en superusuario.

Luego de estar probando varias cosas, ya sea buscando archivos con información sensible, permisos SUID, etc. Encontramos una pagina web que solo se ejecuta dentro de la máquina.

![image](https://user-images.githubusercontent.com/100166954/184573786-8a4cbd61-2f3f-42b1-a26a-b01e56d302ad.png)

Lo que se hace en estos casos es una técnica llamada Local Port Forwarding, es una técnica que consiste en encapsular un protocolo de red sobre otro o un determinado tráfico de red. Con el mismo SSH podemos hacer que el puerto 80 de la máquina sea nuestro puerto 80.

```bash
ssh daniel@10.10.11.136 -L 80:127.0.0.1:80 
```

E iniciamos sesión con las credenciales como la vez anterior. Ahora toca revisar nuestro puerto 80, accediendo al navegador y buscando localhost vemos que nos encontramos con un panel de inicio de sesión.

![image](https://user-images.githubusercontent.com/100166954/184574076-1f2d86c9-0422-4e0e-ade0-03c3ec679807.png)

Antes de empezar a probar contraseñas por defecto o cualquier otra cosa, vemos que en la parte inferior de la página se encuentra la versión del programa. 

Buscando vulnerabilidades para aquella versión, hay una en github (https://github.com/shyam0904a/Pandora_v7.0NG.742_exploit_unauthenticated) la cual se aprovecha de una inyección SQL.

### Que hace el exploit

El exploit se aprovecha de una falla ubicada en un archivo php, concretamente en chart_generator.php para secuestrar la cookie de sesion del usuario administrador.

![image](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/fe87dcaa-b267-464c-8fde-6f2118056790/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220815%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220815T035218Z&X-Amz-Expires=86400&X-Amz-Signature=052d1fb1769156cb7d1b7aa98d3ab43cd101d39b57aa41d63d7d8ba0d3ebfba6&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D"Untitled.png"&x-id=GetObject)

Si luego volvemos a la pagina principal ya estaremos logueados como el usuario administrador.

En un apartado de las herramientas de administrador nos da la posibilidad de poder subir archivos, por lo tanto nos podemos aprovechar de esto para tener acceso completo a la máquina. Debido a que la página interpreta PHP, subimos una reverse shell.

![image](https://user-images.githubusercontent.com/100166954/184574476-a9b9afdb-72ee-46b6-8be8-73c0123cc48f.png)

Y ya el resto es colocarse en escucha con netcat, abrir el archivo desde la web y ganar acceso a la máquina.

### Tratamiento TTY

Para obtener una consola totalmente interactiva sin miedo a que se muera por cualquier cosa debemos seguir los siguientes pasos

```bash
script /dev/null -c bash 

ctrl+z 

stty raw -echo; fg 

reset

xterm

export TERM=xterm

export SHELL=bash
```

Estando ya como el usuario Matt en su directorio home podemos encontrar la flag del user.txt

Esta máquina tiene un binario SUID que es pandora_backup, y como lo encontramos?, de esta manera

```bash
find / -perm -4000 2/dev/null
```
![image](https://user-images.githubusercontent.com/100166954/184575756-21d72ce1-bb98-4fc7-8231-8097774fd356.png)

Ya que el archivo compilado pandora_backup nos permite ejecutarlo como root, con ltrace podemos ver que llama al comando tar por su ruta relativa.

![image](https://user-images.githubusercontent.com/100166954/184575834-b7e79a8f-db81-452c-be44-e0c54fc4a81c.png)

Esto es peligroso ya que podemos alterar el path para que en este caso creamos nuestro archivo llamado tar con una linea que nos spawnee una shell. Esto se conoce como Path Hijacking

En el directorio /tmp, nos creamos un archivo el cual se llame tar y dentro contenga solo una línea así

```bash
/usr/bin/sh
```

Le damos permisos de ejecución y posteriormente ejecutamos el binario pandora_backup. Con esto ya tendríamos acceso a la flag de root, porque nos entrega una shell como superusuario.
