---
title: Instalar BlackArch sobre Arch
author: Fuzz3d
date: 2024-07-28
categories: [Blogging, Tutorial, Herramienta]
tags: [blackarch, arch]
render_with_liquid: false
---

Black Arch es una distribución basada en Arch Linux que se centra en pruebas de penetración y seguridad informática. Al añadir sus repositorios a nuestro sistema, podemos acceder a una amplia gama de herramientas especializadas en seguridad cibernética. Esta guía te llevará paso a paso a través del proceso de configuración, asegurándose de que puedas instalar y utilizar estas herramientas de manera eficiente.

# Para que nos sirve?

El script `strap.sh` de la distro BlackArch se utiliza para configurar el repositorio de BlackArch en un sistema basado en Arch Linux. Este script añade la clave de firma del repositorio, actualiza la lista de paquetes y configura el sistema para que pueda instalar y utilizar las herramientas de seguridad cibernética disponibles en el repositorio de BlackArch.

## Paso numero 1: Descargar el script

Para instalar el repositorio de BlackArch debemos hacer uso del script, previamente mencionado, esto lo podemos hacer facilmente con el siguiente comando:

```bash
curl -O https://blackarch.org/strap.sh
```
![240728_02h42m28s_screenshot](https://github.com/user-attachments/assets/e8e05b3d-0ca0-40a8-99c2-7b4696c31d79)

## Paso numero 2: Instalacion del repositorio

Debemos darle permisos de ejecucion y luego correr el script 

```bash
chmod +x strap.sh
sudo ./strap.sh
```

![240728_02h43m57s_screenshot](https://github.com/user-attachments/assets/309e708b-3203-4be2-a6ef-136463935113)

## Paso numero 3: Descargar las herramientas

Una vez realizado los pasos anteriores ya podemos instalar las herramientas que necesitamos para realizar nuestras pruebas de penetracion 

![240728_14h23m31s_screenshot](https://github.com/user-attachments/assets/566c487d-5f91-417e-b44b-6a50ac150e22)
