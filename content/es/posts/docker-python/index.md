+++
title = "Interactuando con contenedores Docker desde Python"
date = "2025-08-12T16:17:54-03:00"
author = "Alitux"
authorTwitter = "" #do not include @
cover = ""
tags = ["python", "docker","linux", "odoo"]
keywords = ["python", "docker","linux"]
description = "Cómo usar el SDK oficial de Docker en Python para administrar Odoo de forma simple, limpia y sin comandos engorrosos."
showFullContent = false
readingTime = true
hideComments = false
+++

Estoy trabajando en una herramienta DevOps enfocada al despliegue eficiente y simple de **Odoo** en producción y desarrollo. Como framework general para interactuar con la misma estoy usando _Typer_ y mi idea inicial era que python fuera llamando los diferentes comandos necesarios usando una mezcla de subprocess y os. Todo iba muy lindo, pero a medida que el proyecto crecía (y sigue creciendo muchísimo) cai que usar subprocess, por ejemplo, para ejecutar un comando dentro de un contenedor, se hacia bastante engorroso, propenso a errores y además se me complicaba el el post procesamiento de las salidas. Y ahí es cuando me pregunté:
**¿Existirá algo qué me permita interactuar con docker desde Python?** La respuesta es **SI** y es el **SDK oficial de Docker para Python**

## Instalación

Suponiendo que ya tengas un ambiente creado y activado, la instalación es muy sencilla:

```bash
pip install docker
```

## Código básico

Voy a tirar algunos tips piolas que pueden servir de puntapié para arrancar usando el SDK. Si necesitan algo mas avanzado o profundizar no hay nada mejor que leer la [Documentación](https://docker-py.readthedocs.io/en/stable/index.html).

### Listando todos los contenedores

```python 
import docker

client = docker.from_env()
# Traemos absolutamente todos los contenedores 
containers = client.containers.list(all=True) 

```

Con este código nos vamos a traer una lista con todos los contenedores que estén declarados en nuestra máquina. Se pueden filtrar contenedores usando la opción _filter_:

```python
import docker

client = docker.from_env()
# Se filtran solo los contenedores levantados mediante docker compose
containers = client.containers.list(
    all=True,
    filters={"label": "com.docker.compose.project"}
)
```

 Cada contenedor será un objeto [Contenedor](https://docker-py.readthedocs.io/en/stable/containers.html#container-objects) y tendrá sus propiedades y métodos particulares.

### Propiedades y métodos de un contenedor

Explicar absolutamente todo lo que se puede hacer sería una locura, por lo que a modo de ejemplo solo voy a mostrar como parar y volver a iniciar un contenedor, traerme las variables de entorno de un contenedor y ejecutar un comando dentro del mismo. 


#### Parar e iniciar un contenedor

```python
import docker

client = docker.from_env()
containers = client.containers.list(all=True)
container = containers[0].stop() #Se detiene un contenedor
container = containers[0].start() #Se inicia un contenedor
```

#### Obteniendo las variables de entorno

Voy a tomar el primer contenedor que me salga y vamos a chusmear, por ejemplo, las variables de entorno.

```python
import docker

client = docker.from_env()
containers = client.containers.list(all=True)
env_data = containers[0].attrs["Config"]["Env"] #(En formato string ¡Ojo!)
env_dict = {}
env_dict = dict(item.split("=") for item in env_data) #Acá lo pasamos a formato dict para que sea mas cómodo de trabajar.
```
#### Ejecutando un comando dentro de un contenedor

```python
import docker
## Variables de ejemplo

db_user = "usuario"
db_password = "password"
db = "odoo"

client = docker.from_env()
containers = client.containers.list(all=True)

# Query para botener todos los módulos presentes en una db postgresql de Odoo.
query = "SELECT name, latest_version FROM ir_module_module;"
result = containers[0].exec_run(
    [
                "psql",
                f"postgresql://{db_user}:{db_password}@db:5432/{db}",
                "-t",
                "-A",
                "-F",
                "|",
                "-c",
                query,
            ]
)
output = result[1].decode().strip()
```

Y hasta acá llega la entrada del día. Si quiero incentivar que, cada vez que puedan y necesiten, prueben el SDK de Docker en Python porque es excelente y se pueden ahorrar mas que un dolor de cabeza administrando contenedores, volúmenes, redes... 
