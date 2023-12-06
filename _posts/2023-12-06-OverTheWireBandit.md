---
title: OverTheWire - Bandit Walkthrough
date: 2023-12-06
image: ../../assets/img/BANDIT/banditBanner.jpg
categories:
  - OverTheWire
tags:
  - OverTheWire
  - Bandit
  - CTF
---

En este post vamos a realizar todos los niveles del CTF [**OverTheWire: bandit**](https://overthewire.org/wargames/bandit/) que actualmente van desde el 0 hasta el 33.

## Nivel 0:

Para empezar el primer nivel debemos conectarnos por SSH a la siguiente direccion 'bandit.labs.overthewire.org' y mediante el usuario **bandit0**, contraseña **bandit0** y puerto **2220**.

Con el siguiente **oneliner** podemos conectarnos:

```bash
sshpass -p 'bandit0' ssh bandit0@bandit.labs.overthewire.org -p 2220
```

![bandit0_1](/assets/img/BANDIT/bandit0_1.png)

En el directorio donde nos encontramos hay archivo llamado **readme** que contiene la contraseña de **bandit1**
![bandit0_2](/assets/img/BANDIT/bandit0_2.png)

## Nivel 1 -> 2

Salimos de la sesión ssh de bandit0 y con el siguiente comando nos conectamos como **bandit1**:

```bash
sshpass -p 'NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL' ssh bandit1@bandit.labs.overthewire.org -p 2220
```

En el directorio actual existe un archivo llamado literalmente "-", como vemos, si le hacemos un simple **cat** en principio no nos dejará, ya que la terminal piensa que le vamos a asignar un parámetro al comando.

Os dejo las diferentes formas que tenemos de ver el contenido del archivo:
![bandit1_1](/assets/img/BANDIT/bandit1_1.png)

## Nivel 2 -> 3

Al conectarnos como **bandit2** nos damos cuenta de que dentro del directorio actual hay un archivo llamado **"spaces in this file"**, para ver el contenido simplemente escribiremos "cat s" y pulsamos la tecla **Tab**, o escribimos `cat spaces*`.
![bandit2_1](/assets/img/BANDIT/bandit2_1.png)

## Nivel 3 -> 4

La contraseña para el siguiente nivel se encuentra en un archivo oculto dentro del directorio **inhere**, los archivos ocultos en sistemas Linux empiezan con un ".".

Para  ver los archivo ocultos usamos el comando `ls -la`:
![bandit3_1](/assets/img/BANDIT/bandit3_1.png)

Con este comando lo hariamos de una forma mas "certera", ya que buscamos los archivos con el comando `find` y filtramos para que solo nos muestre el archivo **.hidden** y le hacemos un `cat` de forma paralela con el comando `xargs`:
![bandit3_2](/assets/img/BANDIT/bandit3_2.png)


## Nivel 4 -> 5

La contraseña para el siguiente nivel se encuentra en el único archivo legible en el directorio **inhere**:

Con el comando `find` buscaremos los archivos legibles y le haremos un `file` de forma paralela con `xargs`, para ver el tipo de cada archivo
![bandit4_1](/assets/img/BANDIT/bandit4_1.png)

Como vemos, hay un archivo que es de tipo **ASCII text**, vamos a mostrar su contenido, con el siguiente **oneliner**:
![bandit4_2](/assets/img/BANDIT/bandit4_2.png)


## Nivel 5 -> 6

La contraseña para el siguiente nivel se encuentra en un archivo dentro del directorio **inhere** y tiene las siguientes propiedades:

- legible por humanos
- 1033 bytes de tamaño
- No es ejecutable

Si buscamos por archivo legibles, no saldrán muchos:
![bandit5_1](/assets/img/BANDIT/bandit5_1.png)

Pero si le añadimos el parámetro `-size` y le pasamos que el archivo tiene un tamaño de 1033 bytes **(se representa con la letra "c" en find)**, solo nos devuelve un archivo:

![bandit5_2](/assets/img/BANDIT/bandit5_2.png)

Vamos a mostrar su contenido:
![bandit5_3](/assets/img/BANDIT/bandit5_3.png)

(Borramos los espacios con el comando `tr` ya que en la salida hay muchos espacios)


## Nivel 6 -> 7

La contraseña para el siguiente nivel se encuentra en algún lugar del servidor y tiene las siguientes propiedades:

- El usuario propietario es **bandit7**.
- El grupo propietario es **bandit6**.
- Tiene 33 bytes de tamaño.

Usaremos el comando `find` como en el nivel anterior para buscar en el sistema archivo con usuario propietario **bandit7**, grupo propietario **bandit6**, y 33 bytes de tamaño, y la hacemos un `cat`:
![bandit6_1](/assets/img/BANDIT/bandit6_1.png)


## Nivel 7 -> 8

La contraseña para el siguiente nivel se encuentra dentro de archivo **data.txt** al lado de la palabra **millionth**:
![bandit7_1](/assets/img/BANDIT/bandit7_1.png)

Vamos a filtrar para que solo nos muestre la contraseña:
![bandit7_2](/assets/img/BANDIT/bandit7_2.png)


## Nivel 8 -> 9

La contraseña para el siguiente nivel se encuentra en el archivo **data.txt** y está en la línea que ocurre una vez.

Con el comando `sort` y `uniq` podremos ver la línea que solo está escrita una vez en el archivo:
![bandit8_1](/assets/img/BANDIT/bandit8_1.png)

## Nivel 9 -> 10

La contraseña para el siguiente nivel se encuentra en el archivo **data.txt** en una de las pocas cadenas legibles, al lado de varios carateres '=', con el comando `grep` buscamos caracteres "=" dentro del archivo y luego imprimimos la última línea y filtramos por la última palabra de la línea on `awk`:
![bandit9_1](/assets/img/BANDIT/bandit9_1.png)

## Nivel 10 -> 11

La contraseña para el siguiente nivel se encuentra en el archivo **data.txt**, el cuál contiene texto codificado en **base64**, usaremos el comando `base64 -d` para decodificar el contenido del archivo:
![bandit10_1](/assets/img/BANDIT/bandit10_1.png)

## Nivel 11 -> 12

Este nivel ya es un poco más complejo la contraseña se encuentra en el archivo **data.txt**, donde las letras se han girado **13 posiciones**, un cifrado llamado [ROT13](https://es.wikipedia.org/wiki/ROT13).

En este nivel usaremos el comando `tr` para manipular el texto y dar con la clave:
![bandit11_1](/assets/img/BANDIT/bandit11_1.png)

## Nivel 12 -> 13

Tal cuál vamos subiendo de nivel, la cosa se pone más complicada, en este nivel la contraseña se encuentra en el archivo **data.txt**, el cuál es un volcado hexadecimal de un archivo comprimido repetidamente.
![bandit12_1](/assets/img/BANDIT/bandit12_1.png)

Para trabajar más cómodos, copiaremos el archivo y nos lo llevaremos a nuestra máquina.

Y gracias al comando `xxd` pasaremos el volcado decimal a un archivo binario, y con `sponge` lo guarda todo al archivo **data**:
![kali_1](/assets/img/BANDIT/kali_1.png)

Como vemos el contenido es ilegible, ya que es un comprimido, lo podemos comprobar con el comando `file`:
![kali_2](/assets/img/BANDIT/kali_2.png)

El nivel se basa en ir descomprimiendo archivo hasta dar con el que es de tipo **ASCII text** contiene la contraseña, pero os dejaré un script el cuál os automatiza todas las descompresiones:

```bash
#!/bin/bash

ctrl_c(){
  echo -e "\n\n[!] Saliendo...\n"
  exit 1
}

trap ctrl_c SIGINT

primercomprimido="data"
descomprimir="$(7z l $primercomprimido | tail -n 3 | head -n 1 | awk 'NF{print $NF}')"

7z x $primercomprimido &>/dev/null

while [ $descomprimir ]; do
  echo -e "\n[+] Nuevo archivo descomprimido: $descomprimir"
  7z x $descomprimir &>/dev/null
  descomprimir="$(7z l $descomprimir 2>/dev/null | tail -n 3 | head -n 1 | awk NF'{print $NF}')"
done
```
El script nos ahorra la reiterada tarea de ir descomprimiendo cada archivo, la variable **primercomprimido** se debe llamar como el archivo que iba después de `sponge`.

![kali_3](/assets/img/BANDIT/kali_3.png)


Ejecutamos el script y el último archivo que fue descomprimido fue **data8.bin** que contenía a **data9.bin** el cual es legible y contiene la contraseña.

## Nivel 13 -> 14

La contraseña para el siguiente nivel se encuentra en el archivo `etc/bandit_pass/bandit14` y puede ser leído por el usuario bandit14, no necesitamos conseguir la contraseña, pero si acceder al siguiente nivel con una **clave privada SSH**, la clave se encuentra en el directorio actual y se llama **sshkey.private**:

![bandit13_1](/assets/img/BANDIT/bandit13_1.png)
Con el comando `ssh -i` podremos usar esta clave privada para conectarnos como el usuario **bandit14**:

![bandit13_2](/assets/img/BANDIT/bandit13_2.png)
Tras ejecutar este comando, estaremos como el usuario **bandit14**.

![bandit13_3](/assets/img/BANDIT/bandit13_3.png)
Y podremos ver la contraseña del usuario.


## Nivel 14 -> 15

En este nivel nos piden que enviemos la contraseña de **bandit14** el puerto 30000 de **localhost**, usaremos los comandos `cat` y [`nc`](https://linux.die.net/man/1/nc)

![bandit14_1](/assets/img/BANDIT/bandit14_1.png)
Mostramos el contenido del archivo **/etc/bandit_pass/bandit14** que contiene la contraseña del usuario y lo enviamos el puerto 30000 de **localhost**.


## Nivel 15 -> 16

Este nivel es similar al anterior, deberemos enviar la contraseña del nivel actual al puerto 30001 de **localhost** mediante **cifrado SSL**.

Usaremos el comando **openssl** con el parámetro **s_client** para conectarnos al puerto 30001 y enviaremos la contraseña:
![bandit15_1](/assets/img/BANDIT/bandit15_1.png)

Una vez puesto este comando nos pedirá la contraseña:
![bandit15_2](/assets/img/BANDIT/bandit15_2.png)

La pegamos y nos devuelve la contraseña del siguiente nivel:
![bandit15_3](/assets/img/BANDIT/bandit15_3.png)

## Nivel 16 -> 17

La contraseña para el siguiente nivel se puede recuperar enviando la contraseña del nivel a un puerto en localhost **entre el 31000 y el 32000.**  y está usando SSL para la conexión:

Primero usaremos el comando **nmap** para escanear los puertos de la máquina en ese rango:
![bandit16_1](/assets/img/BANDIT/bandit16_1.png)

Así que ahora probaremos puerto por puerto, el siguiente comando `openssl s_client -connect localhost:<puerto>` hasta recibir la respuesta correcta:
![bandit16_2](/assets/img/BANDIT/bandit16_2.png)

El puerto **31790** nos ha devuelto una clave privada, usaremos esta misma para conectarnos al usuario **bandit17**.
![bandit16_3](/assets/img/BANDIT/bandit16_3.png)

Vamos a crear un directorio temporal para poder crear archivos.
![bandit16_4](/assets/img/BANDIT/bandit16_4.png)

Y vamos a volcar esa clave privada en el archivo **id_rsa**
![bandit16_5](/assets/img/BANDIT/bandit16_5.png)

Antes de conectarnos debemos darle los permisos **600** al archivo ya que de otra forma no nos permitirá conectarnos


## Nivel 17 -> 18

Para descubrir la contraseña deberemos descubrir la única línea que se ha cambiado entre los archivos passwords.old y passwords.new.

En este nivel usaremos el comando `diff` que nos permite comparar línea por línea archivos para ver que líneas les diferencian.
![bandit17_1](/assets/img/BANDIT/bandit17_1.png)

Como vemos, nos muestra que línea diferencia a un archivo de otro al contraseña del siguiente nivel es el segundo resultado.

## Nivel 18 -> 19

La contraseña se encuentra en el archivo **readme**, el problema es que algo ha sido modificado en el archivo **.bashrc** del usuario y no expulsa cuando nos conectamos, pero `ssh` nos permite ejecutar comandos recién nos conectamos, con el parámetro **command**
![kali_4](/assets/img/BANDIT/kali_4.png)

Así que usaremos el comando `bash` para poder lanzarnos una consola.
![kali_5](/assets/img/BANDIT/kali_5.png)

Y mediante este comando hemos podido ejecutar una **bash** como ese usuario.


## Nivel 19 -> 20

En el siguiente nivel nos encontramos un binario llamado **bandit20-do** que tiene permisos SUID, esto quiere decir que **bandit19** puede ejecutar este binario como **bandit20** de forma temporal.
![bandit19_1](/assets/img/BANDIT/bandit19_1.png)

![bandit19_2](/assets/img/BANDIT/bandit19_2.png)

Esto nos permite, entre muchas cosas, tanto mostrar la contraseña del usuario  **bandit20**, como lanzarnos una bash como ese usuario.

## Nivel 20 -> 21

Al conectarnos a la máquina, nos damos cuenta que existen un binario llamado **suconnect** el cual tiene permisos SUID y el propietario es **bandit21**:
![bandit20_1](/assets/img/BANDIT/bandit20_1.png)

Este binario establece una conexión con el host local en el puerto que le especifiquemos como argumento, lee una línea que le pasemos la compara con la contraseña de **bandit20**, si es correcta nos devolverá la contraseña de **bandit21**.

Este nivel usaremos dos consolas con la sesión ssh de bandit20 iniciada, en una nos pondremos en escucha por el puerto **4646**, en la otra usaremos el binario para conectarnos a ese puerto.
![bandi20_2](/assets/img/BANDIT/bandit20_2.png)

En la ventana de abajo, si pegamos la contraseña, nos devolverá otra, que corresponde a **bandit21**
![bandi20_3](/assets/img/BANDIT/bandit20_3.png)


## Nivel 21 -> 22

En este nivel empezamos a jugar con tareas [cron](https://www.hostinger.es/tutoriales/cron-job), que básicamnte son tareas que se puede programar para que se ejecuten periódicamente en el sistema.
![bandit21_1](/assets/img/BANDIT/bandit21_1.png)

Como vemos en el directorio **/etc/cron.d/** tenemos varios archivos, el que nos interesa es **cronjob_bandit22**.
![bandit21_2](/assets/img/BANDIT/bandit21_2.png)

Si mostramos su contenido, podemos ver que se está ejecutando un script llamado **cronjob_bandit22.sh** de forma periódica (en este caso cada minuto)
![bandit21_3](/assets/img/BANDIT/bandit21_3.png)

Y este es el contenido del script, básicamente le está asignando unos permisos al archivo y luego está volcando la contraseña de **bandit22** en él.
![bandit21_4](/assets/img/BANDIT/bandit21_4.png)

Mostramos el contenido con el comando `cat` y ya tenemos la contraseña.

## Nivel 22 -> 23

En este nivel también se están usando tareas [cron](https://www.hostinger.es/tutoriales/cron-job).
![bandit21_1](/assets/img/BANDIT/bandit22_1.png)

En este caso nos interesa **cronjob_bandit23**, ya que es el siguiente nivel.
![bandit22_2](/assets/img/BANDIT/bandit22_2.png)

Este scripts crea una variable **myname** mediante esa crea un **hash md5** y crea archivo donde vuelca la contraseña de lo que valga en este caso **whoami**, vamos a ver como vulnerar esto:
![bandit22_3](/assets/img/BANDIT/bandit22_3.png)

Si ejecutamos el script, obtendremos la contraseña de **bandit22**, ya que es lo que devuelve el comando `whoami`.
![bandit22_4](/assets/img/BANDIT/bandit22_4.png)

Así que copiaremos el comando que se almacena en la variable **mytarget** y cambiaremos el valor de la variable **myname** por **bandit23**, nos devuelve un hash, que es el nombre de archivo que contiene la contraseña de **bandit23**:
![bandit22_5](/assets/img/BANDIT/bandit22_5.png)

## Nivel 23 -> 24

Otro nivel con tareas **cron**:

![bandit23_1](/assets/img/BANDIT/bandit23_1.png)
Este script básicamente lo que hace es ejecutar todo lo que se encuentre en la ruta **/var/spool/bandit24/foo** como **bandit24** así que ya más o menos tenemos un idea de lo que podemos hacer para conseguir la contraseña.

![bandit23_2](/assets/img/BANDIT/bandit23_2.png)
Primero he creado un directorio temporal con `mktemp`.

![bandit23_3](/assets/img/BANDIT/bandit23_3.png)
Y he creado un script el cuál nos vuelca la contraseña de **bandit24** en el directorio actual, en un archivo llamado **bandit24pass.txt**.

![bandit23_4](/assets/img/BANDIT/bandit23_4.png)
El siguiente paso será dar permisos de lectura y ejecución al script para que **bandit24** lo pueda ejecutar y permiso `777` al directorio temporal.

![bandit23_5](/assets/img/BANDIT/bandit23_5.png)
Copiamos el script a la ruta que veíamos en la tarea cron.

![bandit23_5](/assets/img/BANDIT/bandit23_5.png)
Si ejecutamos reiteradamente cada segundo `ls -l` con el comando `watch -n 1 ls -l` veremos que al pasar un minuto aparece el archivo.

![bandit23_6](/assets/img/BANDIT/bandit23_6.png)
Y el archivo contiene la contraseña de **bandit24**.

## Nivel 24 -> 25

En este nivel tenemos un servicio **(daemon)** escuchando en el puerto **30002**, y nos devolverá la contraseña de **bandit25** si le pasamos la contraseña de bandit24 y un PIN secreto de 4 dígitos, no hay forma de averiguar ese PIN así que deberemos hacer fuerza bruta.
![bandit24_1](/assets/img/BANDIT/bandit24_1.png)

Mediante este **script** creamos un archivo llamado **combinaciones.txt** el cuál tiene conjunto de contraseña:PIN para realizar la fuerza bruta.
![bandit24_2](/assets/img/BANDIT/bandit24_2.png)

Y con el siguiente comando comenzaremos a realizar fuera bruta enviando ese archivo hacia el puerto 30002 de localhost hasta dar con la combinación correcta.

## Nivel 25 -> 26

En este nivel nos dicen que debería ser fácil conectarnos como **bandit26**, pero su shell no es **/bin/bash**, tenemos que descubrir que es y como funciona y como vulnerarlo.

![bandit25_1](/assets/img/BANDIT/bandit25_1.png)
Tenemos su clave privada, pero de alguna forma, nos expulsa al cconectarnos:

![bandit25_2](/assets/img/BANDIT/bandit25_2.png)

![bandit25_3](/assets/img/BANDIT/bandit25_3.png)

Si buscamos que tipo de **shell** tiene bandit26, vemos que tiene una llamada **showtext**, vamos a ver que es:
![bandit25_4](/assets/img/BANDIT/bandit25_4.png)

Este script causa que cuando nos conectamos, ejecuta un `more` hacia un archivo llamado **text.txt** que es el banner que nos muestra al conectarnos, y luego nos expulsa.
![bandit25_5](/assets/img/BANDIT/bandit25_5.png)

Para conectarnos como bandit26, tenemos que aprovechar el `more`, para ello debemos conectarnos con la clave privada y hacer la terminal pequeña hasta que nos salga algo así, estamos dentro del comando `more` que se ejecutaba al conectarnos a **bandit26**.
![bandit25_6](/assets/img/BANDIT/bandit25_6.png)

Pulsamos la tecla `v` para poder interactuar con more y escribiremos lo siguiente, que nos mostrará la contraseña de **bandit26**.
![bandit25_7](/assets/img/BANDIT/bandit25_7.png)

![bandit25_8](/assets/img/BANDIT/bandit25_8.png)
Pero en este caso nos interesa 'spawnearnos' una shell como **bandit26**, lo haremos con este comando.

![bandit25_9](/assets/img/BANDIT/bandit25_9.png)
Y ahora escribimos **:shell**.

![bandit25_10](/assets/img/BANDIT/bandit25_10.png)
Ya tenemos una shell como bandit26.



## Nivel 26 -> 27

Este nivel es más sencillo, tenemos un ejecutable con SUID llamado **bandit27-do**, el cuál ejecuta comandos como **bandit27**, ya sabemos como pasarnos esto:

![bandit26_1](/assets/img/BANDIT/bandit26_1.png)

Ya tenemos la contraseña de **bandit27**.

## Nivel 27 -> 28

Ahora vamos a jugar con repositorios de git, este caso está en **ssh://bandit27-git@localhost/home/bandit27-git/repo** en el puerto **2220**, la contraseña para el usuario **bandit27-git** es la misma que para **bandit27**.
![bandit27_1](/assets/img/BANDIT/bandit27_1.png)

Nos creamos un directorio temporal y ejecutamos este comando para clonarnos el **repositorio**, le pasamos la contraseña:
![bandit27_2](/assets/img/BANDIT/bandit27_2.png)

Y nos lo clona.
![bandit27_3](/assets/img/BANDIT/bandit27_3.png)

## Nivel 28 -> 29

En este nivel seguimos con repositorios, pero en este vamos a jugar con versiones.
![bandit28_1](/assets/img/BANDIT/bandit28_1.png)

Como vemos el campo no está.
![bandit28_2](/assets/img/BANDIT/bandit28_2.png)

Con log podemos ver todos los cambios y **commits** han realizado en este repositorio, vemos que en el segundo se agregó datos que faltaban, y en el último 'arreglaron la fuga de datos', así que vamos a volver a la segunda versión.
![bandit28_3](/assets/img/BANDIT/bandit28_3.png)

Usamos el comando `git checkout` para esto, y ya vemos la contraseña en el archivo **README.md**.

## Nivel 29 -> 30

Otro nivel usando repositorios, pero en este caso jugaremos con las [branch](https://keepcoding.io/blog/que-es-git-branch/#:~:text=Git%20Branch%20se%20define%20como,m%C3%BAltiples%20versiones%20de%20una%20aplicaci%C3%B3n.) o ramas en Git.

![bandit29_1](/assets/img/BANDIT/bandit29_1.png)
Nos clonamos el repositorio de **bandit29**,

![bandit29_2](/assets/img/BANDIT/bandit29_2.png)
En este nivel no nos servirá cambiar de versión del repositorio.

![bandit29_3](/assets/img/BANDIT/bandit29_3.png)
Así que usamos el comando `git branch` que nos permite crear, listar o eliminar ramas en Git, entre todas, nos llama la atención la rama de **dev**.

![bandit29_4](/assets/img/BANDIT/bandit29_4.png)
Así que usamos el comando `git checkout dev` para cambiarnos a esa rama, y ya podemos ver la contraseña.

## Nivel 30 -> 31

En este nivel usaremos las [tags](https://www.atlassian.com/es/git/tutorials/inspecting-a-repository/git-tag) de git para dar con la contraseña.

![bandit30_1](/assets/img/BANDIT/bandit30_1.png)
Nos creamos el directorio temporal y nos clonamos el repositorio como en los ejercicios anteriores.

![bandit30_2](/assets/img/BANDIT/bandit30_2.png)
En el **README.md** no podemos ver nada de valor

![bandit30_3](/assets/img/BANDIT/bandit30_3.png)
Tampoco en los **logs** ni en las **ramas**.

![bandit30_4](/assets/img/BANDIT/bandit30_4.png)
Si usamos el comando `git tag` para listar las etiquetas podemos ver una que se llama **secret**.

![bandit30_5](/assets/img/BANDIT/bandit30_5.png)
Y con el comando `git show secret` podemos mostrar esa etiqueta, que es la contraseña para el siguiente nivel.


## Nivel 31 -> 32

En este nivel consiste en enviar con `git push` un archivo con un contenido en concreto para que nos devuelva la contraseña del siguiente nivel.

![bandit31_1](/assets/img/BANDIT/bandit31_1.png)
Como vemos, nos pide que creemos un archivo llamado **key.txt** con el contenido **May I come in?** y enviar como la rama **master**.

![bandit31_2](/assets/img/BANDIT/bandit31_2.png)
Así que creamos el archivo, le agregamos el contenido y hacemos un `git add`, pero como vemos, existe un archivo llamado **.gitignore** el cuál no nos está permitiendo añadir el archivo.

![bandit31_3](/assets/img/BANDIT/bandit31_3.png)
No nos permite añadir todo lo que sea **.txt**, así que lo borramos.

![bandit31_4](/assets/img/BANDIT/bandit31_4.png)
Ahora si, añadimos el archivo y le agregamos un **commit**, y le hacemos un `git push -u origin master`.

![bandit31_5](/assets/img/BANDIT/bandit31_5.png)

![bandit31_6](/assets/img/BANDIT/bandit31_6.png)
Y nos devuelve la contraseña para el siguiente nivel.



## Nivel 32 -> 33 (Final)

Último nivel.

![bandit32_1](/assets/img/BANDIT/bandit32_1.png)
Al conectarnos, nos muestran un mensaje de que estamos en una **UPPERCASE SHELL**.

![bandit32_2](/assets/img/BANDIT/bandit32_2.png)
Y todo lo que escribamos se convierte en mayúsculas, así que no podremos ejecutar ningún comando.

Como sabemos, la variable **$0** referencia a la shell o script actual que se está ejecutando, así que si escribimos esto en la terminal, ya que estamos ejecutando **bash**, conseguimos ejecutarlo y tener una consola.
![bandit32_3](/assets/img/BANDIT/bandit32_3.png)

![bandit32_4](/assets/img/BANDIT/bandit32_4.png)
Y por aquí termina el post de hoy, muy interesante para empezar a trastear con Linux y en el mundo del Hacking, espero que os haya gustado y lo hayáis disfrutado, nos vemos en el siguiente, **¡un saludo :)!**.

---

# **Contraseñas de cada nivel**

| Usuario      | Contraseña                       |
| ------------ | -------------------------------- |
| **bandit0**  | bandit0                          |
| **bandit2**  | rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi |
| **bandit3**  | aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG |
| **bandit4**  | 2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe |
| **bandit5**  | lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR |
| **bandit6**  | P4L4vucdmLnm8I7V7jG1ApGSfjYKqJU  |
| **bandit7**  | z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S |
| **bandit8**  | TESKZC0XvTetK0S9xNwm25STk5iWrBvP |
| **bandit9**  | EN632PlfYiZbn3PhVK3XOGSlNInNE00t |
| **bandit10** | G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s |
| **bandit11** | 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM |
| **bandit12** | JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv |
| **bandit13** | wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw |
| **bandit14** | fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq |
| **bandit15** | jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt |
| **bandit16** | JQttfApK4SeyHwDlI9SXGR50qclOAil1 |
| **bandit17** | VwOSWtCA7lRKkTfbr2IDh6awj9RNZM5e |
| **bandit18** | hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg |
| **bandit19** | awhqfNnAbc1naukrpqDYcF95h7HoMTrC |
| **bandit20** | VxCazJaVykI6W36BkBU0mJTCM8rR95XT |
| **bandit21** | NvEJF7oVjkddltPSrdKEFOllh9V1IBcq |
| **bandit22** | WdDozAdTM2z9DiFEQ2mGlwngMfj4EZff |
| **bandit23** | QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G |
| **bandit24** | VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar |
| **bandit25** | p7TaowMYrmu23Ol8hiZh9UvD0O9hpx8d |
| **bandit26** | c7GvcKlw9mC7aUQaPx7nwFstuAIBw1o1 |
| **bandit27** | YnQpBuifNMas1hcUFk70ZmqkhUU2EuaS |
| **bandit28** | AVanL161y9rsbcJIsFHuw35rjaOM19nR |
| **bandit29** | tQKvmcwNYcFS6vmPHIUSI3ShmsrQZK8S |
| **bandit30** | xbhV3HpNGlTIdnjUrdAlPzc2L6y9EOnS |
| **bandit31** | OoffzGDlzhAlerFJ2cAiz1D41JW1Mhmt |
| **bandit32** | rmCBvG56y58BXzv98yZGdO7ATVL5dW8y |
| **bandit33** | odHo63fHiFqcWWJG9rLiLDtPm45KzUKy |
