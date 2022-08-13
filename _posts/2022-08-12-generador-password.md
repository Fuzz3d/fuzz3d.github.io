---
layout: post
title: Generador de contraseñas
gh-repo: Fuzz3d/Password-Generator
gh-badge: [star, fork, follow]
tags: [Python, Script, Password]
comments: true
---

Con este sencillo script de Python puedes crear la cantidad de contraseñas que desees del largo que quieras.

```python
import random 

print("Generador de Contraseñas")

caracteres = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXTZ1234567890!@#$%^&*().,?~/"

number = input("Cantidad de contraseñas: ")
number = int(number)

largo = input("Numero de caracteres: ")
largo = int(largo)

print("[+] Generando contraseñas")

for pwd in range(number):
    password = ""
    for c in range(largo):
        password += random.choice(caracteres)
    print(password)
```
