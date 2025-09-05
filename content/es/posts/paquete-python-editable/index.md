+++
title = "Paquetes Python en modo editable"
date = "2025-09-05T10:49:54-03:00"
author = "Alitux"
authorTwitter = "" #do not include @
cover = ""
tags = ["python","pip","pipx", "package","linux","desarrollo"]
keywords = ["python","pip","package","linux","desarrollo"]
description = "Hoy les explico como instalar paquetes python en modo editable, para facilitar el desarrollo de proyectos"
showFullContent = false
readingTime = true
hideComments = false
+++

Como algunos ya saben, estoy trabajando en una herramienta que facilitará el despliegue y desarrollo de Odoo. La estoy codeando integramente en Python, valiéndome de Typer y el SDK de Docker. Sin embargo, mi flujo de laburo venía medio trabado en una etapa puntual: Básicamente codeaba, empaquetaba y después testeaba. La etapa de empaquetado me llevaba valiosos segundos, que en sucesivas iteraciones se trasnformaban en minutos. Es por ello que me puse a investigar alguna forma de hacer mas dinámico el desarrollo. Y ahí me encontré con el [PEP 660](https://peps.python.org/pep-0660/) que permite instalar paquetes en modo editable. 

Supongamos que queremos que el paquete editable debe estar disponible en el sistema y no en un entorno aislado. Para ello tenemos que seguir este simple procedimiento:

Suponiendo que tenés que armado un paquete, nos paramos en la carpeta principal del proyecto y ejecutamos el siguiente comando:

```bash
pipx install --editable .
```

Esto instalará el paquete en modo editable, y nos permitirá editar el código sin tener que volver a instalar el paquete.

Luego se lo llama como se llama cualquier paquete instalado con pipx, por ejemplo:

```bash
mipaquete
```

Y con eso ya podemos editar el código y probar nuestro trabajo sin tener que volver a instalar el paquete. 😎