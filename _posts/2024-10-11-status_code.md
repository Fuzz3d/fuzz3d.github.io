---
title: Códigos de estado
author: Fuzz3d
date: 2024-10-11
categories: [Blogging, Web]
tags: [web]
render_with_liquid: false
---

## Introducción

Cada mensaje de respuesta HTTP debe contener un código de estado en su primera línea, indicando el resultado de la solicitud. Los códigos de estado se dividen en cinco grupos según el primer dígito del código:

- **1xx**: Información
- **2xx**: Éxito
- **3xx**: Redirección
- **4xx**: Error
- **5xx**: Error interno

---

Existen numerosos códigos de estado específicos, muchos de los cuales sólo se utilizan en circunstancias especiales. Estos son los códigos de estado que es más probable encontrar cuando se ataca una aplicación web, junto con la frase de la razón habitual asociada a ellos:

## Lista de codigos

- **100 Continue**: Se envía en algunas circunstancias cuando un cliente envía una solicitud que contiene un cuerpo.
- **200 OK**: La solicitud se ha procesado correctamente.
- **201 Created**: Se ha creado un nuevo recurso en el servidor.
- **301 Moved Permanently**: El recurso solicitado ha sido movido de manera permanente a otro URI.
- **302 Found**: Se ha encontrado el recurso solicitado, pero se ha redirigido a otra URI.
- **304 Not Modified**: El recurso solicitado no ha cambiado.
- **400 Bad Request**: La solicitud no es válida.
- **401 Unauthorized**: El usuario no está autorizado para acceder al recurso.
- **403 Forbidden**: El servidor no puede realizar la operación solicitada.
- **404 Not Found**: El recurso solicitado no existe.
- **405 Method Not Allowed**: El método solicitado no está permitido para el recurso.
- **413 Request Entity Too Large**: El tamaño de la solicitud es demasiado grande.
- **414 Request-URI Too Long**: El URI solicitado es demasiado largo.
- **500 Internal Server Error**: Ha ocurrido un error interno en el servidor.
- **503 Service Unavailable**: El servidor no está disponible para procesar la solicitud.