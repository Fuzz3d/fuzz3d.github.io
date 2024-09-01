---
title: Escaner de puertos escrito en Python
author: Fuzz3d
date: 2024-08-29
categories: [Blogging, Tutorial, Herramienta]
tags: [redes, python, hacking]
render_with_liquid: false
---

El objetivo de este articulo es demostrar como es la creación de un escaner de puertos simples en python.

### Librerias utilizadas
- socket
- argparse
- sys
- concurrent.futures
- termcolor

----

### Definir Ctrl+C
**Objetivo:** Permitir que el usuario interrumpa el programa de manera segura
```python
open_sockets = []

def def_handler(sig, frame):

    print(colored(f"[!] Saliendo del programa...", 'red'))

    for socket in open_sockets:
        socket.close()

    sys.exit(1)

signal.signal(signal.SIGINT, def_handler) # CTRL+C
```

**Detalles:**

- Muestra un mensaje indicando que el programa se está cerrando.
- Cierra todos los sockets que están abiertos en `open_sockets` para liberar recursos.
- Termina el programa con `sys.exit(1)`.

### Capturar el objetivo (`target`) y los puertos (`port`) que se quieren escanear
```python
def get_arguments(): # Definir argumentos
    parser = argparse.ArgumentParser(description='Escaner de puertos TCP')
    parser.add_argument("-t", "--target", dest="target", required=True, help="Target a escanear (Ej: -t 192.168.1.1)")
    parser.add_argument("-p", "--port", dest="port", required=True, help="Puertos a escanear (Ej: -p 1-1000)")
    options = parser.parse_args() # Recopila todos los argumentos y los almacena en options

    return options.target, options.port # Retorna los argumentos
```

**Detalles:**

- Define los argumentos requeridos: `-t` para el objetivo y `-p` para los puertos.
- Si alguno de los argumentos no se proporciona, muestra un mensaje de ayuda automáticamente.
- Retorna el objetivo y la cadena de puertos proporcionada por el usuario.

### Crear un socket TCP con un tiempo de espera

```python
def create_socket():
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.settimeout(1) # Timeout de 1 segundo para determinar si el puerto esta cerrado o no

    open_sockets.append(s)

    return s 
```

**Detalles:**

- El socket se configura para usar IPv4 y TCP.
- Se establece un tiempo de espera de 1 segundo, lo que ayuda a determinar si un puerto está cerrado.
- Se agrega el socket a la lista `open_sockets` para poder cerrarlo más tarde si es necesario.
- Retorna el socket creado.

### Verificar si un puerto específico está abierto o cerrado

```python
def port_scanner(port, host):

    s = create_socket()

    try:
        s.connect((host, port)) # Se entabla la conexion
        print(colored(f"\n[+] El puerto {port} esta abierto", 'green'))
        s.close()

    except (socket.timeout, ConnectionRefusedError): # Manejo de excepciones
        pass
```

**Detalles:**

- Crea un nuevo socket llamando a `create_socket()`.
- Intenta conectar el socket al puerto y dirección IP proporcionados.
- Si la conexión es exitosa, imprime un mensaje indicando que el puerto está abierto y cierra el socket.
- Si no puede conectar (por timeout o rechazo de conexión), no hace nada y deja que el socket se cierre.

### Escanear múltiples puertos simultáneamente para acelerar el proceso

```python
def scan_ports(ports, target):

    with ThreadPoolExecutor(max_workers=100) as executor:
        executor.map(lambda port: port_scanner(port, target), ports)
```

**Detalles:**

- Utiliza `ThreadPoolExecutor` para gestionar hasta 100 hilos simultáneamente.
- Cada hilo ejecuta `port_scanner` para un puerto en particular.
- La función `executor.map()` asigna la tarea de escaneo a cada puerto.

### Interpretar la entrada del usuario sobre los puertos y convertirla en un formato usable (rango o lista)

```python
def parse_ports(ports_str):

    if '-' in ports_str: # Escanear un rango de puertos
        start, end = map(int, ports_str.split('-'))
        return range(start, end + 1)
    elif ',' in ports_str:
        return map(int, ports_str.split(','))
    else:
        return (int(ports_str),)
```

**Detalles:**

- Si la cadena contiene un guion (`-`), se interpreta como un rango de puertos (por ejemplo, `1-100` se convierte en `range(1, 101)`).
- Si la cadena contiene comas (`,`), se convierte en una lista de puertos específicos (por ejemplo, `22,80,443` se convierte en `[22, 80, 443]`).
- Si se proporciona un solo puerto, lo convierte en una tupla con un solo número.

### Orquestar la ejecución de todas las funciones para llevar a cabo el escaneo de puertos

```python
def main():
    
    target, ports_str = get_arguments() # Recibir los argumentos
    ports = parse_ports(ports_str)
    scan_ports(ports, target)
    
if __name__ == '__main__':
    main()
```

**Detalles:**

- Llama a `get_arguments()` para obtener el objetivo y los puertos a escanear.
- Utiliza `parse_ports()` para interpretar los puertos proporcionados.
- Llama a `scan_ports()` para comenzar el escaneo concurrente de los puertos.

### Flujo General del Programa
1. **Inicia el programa**: Se define el manejador de señales y se capturan los argumentos.
2. **Interpretación de puertos**: Se convierten los puertos introducidos en un rango o lista de puertos.
3. **Escaneo de puertos**: Se escanean los puertos de manera concurrente usando hilos.
4. **Manejo de interrupciones**: Si el usuario presiona `CTRL+C`, el programa se cierra limpiamente, cerrando todos los sockets abiertos.

Este flujo permite escanear puertos de manera eficiente y manejar la interrupción del programa de forma segura.

### Screenshot
![imagen](https://github.com/user-attachments/assets/f391e209-2fcf-4e82-8e0b-917ff4600307)
![imagen](https://github.com/user-attachments/assets/20be515f-6cbc-42c7-8fec-cdb7fe76ce04)
