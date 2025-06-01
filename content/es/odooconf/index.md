+++
date = '2025-05-19T14:00:56-03:00'
draft = true
title = 'Odooconf: Manejo sencillo de odoo.conf'
+++

### ¿Qué es Odooconf?

Una tarea crítica en el despliegue de Odoo es la correcta estimación de los parámetros de configuración del archivo odoo.conf. 
Lo básico puede ser sencillo, pero a medida que uno necesita optimizar workers según usuarios concurrentes, paths, memoria, bases de datos, entre otros, todo tiende a ponerse todavía mas y mas complejo. Ni hablar si se deben automatizar dichas tareas. 

### Ejemplo de uso

Pongamos como ejemplo el escenario en el que se debe desplegar un Odoo para 20 usuarios concurrentes. El servidor tiene una 8 GB de RAM (en nuestro Odoo vamos a usar solamente 6 GB) y un procesador con 2 cores. 

- **¿Cuántos workers y cuanta memoria asignaremos a cada uno?**
- **¿Nos quedamos cortos de workers?**

_Datos:_
- Usuarios concurrentes: **20**
- RAM_VPS_DISPONIBLE: **6 GB**
- CORES: **2**

_Incógnitas_
1. ¿Cuántos workers necesitamos?
2. ¿Cuánta memoria se le debe asignar a cada worker?
3. ¿Es suficiente la cantidad de cores para dar abasto?

#### Solución

Lo primero que uno debe hacer es ir a la [documentación de Odoo](https://www.odoo.com/documentation/master/administration/on_premise/deploy.html) y se dará cuenta que para subestimar la estimación de workers es un error garrafal. Para ello tenemos que:
- CPU
- 6 Usuarios / Worker