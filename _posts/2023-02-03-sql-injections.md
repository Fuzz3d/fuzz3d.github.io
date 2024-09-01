---
title: SQL Injections
author: Fuzz3d
date: 2022-08-12 
categories: [Blogging, Tutorial]
tags: [sql, vuln, hacking]
render_with_liquid: false
---
# Que es una Inyeccion SQL?

![What is a SQL injection](https://user-images.githubusercontent.com/100166954/216692683-bf411f40-7371-4cd4-8847-24c91d067449.png)

Si estás involucrado en el area del Hacking o Pentesting, probablemente hayas escuchado estas palabras “SQL Injection”. Es bastante común al momento de realizar máquinas, en la plataforma que sea, encontrarnos con este tipo de vulnerabilidad, incluso en el mundo real podemos encontrar páginas con este fallo de seguridad

SQL Injection es un tipo de ataque de inyección SQL que utiliza sentencias SQL maliciosamente diseñadas para ejecutar comandos en el servidor de base de datos. Se puede utilizar para ejecutar código malicioso, robar datos confidenciales de la base de datos y también leer/modificar su contenido. Lo mismo se puede utilizar para iniciar sesión en una base de datos u otra ubicación de almacenamiento de datos como atacante.

Un ejemplo típico de inyección SQL ocurre cuando un atacante ingresa una consulta SQL maliciosa en un formulario o campo de entrada en un sitio web. Por ejemplo, supongamos que un sitio web tiene un formulario de inicio de sesión que le solicita a un usuario su nombre de usuario y contraseña. Si la aplicación no está adecuadamente protegida, un atacante podría ingresar la siguiente consulta maliciosa como nombre de usuario:

```python
admin' OR '1'='1
```
Esta consulta maliciosa modifica la consulta original del sitio web, haciendo que la aplicación devuelva todas las filas de la tabla de usuarios en lugar de solo la correspondiente al nombre de usuario y la contraseña proporcionados. De esta manera, el atacante podría tener acceso a información confidencial, como contraseñas y datos sensibles de los usuarios.

# Tipos de Inyecciones SQL

- Inyección SQL In-Band: le permite extraer información de una base de datos de aplicaciones web utilizando los mismos canales explotados anteriormente. Es decir, si ejecutamos el payload desde allí con alguna entrada, los resultados de la consulta (maliciosa) se mostrarán en la pantalla del sitio web. Este es uno de los tipos más comunes de inyección SQL.
- Inyección SQL basado en error: Permite extraer información de la base de datos, por medio de errores provocados a propósito por el atacante.
- Inyección SQL a ciegas: Permite conocer información de la base de datos, sin ver ningún resultado en la pantalla del sitio web. Los resultados se infieren a partir de pruebas de verdadero o falso, que se pueden ejecutar desde los campo de consulta vulnerables.

<img width="800" alt="en_121-what-is-sql-injection-02-2x" src="https://user-images.githubusercontent.com/100166954/216694850-60558c22-c6a1-439f-87e2-33fb07c7c8e8.png">

# Como prevenir estos ataques?

Para protegerse contra las inyecciones SQL, es importante seguir buenas prácticas de seguridad en la programación. Esto incluye validar y escapar adecuadamente todas las entradas del usuario, usar sentencias parametrizadas en lugar de construir consultas dinámicamente con entradas del usuario, y limitar los permisos de la base de datos solo a lo que es absolutamente necesario para el funcionamiento de la aplicación.

Además, es importante mantener actualizadas todas las aplicaciones y el software de la base de datos, ya que los proveedores suelen liberar parches y actualizaciones para corregir vulnerabilidades conocidas.
