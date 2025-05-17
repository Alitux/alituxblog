+++
title = "Tuneles Cloudflare en una Raspberry Pi"
date = "2024-11-12T11:19:13-03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Alitux"
authorTwitter = "alituxtdf" #do not include @
cover = ""
tags = ["Raspberry Pi", "Cloudflare"]
keywords = ["", ""]
description = "Configurar túneles de Cloudflare en una Raspberry Pi permite exponer servicios locales a Internet de forma segura sin abrir puertos."
showFullContent = false
readingTime = false
hideComments = false
draft = false
+++

Hace tiempo que vengo pensando como exponer a Internet servicios alojados localmente. En su momento hice un injerto con wireguard y una VPS que logró su cometido a costa de un rendimiento espantoso.

Hace unos días se me ocurrió probar usando los túneles de Cloudflare y quedé gratamente sorprendido por la perfo y la sencillez. Acá les paso un Tuto tomando como modelo una Raspberry Pi 3 con Raspberry Pi OS en la que funciona un nextcloud sobre Docker. Acá les dejo el [compose](https://gist.github.com/Alitux/c21f175a42ebddf7e838437570f5041d) por si les interesa pero no lo hago parte del tuto porque excede el alcance 

1. Vamos a instalar Cloudflared en nuestra Raspi:
```bash
curl -L https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-archive-keyring.gpg >/dev/null ; echo "deb [signed-by=/usr/share/keyrings/cloudflare-archive-keyring.gpg] https://pkg.cloudflare.com/cloudflared $(lsb_release -cs) main" | sudo tee  /etc/apt/sources.list.d/cloudflared.list<br>sudo apt update; sudo apt install cloudflared
```
2. Nos logueamos:

```bash
cloudflared tunnel login
```

Ese comando nos abrirá un enlace que debemos abrir en nuestro navegador web para autenticar el dispositivo y vincularlo a algún dominio (Por ejemplo: Pirulito.com.ar)

3. Una vez logueados vamos a crear un tunel

```bash
cloudflared tunnel create pirulito
```

4. Ahora vamos a crear un registro DNS para vincular el túnel al dominio. En mi caso me interesa utilizar el subdominio nextcloud.pirulito.com.ar

```bash
cloudflared tunnel route dns pirulito nextcloud.pirulito.com.ar
```

5. Ahora viene lo copado, vamos a levantar el túnel y probar si funca. En este caso hemos levantado nextcloud en el puerto 8080.
```bash
cloudflared tunnel run --url localhost:8080 pirulito
```
Si hiciste todo bien, al cabo de un minuto ya tendría que estar tu túnel levantado y podrías acceder a nextcloud.pirulito.com.ar desde cualquier lugar del mundo ¿Impresionante no? ¡CG-NAT Sucks!

Bueno, el tema es que hacer eso último cada vez que reinicias, prendés o apagas la Raspi es una paja, por lo que vamos a crear un servicio.

## Creando un Servicio de Sistema

1. Creamos el yaml de configuración
```bash
nano ~/.cloudflared/config.yml
```
¿Se acuerdan el ID que se guardaron en el bolsillo? Bueno, ahora es momento de que salga a la cancha. Dentro del archivo pegamos el siguiente contenido:
```yaml
tunnel: nextcloud
credentials-file: /home/servidor/.cloudflared/id-en-tu-bolsillo.json
 
ingress:
- hostname: nextcloud.alitux.com.ar
service: http://localhost:8080
- service: http_status:404
```

- tunnel: va el nombre del túnel que asignamos al principio. (Paso 3)
- credentials-file: Tiene que quedar el path armadito con los datos de tu pi y el id que te guardaste en el bolsillo. Es mucho importante este pasito porque no se baila como tu quieres, se baila como se tiene que bailar.
- hostname: El dominio al que estará vinculado el tunel.
- service: Se especifica el protocolo, localhost y el puerto que vamos a vincular. El protocolo puede ser: http, https, unix, unix+tls,tcp, ssh o rdp.

Guardamos todo con *CTRL+X* y creamos los servicios… Tranca que cloudflared tiene unas magias para que no lo tengamos que hacer a manopla. 

```bash
sudo cloudflared --config ~/.cloudflared/config.yml service install; sudo systemctl enable cloudflared; sudo systemctl start cloudflared
```
Y si todo sale bien ya deberíamos tener los servicios levantados como corresponde ¡Y listo el pollo!