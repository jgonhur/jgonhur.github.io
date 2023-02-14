# Blue

# Enumeración de servicios

Ejecutaremos el siguiente escaneo para la búsqueda de vulnerabilidades en los servicios:

```bash
nmap -p 445 -sV --script vuln 10.10.219.54
```

![Untitled](blue/Untitled.png)

**CVE**

> CVE-2017-0143
> 

**Puertos y servicios**

> SMB port: 445/tcp
> 

# Explotación de la vulnerabilidad

## SMB exploiting

Abrimos msfconsole y buscamos el CVE, usamos eternalblue:

![Untitled](blue/Untitled%201.png)

Asignamos LHOST a la IP de nuestra VPN:

![Untitled](blue/Untitled%202.png)

Asignamos el RHOSTS a la IP de la máquina:

![Untitled](blue/Untitled%203.png)

Asignamos el payload que vamos a usar, en este caso la reverseshell:

![Untitled](blue/Untitled%204.png)

Explotamos

![Untitled](blue/Untitled%205.png)

Dejamos en background

![Untitled](blue/Untitled%206.png)

## Upgrade shell to meterpreter

Buscamos shell_to_meterpreter y cambiamos el payload:

![Untitled](blue/Untitled%207.png)

Listamos las sesiones:

![Untitled](blue/Untitled%208.png)

Seleccionamos la sesión que vamos a utilizar y ejecutamos el exploit:

![Untitled](blue/Untitled%209.png)

Abriendo la sesión de meterpreter (se abre sola, sólo tenemos que ver cuál es la de meterpreter)

![Untitled](blue/Untitled%2010.png)

E iniciamos la sesión de meterpreter:

![Untitled](blue/Untitled%2011.png)

## Crackeando contraseñas

Con hashdump podemos hacer un volcado de los hashes de cada usuario de windows:

![Untitled](blue/Untitled%2012.png)

Creamos un directorio para la máquina de THM. Dentro de él guardamos los hashes en un fichero llamado hashes

![Untitled](blue/Untitled%2013.png)

## Búsqueda de flags

John the ripper tiene una opción para crackear los hashes de NT:

john --wordlist=~/diccionarios/rockyou.txt --format=NT hashes

![Untitled](blue/Untitled%2014.png)

Una vez hemos extraído la contraseña, nos vamos hasta el directorio C:

![Untitled](blue/Untitled%2015.png)

Encontramos la primera flag.

Una vez hecho esto nos vamos a C:\Windows\system32\config que es donde están almacenados los registros:

![Untitled](blue/Untitled%2016.png)

Encontramos la segunda flag.

A continuación, entraremos en el directorio ubicado en C:\Users\Jon\Documents:

![Untitled](blue/Untitled%2017.png)

Encontramos la última flag.