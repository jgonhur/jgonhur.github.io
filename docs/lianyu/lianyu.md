# Lian Yu

# Enumeración de puertos

Normalmente ejecutaremos el siguiente escaneo para la enumeración de puertos:

```bash
sudo nmap -sS -T5 <ipdelamaquina>
```

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled.png)

### Puertos

ssh port: 22

ftp port: 21

http port: 80

rpcbind port: 111

# Directory fuzzing

Vamos a intentar acceder a algún directorio oculto de la máquina haciendo fuerza bruta, mediante un diccionario:

```bash
gobuster dir -u http://<ip_maquina> -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%201.png)

**Directorios encontrados con éxito:**

> /island
> 

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%202.png)

Miramos el código fuente y encontramos una **pista**:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%203.png)

Ahora vamos a intentar profundizar a partir de este directorio:

```bash
gobuster dir -u [http://<ipmaquina>/island](http://10.10.83.36/island) -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%204.png)

**Directorios encontrados con éxito:**

> /island/2100
> 

Inspeccionamos el código fuente de la página y vemos otra pista en el comentario. Nos dice algo de un ticket:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%205.png)

Probaremos a volver a buscar dentro del directorio web con .ticket como extensión:

```bash
gobuster dir -u [http://<ipmaquina>/island/2100](http://10.10.83.36/island/2100) -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .ticket
```

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%206.png)

En este fichero encontramos un token:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%207.png)

Buscando el posible cifrado hemos encontrado que con Base 58 obtenemos un resultado coherente:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%208.png)

# FTP

## Acceso al FTP

Con la **[pista](https://www.notion.so/Lian-Yu-4099d05c64cd49f5be2f30286eb2973c)** que hemos encontrado en el directory fuzzing obtenemos el usuario del FTP:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%209.png)

Con el **ticket** obtenido el fichero /island/2100/green_arrow.ticket vamos a probar (una vez descifrado) a acceder al FTP:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2010.png)

Obtenemos acceso al FTP y ahora exploraremos los directorios.

Encontramos tres imágenes en el directorio raíz, y las descargamos:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2011.png)

# **Análisis de ficheros**

## Números mágicos

Nos encontramos con tres imágenes en el FTP. Las hemos descargado y vemos que una de ellas no se puede visualizar:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2012.png)

Cambiamos los números mágicos de la copia (aconsejable para no tener que acceder al FTP) del fichero Leave_me_alone.png:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2013.png)

Los números mágicos de un archivo .png son:

> **89 50 4E 47 0D 0A 1A 0A**
> 

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2014.png)

Obtenemos la siguiente imagen:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2015.png)

## Esteganografía

A continuación extraeremos información de la imagen aa.jpg con steghide y usamos el salvoconducto proporcionado por la imagen de arriba:

```bash
steghide --extract -sf aa.jpg
```

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2016.png)

Obtenemos un archivo comprimido llamado ss.zip.

Ahora lo descomprimimos y obtenemos el directorio ss:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2017.png)

En passwd.txt encontramos un texto:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2018.png)

En shado encontramos lo que parece un salvoconducto o contraseña:

![Untitled](Lian%20Yu%204099d05c64cd49f5be2f30286eb2973c/Untitled%2019.png)