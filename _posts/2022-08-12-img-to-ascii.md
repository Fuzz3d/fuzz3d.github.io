---
layout: post
title: Img to ASCII
gh-repo: Fuzz3d/Img-to-ASCII
gh-badge: [star, fork, follow]
tags: [Python, Script, ASCII]
comments: true
---

Este script es capaz de convertir cualquier imagen a formato ASCII, en muy pocas líneas de código. 

```python
import ascii_magic 

image = input("Ruta de la imagen: ")
output = ascii_magic.from_image_file(image, columns=200,char="#")
ascii_magic.to_terminal(output)
```
Ejemplo utilizando el código QR del post anterior:

![image](https://user-images.githubusercontent.com/100166954/184461017-672a3787-9953-497c-add0-b6f29608c73c.png)
