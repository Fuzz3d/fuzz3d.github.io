---
title: Password Attacks PT.1
author: Fuzz3d
date: 2025-07-15 
categories: [Blogging,]
tags: [password]
render_with_liquid: false
---
Esta guía se basa en eludir los principios de la **autenticación**, comprometiendo las contraseñas de los usuarios en sistemas, aplicaciones e incluso métodos de cifrado. 

### **Autenticación**

La autenticación verifica la identidad mediante cuatro factores:

1. **Algo que sabes**: Contraseña, PIN, etc.
2. **Algo que tienes**: Tarjeta de acceso, llave de seguridad, herramientas MFA.
3. **Algo que eres**: Biometría (huella, rostro) o identificadores como el correo.
4. **Dónde estás**: Geolocalización o dirección IP.

La combinación de estos factores depende del nivel de seguridad requerido. Por ejemplo:

- **Acceso a sistemas médicos**: Tarjeta CAC + contraseña + PIN.
- **Correo electrónico**: Usuario/contraseña + autenticación en dos pasos (2FA) + biometría.

La **contraseña** es el factor más vulnerable, ya que puede ser bypasseada con técnicas de ataque (TTPs). Si es correcta, el sistema otorga **autorización**, definiendo los permisos del usuario.

### **El Uso de Contraseñas**

Las contraseñas siguen siendo el método de autenticación más común, definidas como combinaciones de letras, números y símbolos que validan la identidad. Por ejemplo, una contraseña de 8 caracteres con mayúsculas y números ofrece **208 mil millones de posibilidades**, pero en la práctica, pueden ser frases memorables (como "ÁrbolPerroElefante") siempre que cumplan los estándares de seguridad.

- **Comodidad vs. Seguridad**: Aunque los sistemas complejos (como MFA) mejoran la seguridad, también aumentan la carga de trabajo y afectan la experiencia de usuario (UX). Por eso, **usuario + contraseña** sigue siendo popular en comercios online.
- **Malas Prácticas**:
    - Un estudio de Google (2019) reveló que **24%** de los estadounidenses usaban contraseñas débiles como "123456" o "password".
    - **66% reutilizaban** la misma contraseña en múltiples cuentas, facilitando ataques si se filtraba una.
    - Solo **15%** usaba gestores de contraseñas.

## **Introducción al Descifrado de Contraseñas**

Las contraseñas se almacenan como **hashes** (valores cifrados irreversibles) para protegerlas. Algoritmos comunes como **MD5** y **SHA-256** transforman contraseñas en cadenas únicas. Por ejemplo:

- `Soccer06!` → MD5: `40291c1d19ee11a7df8495c4cccefdfa`
- `Soccer06!` → SHA-256: `a025dc6fabb09c2b8bfe23b5944635f9b68433ebd9a1a09453dd4fee00766d93`

**Técnicas de Ataque**

1. **Tablas Arcoíris (Rainbow Tables)**:
    - Bases de datos precalculadas de hashes comunes.
    - Se neutralizan con **salting** (agregar datos aleatorios antes del hashing). Ejemplo:
        - `Th1sIsTh3S@lt_Soccer06!` → MD5: `90a10ba83c04e7996bc53373170b5474` (diferente al original).
2. **Ataque de Diccionario**:
    - Usa listas de contraseñas comunes (ej. **rockyou.txt** con 14M de contraseñas reales filtradas).
    - Eficaz y rápido, especialmente en pruebas de penetración.
3. **Fuerza Bruta**:
    - Prueba todas las combinaciones posibles.
    - Inviable para contraseñas largas, pero efectivo en contraseñas cortas (<9 caracteres).
    - Velocidad depende del hardware y algoritmo:
        - MD5: ~5 millones de intentos/segundo en un portátil estándar.

### **John the Ripper: La Herramienta Clave para Descifrar Contraseñas**

**¿Qué es John the Ripper (JtR)?**

- Herramienta **open-source** para descifrar contraseñas mediante ataques de **fuerza bruta, diccionario y reglas**.
- Lanzada en 1996, es un estándar en ciberseguridad. La versión **"jumbo"** incluye optimizaciones, soporte para 64 bits y listas de palabras multilingües.

**Modos de Ataque Principales**

1. **Single Crack Mode**
    - Usa información del usuario (nombre, directorio home, etc.) para generar contraseñas probables.
    - Ejemplo: Si el usuario es `r0lf` y su nombre real es "Rolf Sebastian", JtR prueba variantes como `Rolf1` o `Sebastian123`.
    - Comando:

```bash
john --single archivo_passwd
```

**Wordlist Mode (Diccionario)**

- Prueba contraseñas desde un archivo de palabras (ej. `rockyou.txt`).
- Soporta reglas para modificar palabras (mayúsculas, números, etc.).
- Comando:

```bash
john --wordlist=lista.txt hash_archivo
```

**Incremental Mode (Fuerza Bruta Inteligente)**

- Combina fuerza bruta con un **modelo estadístico** (cadenas de Markov) para priorizar combinaciones probables.
- Lento pero exhaustivo. Ideal para contraseñas sin patrones conocidos.
- Comando:

```bash
john --incremental hash_archivo
```

**Identificación de Hashes**

- JtR puede tener dificultades para reconocer formatos de hash poco comunes. Herramientas como **hashID** ayudan a identificarlos:
    
    ```bash
    hashid -j "hash_ejemplo"
    ```
    
- Ejemplo: El hash `193069ceb0461e1d40d216e32c79c704` podría ser MD5, NTLM o RIPEMD-128. El contexto es clave.

**Descifrado de Archivos Protegidos**

- JtR incluye herramientas `2john` para extraer hashes de archivos (ZIP, PDF, etc.) y convertirlos a formatos crackeables:

```bash
zip2john archivo.zip > hash_zip
john hash_zip
```

### **Introducción a Hashcat**

Hashcat es una potente herramienta de *cracking* de contraseñas, compatible con Linux, Windows y macOS. Originalmente privativo, pasó a ser de **código abierto en 2015**. Destaca por su soporte para GPU, lo que acelera el proceso de descifrado de hashes.

**Sintaxis Básica**

El comando general para usar Hashcat es:

```bash
hashcat -a [modo_ataque] -m [tipo_hash] <hashes> [wordlist, reglas, máscara, ...]
```

**Tipos de Hash**

Hashcat soporta **cientos de algoritmos de hash**, cada uno con un ID. Para identificarlos:

- Usar `hashcat --help` para ver la lista.
- Herramientas como `hashid` ayudan a reconocer hashes desconocidos.

**Modos de Ataque Principales**

**Ataque de Diccionario (`a 0`)**

- Prueba contraseñas de una lista de palabras (ej: `rockyou.txt`).
- Ejemplo para un hash MD5 (ID `0`):

```bash
hashcat -a 0 -m 0 e3e3ec5831ad5e7288241960e5d4fdb8 /usr/share/wordlists/rockyou.txt
```

**Reglas de Modificación**:

Aplican transformaciones (ej: añadir números o usar "leet speak").

```bash
hashcat -a 0 -m 0 <hash> rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

**Ataque de Máscara (`a 3`)**

- Fuerza bruta **personalizada**, definiendo patrones (ej: 6 letras + 2 números).
- Optimiza el espacio de búsqueda.

Hashcat es una herramienta esencial para auditorías de seguridad, permitiendo ataques eficientes con diccionarios, reglas y máscaras. Ideal para pentesters y administradores que buscan fortalecer la seguridad de contraseñas.
