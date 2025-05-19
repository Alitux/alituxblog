+++
title = "Agregar línea con devolución en Odoo 15"
date = "2023-08-07T11:19:13-03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Alitux"
authorTwitter = "alituxtdf" #do not include @
cover = ""
tags = ["odoo", "Python"]
keywords = ["", ""]
description = "Este código muestra cómo agregar una línea de devolución (con descuento negativo) a una factura borrador en Odoo. Usando account.move y product.template, crea una línea con precio negativo y la asocia a la factura especificada. Ideal para automatizar reembolsos o ajustes masivos. Útil para módulos de gestión de devoluciones o procesos contables automatizados. 🚀"
showFullContent = false
readingTime = true
hideComments = false
+++

Estoy armando un módulo que tiene varias funcionalidades, entre ellas, una que debe generar devoluciones masivas en facturas borrador. Les dejo el siguiente Snippet que puede ser útil.

```python
from odoo import models
 
##DATOS INICIALES
ID_FACTURA = 14
ID_PRODUCTO = 5 #IMPORTANTE: ¡ID DEL PRODUCT.TEMPLATE!
 
facturas = env['account.move']
factura = facturas.browse(ID_FACTURA)
productos_t = env['product.template']
 
# Crear una línea de factura tipo devolución con descuento
linea_factura = {
    'product_id': productos_t.browse(ID_PRODUCTO).product_variant_id.id,
    'name': 'Descuento por ser buena gente',
    'price_unit': -100,
    'quantity': 1,
}
 
factura.write({'invoice_line_ids': [(0, 0, linea_factura)]})
self.env.cr.commit()
```
Es bien básico, pero hace lo que tiene que hacer. 🙂