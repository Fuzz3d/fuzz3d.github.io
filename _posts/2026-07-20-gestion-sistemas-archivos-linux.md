---
title: Sistema de archivos en Linux
author: Fuzz3d
date: 2026-07-20
categories: [Blogging, Tutorial,]
tags: [linux, files, hacking]
render_with_liquid: false
---

# Cómo funciona el sistema de archivos en Linux 

Si vienes de Windows, seguro estás acostumbrado a ver tus discos como "C:", "D:", etc. En Linux las cosas se ven distinto, y al principio puede parecer confuso. En este artículo te explico, de forma simple, cómo Linux organiza y guarda tus archivos, qué son los discos y particiones, y qué es eso del "swap" que seguro has escuchado nombrar.

## ¿Qué es un sistema de archivos?

Un sistema de archivos es, básicamente, el método que usa tu computador para guardar y organizar la información en el disco. Linux no usa uno solo, sino que soporta varios, y cada uno tiene sus pros y contras:

- **ext4**: es el más usado hoy en día en Linux. Es estable, rápido y confiable. Si no sabes cuál elegir, probablemente tu sistema ya viene con este por defecto.
- **ext2**: una versión más antigua y simple, sin las protecciones que tiene ext4. Todavía se usa en casos muy puntuales, como pendrives.
- **Btrfs**: un sistema más moderno con funciones extra, como poder "tomar una foto" del estado de tus archivos en un momento dado (snapshot) para volver atrás si algo sale mal.
- **XFS**: pensado para mover archivos muy grandes de forma rápida, típico en servidores.
- **NTFS**: el sistema que usa Windows. Linux puede leerlo y escribirlo, lo que es útil si tienes un computador con arranque dual (Windows y Linux al mismo tiempo) o usas un disco externo que se comparte entre ambos sistemas.

La elección depende de qué necesitas: velocidad, seguridad ante fallos, compatibilidad con Windows, o simplemente lo que tu distribución trae por defecto (y en la mayoría de los casos, no necesitas cambiarlo).

## ¿Cómo "sabe" Linux dónde está cada archivo?

Aquí viene una idea clave que a los que recién parten en Linux les cuesta entender: los **inodos**.

Imagina que el sistema de archivos es una biblioteca. Cada libro (archivo) tiene una ficha en el catálogo con su título, autor y en qué estante está guardado, pero la ficha no es el libro en sí. En Linux, esa "ficha" es el inodo: contiene los datos del archivo (permisos, tamaño, fecha de modificación, dónde están sus datos en el disco), pero no el nombre del archivo ni su contenido.

¿Por qué importa esto? Porque en Linux puedes quedarte sin espacio en el disco de dos formas distintas: te puedes quedar sin espacio de almacenamiento (el disco físico se llenó), o te puedes quedar sin inodos disponibles (el "catálogo" se llenó, aunque el disco tenga espacio libre). Esto último pasa más de lo que uno cree, sobre todo cuando hay muchísimos archivos pequeños.

## Los tres tipos de archivos que verás siempre

En Linux, casi todo lo que ves es alguno de estos tres tipos:

1. **Archivos regulares**: son los archivos normales, de texto o binarios (fotos, videos, programas). Están repartidos por todo el sistema, no solo en una carpeta.
2. **Directorios**: son las carpetas. Sirven para agrupar y organizar otros archivos o directorios dentro de ellos.
3. **Enlaces simbólicos (symlinks)**: son como "accesos directos" en Windows. Apuntan a otro archivo o carpeta sin duplicar su contenido, lo que es útil para acceder rápido a algo sin tener dos copias.

Cada archivo tiene también permisos: quién puede leerlo, escribirlo o ejecutarlo. Por ejemplo, el dueño de un archivo puede tener permiso total, mientras que el resto de los usuarios solo pueden leerlo. Estos niveles son independientes entre sí.

## Discos y particiones: ordenando el espacio físico

Cuando hablamos de "gestionar discos" en Linux, nos referimos a administrar el almacenamiento físico: discos duros, SSD, pendrives, etc.

La herramienta más usada para esto es `fdisk`, que permite crear, borrar y revisar particiones de un disco. Una **partición** es simplemente dividir un disco en secciones separadas, como si cortaras una torta en pedazos: cada pedazo puede tener su propio sistema de archivos y funcionar de forma independiente.

Para ver las particiones de tu disco, puedes usar:

```bash
sudo fdisk -l
```

Esto te mostrará cada disco conectado, su tamaño, y cómo está dividido.

## Montar y desmontar: cómo Linux "conecta" un disco a una carpeta

Acá viene otro concepto que confunde a los que llegan de Windows: en Linux, para poder usar una unidad (un pendrive, un disco externo, una partición), primero hay que **montarla**.

Montar significa "conectar" ese disco a una carpeta específica de tu sistema, para que puedas acceder a su contenido como si fuera una carpeta más. Por ejemplo, si conectas un pendrive, puedes montarlo así:

```bash
sudo mount /dev/sdb1 /mnt/usb
```

Con esto, todo lo que hay dentro del pendrive aparecerá dentro de la carpeta `/mnt/usb`.

Cuando ya no lo necesitas, lo desmontas con:

```bash
sudo umount /mnt/usb
```

Ojo: no puedes desmontar un disco que está siendo usado por algún programa en ese momento. Si Linux te da un error al intentarlo, probablemente hay algo abierto usando ese disco.

### El archivo fstab: para que todo se monte solo

Si quieres que ciertas unidades se monten automáticamente cada vez que enciendes tu computador (por ejemplo, tu disco principal), Linux usa un archivo llamado `/etc/fstab`. Ahí se define qué disco va en qué carpeta y con qué opciones, para que no tengas que montarlo manualmente cada vez.

## ¿Qué es el swap y para qué sirve?

El **swap** (espacio de intercambio) es una especie de "memoria RAM de respaldo", pero en el disco.

Cuando tu computador se queda sin memoria RAM disponible, Linux toma la información que no está usando en ese momento y la mueve temporalmente al swap, liberando RAM para lo que sí necesitas ahora. No es tan rápido como la RAM real, pero evita que tu sistema se cuelgue cuando la memoria se llena.

El swap también se usa para la **hibernación**: cuando apagas tu computador en modo hibernar, Linux guarda todo el estado actual (programas abiertos, procesos) en el swap, y cuando lo enciendes de nuevo, retoma todo justo donde lo dejaste.

¿Cuánto swap necesitas? Depende de cuánta RAM tengas. Si tienes bastante RAM (16GB o más), probablemente necesites poco o nada de swap. Si tienes menos memoria o usas programas pesados, un poco más de swap ayuda a que todo funcione más fluido.

Un detalle importante: como el swap puede guardar temporalmente información sensible, muchas distribuciones recomiendan cifrarlo.

## En resumen

- Linux organiza sus archivos usando un sistema de archivos (ext4 siendo el más común).
- Cada archivo tiene un "inodo" con sus datos, algo así como una ficha de biblioteca.
- Los discos se dividen en particiones, y cada partición se "monta" en una carpeta para poder usarla.
- El swap es tu respaldo cuando la RAM se llena, y también sirve para hibernar el sistema.

No necesitas memorizar todo esto de una vez. Con usar Linux en el día a día, estos conceptos se vuelven naturales bastante rápido.
