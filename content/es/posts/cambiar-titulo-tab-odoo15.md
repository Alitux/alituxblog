+++
title = "Cambiar Título del Tab en Odoo 15"
date = "2023-11-27T12:19:13-03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Alitux"
authorTwitter = "alituxtdf" #do not include @
cover = ""
tags = ["odoo", "python","sysadmin","docker"]
keywords = ["", ""]
description = "Como cambiar el título del tab de Odoo 15 en Docker"
showFullContent = false
readingTime = false
hideComments = false
draft = false
+++

Una cortita: Para cambiar el título «odoo» que aparece en el tab de Odoo y suponiendo que lo están corriendo en Docker, se debe ejecutar dentro del contenedor de Odoo:

```bash
sed -i 's/zopenerp: "Odoo"/zopenerp: "Tunuevotitulo"/' /usr/lib/python3/dist-packages/odoo/addons/web/static/src/webclient/webclient.js
```
Y si no queremos entrar al contenedor podemos hacer: 
```bash
docker exec -tiu root sed -i 's/zopenerp: "Odoo"/zopenerp: "Tunuevotitulo"/' /usr/lib/python3/dist-packages/odoo/addons/web/static/src/webclient/webclient.js
```
**IMPORTANTE0:** No olvides en reemplazar Tunuevotitulo por el nombre quieras poner.

**IMPORTANTE1:** El cambio que no es permanente, esto quiere decir que si bajamos el contenedor y lo volvemos a levantar se vuelve todo a cero. Para hacer los cambios permanentes podríamos codear un addon o bien exponer toda la carpeta _/usr/lib/python3/dist-packages/odoo/addons/web/static/src/webclient_ como un volumen, pero no es una buena práctica y no lo sugiero mas que para safar o fines de desarrollo. 