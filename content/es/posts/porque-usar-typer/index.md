+++
title = "Conociendo Typer"
date = "2025-01-05T11:19:13-03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Alitux"
authorTwitter = "alituxtdf" #do not include @
cover = ""
tags = ["typer", "python", "sysadmin", "devops"]
keywords = ["", ""]
description = "Al meterse en DevOps, automatizar tareas es clave y Python suele ser una gran herramienta. Aunque argparse es popular para CLI, su uso puede volverse engorroso en proyectos grandes. Ahí entra Typer, que permite escribir comandos limpios, con validaciones, tipado moderno y un enfoque más mantenible y modular para CLIs en Python."
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++

Cuando se empieza a avanzar en el mundo devops siempre surge la necesidad de automatizar tareas y acá uno puede tirar de bash, de lua, de ruby, etc. 
En mi caso, trato de usar Python para casi todo lo que se pueda. Seguramente lo primero que vamos a encontrar es la libreria [argparse](https://docs.python.org/3/library/argparse.html) que es una de las más usadas en Python. Sin embargo, aunque es muy potente y flexible, deja muchas cuestiones libradas al criterio del desarrollador y carece de una buena forma de hacer validaciones o el código a medida que crece tiende a enredarse un poco. Y aclaro para mi es una excelente opción para hacer un CLI cuando se trata de proyectos simples, pero al día de la fecha lo ideal es algo que nos permita crear y mantener un código limpio, modular y con estándares modernos. Y acá es donde entra Typer.

## Typer

{{< figure src="logo_typer.svg"  width="200" >}}

[Typer](https://typer.tiangolo.com/) es una librería creada por [Sebastián Ramírez](https://github.com/tiangolo) que permite crear CLIs de forma sencilla y limpia. En lo personal creo que Typer no es solo una libreria, si no que lo veo de más parecido a un framework que cubre lo básico que buscamos siempre cuando armamos una CLI: Validaciones, argumentos, opciones, spinners, colores, etc. ¡Y todo eso viene built-in!

Los que vengan del palo de [FastAPI](https://fastapi.tiangolo.com/) notarán ciertos parecidos que agradecerán y que harán la curva de aprendizaje mucho mas rápida, ya que el desarrollador es el mismo y tuvo las mismas premisas para desarrollar Typer.

Sin hablar mucho de mas tiro las principales características de Typer:

- **Documentación automática**: Si, solamente comentando las funciones se crea la documentación. ❤️
- **Tipado Fuerte**: Al utilizar [Pydantic](https://pydantic-docs.helpmanual.io/) podemos crear tipos de datos que nos permiten validar los argumentos y opciones de nuestra CLI.
- **Apps de gran crecimiento**: Typer es un framework que permite crear CLIs de forma modular y escalables de forma sencilla y ordenada

**PD:** Si quieren ver en mas profundidad pueden consultar la
[Documentación oficial](https://typer.tiangolo.com/tutorial/features/)

## A los bifes

Este blog intenta ser un lugar práctico, la idea no es solo teorizar ni justificar porque una u otra herramienta, pero si embarrarnos las manos y aprender haciendo. 

### Instalación

Supongo que ya tienen su ambiente creado y activado, si no lo hicieron pueden usar la herramienta que se les cante (virtualenv, pipenv, poetry, etc.). En mi caso particular como armo herramientas que ayudan a deploys o se suelen instalar dentro de contenedores me gusta usar [Poetry](https://python-poetry.org/).

### Si usan Virtualenv
```bash
pip install typer
```
### Si usan Poetry
```bash
poetry add typer
```

### CLI Simple que recibe dos argumentos

Me voy a alejar un poco del tutorial que propone Seba, porque mi idea es ir un poco al grano de lo que yo uso día a día. Si ven que voy a 2000 km/h, entonces sigan el [tutorial oficial](https://typer.tiangolo.com/tutorial/first-steps/) que es excelente y mucho mas completo que el que voy a mostrar aquí.

Primero creamos un archivo llamado `main.py` y dentro de él vamos a escribir lo siguiente:

```python
import typer
from typing import Optional

app = typer.Typer()

@app.command()
def new(
    odoo_conf: str = typer.Argument(...,help="Ruta donde se generará el nuevo odoo.conf"),
    users: Optional[int] = typer.Option(None, help="Cantidad de usuarios concurrentes esperados"),
    ):

    """Genera un nuevo archivo odoo.conf con configuraciones base"""

    print(f"Generando nuevo archivo {odoo_conf} para  {users} usuarios")

if __name__ == "__main__":
    app()
```

Vamos a explicar un poco: Lo primero que hacemos es importar **typer** y desde typing importamos **Optional** ¿Por qué importamos este último? Simple, porque necesitamos una variable opcional.

Luego creamos un objeto de tipo **typer.Typer()** que es lo que hace que nuestra CLI sea un CLI.

Y ahora me gustaría que prestemos atención al decorador que agrego en la función **new**.
```python
@app.command()
def new(...)
...
```
Lo que hace el decorador acá es decirle a typer _"Ey, agregá esta función dentro de la app que acabamos de crear"._  Así de simple, sin muchas vueltas. Pero ahora se viene lo mas copado...

Observemos atentamente la función **new** que acabamos de crear:
```python
odoo_conf: str = typer.Argument(...,help="Ruta donde se generará el nuevo odoo.conf"),
users: Optional[int] = typer.Option(None, help="Cantidad de usuarios concurrentes esperados")
```
Recibe dos argumentos: 
- **odoo_conf:** Lo definimos como un string(str) y a su vez le decimos que será un argumento necesario (_typer.Argument(...)_) y a su vez escribimos su ayuda en la misma línea (_help="Ruta donde se generará el nuevo odoo.conf"_) 

- **users:** Algo parecido pasa con este argumento... Lo definimos como un entero que será un argumento opcional (_Optional[int]_) y a su vez escribimos su ayuda en la misma línea (_help="Cantidad de usuarios concurrentes esperados"_)

Seguimos analizando la función y nos encontramos con 
```python
    """Genera un nuevo archivo odoo.conf con configuraciones base"""
```
Ese pedacito de comentario Typer lo tomará como documentación de nuestra CLI y nos lo mostrará cuando ejecutemos la utilidad con la opción --help. A su vez, cada argumento mostrará lo que definimos en _help="sarasaaaa"_.

**¿No les parece hermoso y simple a la vez que en tan poco código resolvamos tanto?**

El _print_ es solo para que muestre algo cuando reciba los dos argumentos, pero lo puse solo para no poner _pass_ y no enredar el ejemplo con lógica 😄

## Conclusión

Hemos visto menos del 1% de lo que puede hacer Typer, pero se vendrán nuevas entradas en las que iré profundizando en el uso de Console, Colors, Spinners, etc. Les diría que adoptando Typer ya cubrís el 95% de las necesidades de un CLI moderno. 

**Si sos desarrollador Python y encima te dedicás a labores de infraestructura, Typer no puede faltar en tu caja de herramientas 🧰**