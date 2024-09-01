---
title: Conexiones TCP por Python
author: Fuzz3d
date: 2024-08-27
categories: [Blogging, Tutorial, Herramienta]
tags: [redes, python, hacking]
render_with_liquid: false
---

# Server

![Shot-2024-08-27-134300](https://github.com/user-attachments/assets/d5b8dacf-d70b-4b22-afb2-12ef94d69325)

- Input -> Pide al usuario que ingrese la dirección IP y el puerto en los que el servidor estará escuchando
- host -> IP del servidor
- Port -> Puerto del servidor

### Creación del Socket
![Shot-2024-08-27-134416](https://github.com/user-attachments/assets/827a69c3-d507-4b1b-9ea3-4abe3e016c7f)
### Desglose 
- Crea un socket TCP/IP con `AF_INET` para IPv4 y `SOCK_STREAM` para TCP
- **`with...`** -> Asegura que el socket se cierre automáticamente al terminar
- **`s.bind`** -> Poner en escucha
- **`s.listen`** -> Configura el servidor para escuchar conexiones entrantes, permitiendo una cola de hasta 1 conexión
- **`conn, addr = s.accept()`** Espera y acepta una conexión entrante. `conn` es el objeto de conexión y `addr` es la dirección del cliente
- **`with conn:`** Inicia un bloque `with` para asegurar que la conexión se cierre automáticamente cuando se termine
- **`while True:`** Inicia un bucle infinito para manejar la comunicación con el cliente.
- **`data = conn.recv(1024)`** Recibe hasta 1024 bytes de datos del cliente
- **`if not data: break`** Si no se reciben datos, rompe el bucle y cierra la conexión
- **`conn.sendall(data)`** Envía de vuelta todos los datos recibidos al cliente (funcionando como un servidor "eco")
- **`start_server()`** Llama a la función para iniciar el servidor

Este programa crea un servidor simple que escucha en la IP y puerto que el usuario ingresa, aceptando una conexión y devolviendo al cliente cualquier dato que recibe (función de eco)

---

# Cliente 

### Función start_client()
![Shot-2024-08-27-141625](https://github.com/user-attachments/assets/e748d6a5-f615-4f4a-a3cb-e18a95eb49c6)

- **`host = input('Ingrese la dirección del servidor: ')`** Solicita la dirección del servidor
- **`port = int(input('Ingrese el puerto del servidor: ')`** Solicita el puerto del servidor

### Conexión al servidor

![Shot-2024-08-27-141424](https://github.com/user-attachments/assets/f969bf87-acf4-4ed2-8199-f347d8aefcf2)

### Desglose

- **`with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:`** Crea un socket TCP/IP. El uso de `with` asegura que el socket se cierre automáticamente al finalizar
- **`s.connect((host, port))`** Intenta conectarse al servidor usando la IP y puerto proporcionados.
- **`s.sendall(b"\n[+] Mensaje enviado de vuelta con éxito!")`** Envía un mensaje al servidor. El prefijo `b` convierte la cadena en un objeto de bytes, que es el formato necesario para enviar datos a través de un socket.
- **`data = s.recv(1024)`** Espera y recibe datos del servidor, almacenando hasta 1024 bytes en la variable `data`
-  **`print(f"\n[i] El servidor te ha enviado un mensaje: {data.decode()}")`** Decodifica los datos recibidos de bytes a texto (string) y los muestra en la consola
- **`start_client()`** Llama a la función para iniciar el cliente.

Este cliente se conecta al servidor especificado, envía un mensaje y luego espera una respuesta. La respuesta del servidor se recibe y se muestra en la consola
