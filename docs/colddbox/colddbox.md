# ColddBox

# Enumeración de puertos

Realizamos un escaneo intensivo con el siguiente comando para escanear todos los puertos y obtener además las versiones de los servicios:

```bash
nmap -sC -sV -p- <ipmaquina> -Pn
```

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled.png)

### Puertos

> **SSH**: 4512
> 

> **HTTP**: 80
> 

### Servicios HTTP

> Wordpress 4.1.31
> 

> Apache/2.4.18
> 

### Servicio SSH

> OpenSSH 7.2p2 Ubuntu
> 

# WordPress Scan

Realizaremos un escaneo de las vulnerabilidades de esta versión de WordPress con la herramienta wpscan:

```bash
wpscan --url http://<ipmaquina>
```

## User enumeration

Enumeraremos los usuarios, con el siguiente comando:

```bash
wpscan --url [http://](http://10.10.38.110/)<ipmaquina> --enumerate u
```

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%201.png)

Usuarios:

- c0ldd
- hugo
- philip

Escogemos un usuario, por ejemplo c0ld, e intentamos aplicar el diccionario rockyou.txt  a su contraseña con wpscan:

```bash
wpscan --url http://http://<ipmaquina>/ --usernames c0ldd --passwords /usr/share/wordlists/rockyou.txt
```

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%202.png)

Obtenemos la contraseña del usuario c0ldd.

## User enumeration

A continuación veremos cómo realizar la enumeración de usuarios y el uso de la wordlist para las contraseñas simultáneamente:

```bash
wpscan --url [http://](http://10.10.38.110/)<ipmaquina> --passwords /usr/share/wordlists/rockyou.txt 
```

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%203.png)

# Escalada de privilegios

Nos loggeamos con el usuario c0ldd en el wordpress y, como podemos ver, tenemos acceso a la subida de archivos:

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%204.png)

## Reverse shell

Nos [descargamos](https://pentestmonkey.net/tools/web-shells/php-reverse-shell) la reverse shell de php de pentest monkey y cambiamos los campos pertinentes:

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%205.png)

Introducimos la **[IP de nuestra interfaz WAN](https://www.cual-es-mi-ip.net/)** y el **puerto** en el que vamos a escuchar como 8080.

Ahora nos ponemos a la escucha con netcat para cazar la comunicación:

```bash
sudo nc -nlvp 8080
```

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%206.png)

Y subimos nuestra reverse shell al wordpress desde los plugins, porque desde media no nos lo permite.

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%207.png)

Una vez subido e instalado, copiamos la URL que nos proporciona y la ejecutamos, en el buscador:

> /wp-content/uploads/2022/12/php-reverse-shell.php
> 

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%208.png)

Una vez ha interceptado netcat la comunicación mediante el puerto 8080, nos generará una reverse shell:

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%209.png)

Una vez en la reverse shell, intentaremos encontrar los ficheros que nos pide la máquina de TryHackMe, pero no tenemos permiso para abrirlo:

![Untitled](ColddBox%20130c95b22c1d4dbc8955cb52795c72f6/Untitled%2010.png)

No nos deja abrirlo con cat ni con vim.

Ejecutamos sudo -l sin resultado alguno, en busca de algún programa que podamos usar con los privilegios actuales.