+++
title = "Parsear texto en Python para MarkdownV2 (API Telegram)"
date = "2023-11-07T10:19:13-03:00"
author = "Alitux"
authorTwitter = "alituxtdf" #do not include @
cover = ""
tags = ["python", "markdown","telegram"]
keywords = ["", ""]
description = "Como parsear texto en Python para MarkdownV2 (API Telegram)"
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++

Supongamos que se tiene el texto:

```code
web#id=28&cids=1&menu_id=281&action=415&model=crm.lead&view_type=form
```
Si uno intentara mandar el texto vía la API de Telegram la API respondería:
```json	
{"ok":false,"error_code":400,"description":"Bad Request: can't parse entities: Character '#' is reserved and must be escaped with the preceding '\\'"}
```

Esto es porque el carácter # es especial y hay que «escaparlo» o decirle a la API que no lo use para formatear texto. Para ello me armé la siguiente función que funciona de primera con todos los caracteres especiales:

```python
def markdownv2_parser(in_string):
    # Parsing MarkdownV2
    SPECIAL_CHARS = ['\\','_', '*', '[', ']',
                     '(', ')', '~', '`', '>', '<', '&', '#', '+', '-', '=', '|', '{', '}', '.', '!']
    for char in SPECIAL_CHARS:
        in_string = in_string.replace(char, "\\" + char)
    return in_string
```
¡Y listo el pollo!