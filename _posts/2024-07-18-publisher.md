---
title: Publisher Writeup
author: Fuzz3d
date: 2024-07-18
categories: [Blogging, Tutorial, Hacking]
tags: [ctf, hacking]
render_with_liquid: false
---

# Enumeracion

La fase de enumeración es fundamental para identificar los servicios y puertos abiertos que podrían ser explotados. Luego, pasaremos a la recolección de información, donde profundizaremos en los datos obtenidos para descubrir posibles vulnerabilidades. Finalmente, abordaremos la explotación de la máquina, aplicando técnicas y herramientas específicas para comprometerla de manera controlada y ética.

En esta sección, utilizaremos la herramienta nmap para escanear puertos y servicios abiertos en la máquina objetivo. El comando utilizado es:

```bash
nmap -T4 -sS -sCV -Pn {IP}
```

Este comando realiza un escaneo de puertos utilizando las siguientes opciones:

- `T4`: Establece la plantilla de temporización en «Agresivo».  Nmap utiliza plantillas de temporización para controlar la velocidad y agresividad del sondeo.  La plantilla 4 consigue un equilibrio entre velocidad y precisión.
- `sS`: Realiza un escaneo SYN. Envía un paquete SYN a cada puerto y espera una respuesta SYN-ACK. Se trata de un escaneo sigiloso, ya que no establece una conexión completa.
- `sCV`: Detecta versiones de servicios y ejecuta scripts de nmap para obtener más información.
- `Pn`: Indica a nmap que no haga ping a la máquina antes de escanearla.

El resultado de este escaneo nos proporcionará una lista de puertos abiertos y los servicios asociados, lo cual es esencial para las siguientes fases de recolección de información y explotación. 

![Captura de pantalla 2024-07-17 162047](https://github.com/user-attachments/assets/0d00756e-e3d6-4cf7-80a3-34da74f7c25d)

Al entrar a la pagina web lo que podemos ver a simple vista que “Spip” esta por todos lados, si no sabemos que es esto podemos realizar una simple búsqueda en Google el cual nos dirá que “SPIP es un software libre de origen francés tipo sistema de gestión de contenidos destinado a la producción de sitios web, orientado a revistas colaborativas en línea e inspirado en los roles de una redacción.”  

![Captura de pantalla 2024-07-17 162301](https://github.com/user-attachments/assets/9fa3e01e-d3bf-45cf-bdd8-aa5fa7c37b7a)

### FUZZING

El siguiente paso es realizar fuzzing con la herramienta de nuestra elección para descubrir posibles puntos de entrada adicionales en la máquina. El fuzzing nos permitirá identificar directorios, archivos ocultos y otros recursos que no son evidentes a simple vista.

Para este propósito, podemos utilizar herramientas como `gobuster`, `dirb` o `ffuf`. A continuación, se muestra un ejemplo de cómo utilizar `gobuster` para realizar fuzzing en el sitio web:

```bash
gobuster dir -u <http://10.10.200.210> -w /usr/share/wordlists/dirb/common.txt
```

Este comando utiliza `gobuster` con las siguientes opciones:

- `dir`: Indica que se realizará un escaneo de directorios.
- `u <http://10.10.200.210`>: Especifica la URL objetivo.
- `w /usr/share/wordlists/dirb/common.txt`: Utiliza una lista de palabras común para el fuzzing.

El resultado del fuzzing nos proporcionará una lista de directorios y archivos descubiertos que podemos investigar más a fondo para continuar con la fase de recolección de información y explotación.

![Captura de pantalla 2024-07-17 163605](https://github.com/user-attachments/assets/eb7ead93-6bc7-4a68-90f0-a472577b1c60)

Vemos que el resultado nos arroja el directorio /spip, dentro podemos ver lo siguiente 

![Captura de pantalla 2024-07-17 162301](https://github.com/user-attachments/assets/acb7704d-5bc2-473c-9455-7c7807d6e083)

Tenemos un vector de ataque potencial, que es el CMS SPIP. Ahora solo debemos conocer la versión para saber si es vulnerable o no. Esto lo podemos hacer con la herramienta `Wappalyzer` o yendo al archivo dentro de http://10.10.200.210/spip/local/config.txt. De cualquier forma la version utilizada es la 4.2.0 

![Captura de pantalla 2024-07-17 164245](https://github.com/user-attachments/assets/988ac95c-05bf-4152-a697-950b220fdcd7)

![Captura de pantalla 2024-07-17 164349](https://github.com/user-attachments/assets/040d25b2-aa78-4628-bbfc-3e3611d70703)

### BÚSQUEDA DE EXPLOITS

El siguiente paso es buscar exploits específicos para la versión del CMS SPIP que hemos identificado, la versión 4.2.0. Para ello, podemos utilizar varias fuentes y herramientas en línea que nos proporcionarán información sobre vulnerabilidades conocidas y exploits disponibles.

Algunas de las fuentes más comunes y útiles para buscar exploits incluyen:

- **Exploit Database (https://www.exploit-db.com/)**: Una base de datos completa y actualizada de exploits públicos.
- **CVE Details (https://www.cvedetails.com/)**: Un recurso para buscar vulnerabilidades específicas y sus detalles.
- **NVD (National Vulnerability Database, https://nvd.nist.gov/)**: Una base de datos de vulnerabilidades gestionada por el NIST.
- **SearchSploit**: Una herramienta de línea de comandos que permite buscar exploits en la base de datos local de Exploit Database.

A continuación, se muestra un ejemplo de cómo utilizar `SearchSploit` para buscar exploits relacionados con SPIP:

```bash
searchsploit spip 4.2.0
```

El resultado de esta búsqueda nos proporcionará una lista de exploits disponibles para la versión específica del CMS SPIP. Una vez que identifiquemos un exploit adecuado, podemos proceder con la explotación de la máquina de manera controlada y ética, asegurándonos de documentar cada paso del proceso para futuras referencias y análisis.

Efectivamente existe una vulnerabilidad, en este caso un RCE.

![Captura de pantalla 2024-07-17 164500](https://github.com/user-attachments/assets/16730f27-e51e-4f7f-8850-a11b0d0791b0)

### EXPLOTACION CON METASPLOIT

Para realizar la explotación con Metasploit, primero debemos configurar algunos parámetros importantes, como la ruta del exploit, la IP del objetivo y nuestra IP. A continuación, se muestra un ejemplo

![Captura de pantalla 2024-07-17 165015](https://github.com/user-attachments/assets/171e9950-1c13-42c1-9b83-a2e8205c85e2)

Una vez ejecutado el exploit, deberíamos obtener acceso a la máquina objetivo. Es importante realizar esta explotación de manera controlada y ética, documentando cada paso para futuras referencias y análisis.

![Captura de pantalla 2024-07-17 165215](https://github.com/user-attachments/assets/9d22f3be-caf9-4344-808c-72710fb25537)

Utilizando el comando help nos lista la serie de comandos disponibles. 
Navegando un poco por los directorios llegamos a obtener una id_rsa que nos servira para conectarnos remotamente a la maquina victima. 

![Captura de pantalla 2024-07-17 165625](https://github.com/user-attachments/assets/e15e4f78-3a5f-45c5-9fa1-f99d5bcdf963)


### EXPLOTACION MANUAL

A continuación veremos cómo realizar esta explotación pero de una forma un poco más manual y controlada.

Lo que haremos será utilizar un exploit de GitHub del usuario Chocapikk. 

https://github.com/Chocapikk/CVE-2023-27372.git

Los pasos son los siguientes 

- Clonar el repositorio con el comando: `git clone https://github.com/Chocapikk/CVE-2023-27372`
- Entrar a la carpeta: `cd CVE-2023-27372`
- Instalar los requisitos necesarios: `pip install -r requirements.txt`
- Exploit: `python CVE-2023-27372.py -u http://10.10.200.210/spip/ -v -o report.txt`

Una vez hechos estos pasos podemos lanzar la herramienta y si todo ha salido de manera correcta ejecutar comando en la maquina victima

![image](https://github.com/user-attachments/assets/f77c15af-8636-46c8-99c2-166990d96863)

### ESCALADA DE PRIVILEGIOS

Una vez que hemos obtenido acceso inicial a la máquina, el siguiente paso es buscar maneras de escalar privilegios para obtener acceso root. La escalada de privilegios nos permitirá tener un control total sobre la máquina y acceder a áreas y archivos que normalmente estarían restringidos.

Algunas técnicas comunes para la escalada de privilegios incluyen:

- **Buscar configuraciones incorrectas o vulnerabilidades en servicios y aplicaciones que se ejecutan con privilegios elevados.**
- **Explorar el sistema en busca de archivos y directorios con permisos inadecuados.**
- **Revisar cron jobs y scripts que se ejecutan automáticamente para identificar posibles puntos de explotación.**
- **Buscar contraseñas almacenadas en texto plano o configuraciones de sudo mal configuradas.**

En este caso, vamos a revisar los permisos de los archivos y directorios.

```bash
# Listar archivos con permisos SUID
find / -perm -4000 2>/dev/null

```

El comando `find / -perm -4000 2>/dev/null` nos ayudará a identificar archivos con el bit SUID establecido, lo que significa que se ejecutan con los privilegios del propietario del archivo, que a menudo es root. Estos archivos pueden ser potenciales vectores de escalada de privilegios si tienen vulnerabilidades.

Podemos ver un binario un poco sospechoso “run_container”.

![image](https://github.com/user-attachments/assets/e7fcc4d8-c875-4026-8bd6-eb92d7634698)

Si le echamos un ojo a este binario con el comando strings podemos ver que en realidad ejecuta un script de bash alojado en la carpeta /opt.

Si analizamos el script veremos que tenemos permisos de lectura y escritura sin embargo no podremos hacer nada ya que AppArmor nos bloquea esta accion, además nuestra shell es una ash y no una bash, aunque podemos evadir esto de alguna forma. 

El perfil AppArmor tiene un fallo, hay una regla de denegación para /dev/shm/ y /dev/tmp. No son completas como la regla para /tmp por ejemplo. Debido a la falta del comodín /** todavía podemos escribir en /dev/shm y /var/tmp.

![image](https://github.com/user-attachments/assets/7c0affe2-3dbc-4b83-a819-3c279dc7f2ed)

Nos traemos el binario /bash hacia nuestro directorio temporal y lo ejecutamos, como podemos ver, antes de correr el script no tenemos permisos para listar lo que hay dentro de /opt, sin embargo luego de ejecutar nuestra bash ya podemos acceder a el. 

![image](https://github.com/user-attachments/assets/b40b1103-366e-468b-8e39-67adbe1df75b)

Escribimos un pequeño script que copia el /bin/bash en /var/tmp y establece el bit SUID esta vez. Debido a que el script se ejecuta en el contexto de root como se mencionó al principio, el propietario también sigue siendo root y por lo tanto podemos obtener un shell de root.

![image](https://github.com/user-attachments/assets/a9fe9ead-7e5e-4792-bbd3-42bf6ffe9535)

Luego de ejecutar nuestro binario basH dentro del directorio /var/tmp con el parametro -p podemos obtener una shell como usuario root siendo capaces de leer la flag root.txt

![image](https://github.com/user-attachments/assets/4213cf33-0fde-4b1a-9ebb-5ab63b026bce)
