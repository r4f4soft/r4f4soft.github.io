---
title: "OverTheWire - Bandit Walkthrough"
date: 2023-06-05
image: ../../assets/img/BANDIT/banditBanner.jpg
categories: [ OverTheWire ]
tags: [OverTheWire, Bandit, CTF]
---

En este post vamos a realizar todos los niveles del CTF `OverTheWire: bandit` que actualmente van desde el 0 hasta el 33.

### Nivel 0:
Para conectarnos a la máquina debemos conectarnos por SSH a la siguiente direccion 'bandit.labs.overthewire.org' y mediante el usuario **bandit0**, contraseña **bandit0** puerto **2220**.
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

Dentro encontraremos un fichero llamado **readme** el cuál contiene la password de **bandit1**
```bash
bandit0@bandit:~$ cat readme
NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL
```

### Nivel 1 -> 2:

Nos conectamos con la contraseña anterior a bandit1
```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

Como vemos existen un fichero llamado literalmente **-**, con el siguiente comando (hay otras formas de hacerlo) podremos ver su contenido
```bash
bandit1@bandit:~$ ls -la
total 24
-rw-r-----  1 bandit2 bandit1   33 Apr 23 18:04 -
drwxr-xr-x  2 root    root    4096 Apr 23 18:04 .
drwxr-xr-x 70 root    root    4096 Apr 23 18:05 ..
-rw-r--r--  1 root    root     220 Jan  6  2022 .bash_logout
-rw-r--r--  1 root    root    3771 Jan  6  2022 .bashrc
-rw-r--r--  1 root    root     807 Jan  6  2022 .profile
bandit1@bandit:~$ cat ./-
rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
```

### Nivel 2 -> 3:
Nos conectamos como usuario **bandit2**
```bash
sshpass -p 'rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi' ssh bandit2@bandit.labs.overthewire.org -p 2220
```

En el directorio personal de bandit2 se encuentra un fichero llamado **"spaces in this file"**
```bash
bandit2@bandit:~$ cat spaces\ in\ this\ filename
aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG
```

Simplemente pulsando la tecla Tab se autocompletará el nombre del archivo, también podemos escribirlo entre comillas "spaces in this filename"
```bash
bandit2@bandit:~$ cat "spaces in this filename"
aBZ0W5EmUfAf7kHQeOwd8bauFJ2lAiGT
```

### Nivel 3 -> 4:
Nos conectamos como usuario **bandit3**
```bash
sshpass -p 'aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG' ssh bandit3@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en un fichero oculto dentro del directorio inhere
Con el siguiente comando vamos a buscar todos los ficheros que hay en la carpeta personal, vamos a filtrar por resultados que no coincidan con "bashc | profile | logout" y vamos a hacer un cat de ese fichero con **xargs**

```bash
bandit3@bandit:~$ find . -type f | grep -vE "bashrc|profile|logout" | xargs cat
2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe
```

### Nivel 4 -> 5:
Nos conectamos como usuario **bandit4**
```bash
sshpass -p '2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe' ssh bandit4@bandit.labs.overthewire.org -p 2220
```

La contraseña se encuentra en el único archivo legible por humanos en el directorio **inhere**

```bash
bandit4@bandit:~$ find . -type f -readable | grep "\-file" | xargs file
./inhere/-file03: data
./inhere/-file06: data
./inhere/-file08: data
./inhere/-file07: ASCII text
./inhere/-file04: data
./inhere/-file00: data
./inhere/-file01: data
./inhere/-file02: data
./inhere/-file09: Non-ISO extended-ASCII text, with no line terminators
./inhere/-file05: data
```
Nos damos cuenta de que el fichero `-file07` es de tipo **ASCII text**, mostramos el contenido de ese fichero

```bash
bandit4@bandit:~$ find . -type f -readable | grep "\-file" | xargs file | grep "ASCII text$" | awk '{print $1}' FS=":" | xargs cat
lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR
```

### Nivel 5 -> 6:
Nos conectamos como usuario **bandit5**
```bash
sshpass -p 'lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR' ssh bandit5@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en un fichero dentro del directorio **inhere** y tiene las siguientes propiedades:

- legible por humanos
- 1033 bytes de tamaño
- No es ejecutable

Lo haremos con el siguiente comando:

```bash
bandit5@bandit:~$ find . -type f -readable -size 1033c ! -executable | xargs cat | head -n 1
P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU
```

### Nivel 6 -> 7:
Nos conectamos como usuario **bandit6**
```bash
sshpass -p 'P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU' ssh bandit6@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en algún lugar del servidor y tiene las siguientes propiedades:

- El usuario propietario es **bandit7**.
- El grupo propietario es **bandit6**.
- Tiene 33 bytes de tamaño.

Lo haremos con el siguiente comando:

```bash
bandit6@bandit:~$ find / -user bandit7 -group bandit6 2>/dev/null | xargs cat
z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S
```

### Nivel 7 -> 8:
Nos conectamos como usuario **bandit7**
```bash
sshpass -p 'z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S' ssh bandit7@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en el fichero **data.txt** al lado de la palabra **millionth**.

```bash
bandit7@bandit:~$ cat data.txt | grep millionth | awk 'NF{print $NF}'
TESKZC0XvTetK0S9xNwm25STk5iWrBvP
```

### Nivel 8 -> 9:
Nos conectamos como usuario **bandit8**
```bash
sshpass -p 'TESKZC0XvTetK0S9xNwm25STk5iWrBvP' ssh bandit8@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en el fichero **data.txt** y está en la línea que ocurre una vez.

```bash
bandit8@bandit:~$ sort data.txt | uniq -u
EN632PlfYiZbn3PhVK3XOGSlNInNE00t
```

### Nivel 9 -> 10:
Nos conectamos como usuario **bandit9**
```bash
sshpass -p 'EN632PlfYiZbn3PhVK3XOGSlNInNE00t' ssh bandit9@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en el fichero **data.txt** en una de las pocas cadenas legibles, al lado de varios carateres '='

```bash
bandit9@bandit:~$ cat data.txt | grep -a === | tail -n 1 | awk 'NF{print $NF}'
G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s
```

### Nivel 10 -> 11:
Nos conectamos como usuario **bandit10**
```bash
sshpass -p 'G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s' ssh bandit10@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en el fichero **data.txt**, el cuál contiene texto codificado en **base64**.

```bash
bandit10@bandit:~$ cat data.txt | base64 -d
The password is 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM
```

### Nivel 11 -> 12:
Nos conectamos como usuario **bandit11**
```bash
sshpass -p '6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM' ssh bandit11@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en el archivo **data.txt**, donde las letras se han girado **13 posiciones**.

```bash
bandit11@bandit:~$ cat data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
The password is JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv
```

### Nivel 12 -> 13:
Nos conectamos como usuario **bandit12**
```bash
sshpass -p 'JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv' ssh bandit12@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en el fichero **data.txt**, el cuál es un volcado hexadecimal de un archivo comprimido repetidamente.

Primero, vamos a crear un directorio en`/tmp`, ya que nos tenemos permiso para modificar **data.txt** en el directorio actual, y vamos a copiar **data.txt** allí.

```bash
bandit12@bandit:~$ mkdir /tmp/testr4f4
bandit12@bandit:~$ cp data.txt /tmp/testr4f4
bandit12@bandit:~$ cd /tmp/testr4f4
bandit12@bandit:/tmp/testr4f4$ ls
data.txt
```

Vamos a pasar el volcado hexadecimal a un binario usando la opción -r del comando **xxd** y a colocarle la extensión **.gz**

```bash
bandit12@bandit:/tmp/testr4f4$ xxd -r data.txt data
bandit12@bandit:/tmp/testr4f4$ file data
data: gzip compressed data, was "data2.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 581
bandit12@bandit:/tmp/testr4f4$ mv data data.gz
bandit12@bandit:/tmp/testr4f4$ ls
data.gz  data.txt
```

Una vez que tengamos el archivo **data.gz**, a proseguir con el nivel


Descomprimimos con el comando **gzip** el archivo data.gz:
```bash
bandit12@bandit:/tmp/testr4f4$ gzip -d data.gz
bandit12@bandit:/tmp/testr4f4$ ls
data  data.txt
bandit12@bandit:/tmp/testr4f4$ file data
data: bzip2 compressed data, block size = 900k
```

Nos fijamos que el archivo que estaba dentro es un comprimido **bzip2**

Renombramos el archivo **data** con extensión .bz2 y lo descomprimimos con la herramienta **bzip2**:
```bash
bandit12@bandit:/tmp/testr4f4$ mv data data.bz2
bandit12@bandit:/tmp/testr4f4$ bzip2 -d data.bz2
bandit12@bandit:/tmp/testr4f4$ ls
data  data.txt
bandit12@bandit:/tmp/testr4f4$ file data
data: gzip compressed data, was "data4.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 20480
```

El archivo resultante es un **gzip**, lo renombramos con extensión .gz y lo volvemos a descomprimir con **gzip**:
```bash
bandit12@bandit:/tmp/testr4f4$ mv data data.gz
bandit12@bandit:/tmp/testr4f4$ gzip -d data.gz
bandit12@bandit:/tmp/testr4f4$ ls
data  data.txt
bandit12@bandit:/tmp/testr4f4$ file data
data: POSIX tar archive (GNU)
```

Ahora tenemos un archivo **tar**, renombramos con extensión .tar y descomprimimos con la herramienta **tar**:
```bash
bandit12@bandit:/tmp/testr4f4$ mv data data.tar
bandit12@bandit:/tmp/testr4f4$ tar xvf data.tar
data5.bin
bandit12@bandit:/tmp/testr4f4$ ls
data5.bin  data.tar  data.txt
bandit12@bandit:/tmp/testr4f4$ file data5.bin
data5.bin: POSIX tar archive (GNU)
```

Dentro se encuentra otro archivo .tar llamado **data5.bin**, renombramos y volvemos a descomprimir.
```bash
bandit12@bandit:/tmp/testr4f4$ mv data5.bin data5.tar
bandit12@bandit:/tmp/testr4f4$ tar xvf data5.tar
data6.bin
bandit12@bandit:/tmp/testr4f4$ ls
data5.tar  data6.bin  data.tar  data.txt
bandit12@bandit:/tmp/testr4f4$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
```

Dentro se encuentra un archivo bzip2, volvemos a renombrar y descomprimimos:
```bash
bandit12@bandit:/tmp/testr4f4$ mv data6.bin data6.bz2
bandit12@bandit:/tmp/testr4f4$ bzip2 -d data6.bz2
bandit12@bandit:/tmp/testr4f4$ ls
data5.tar  data6  data.tar  data.txt
bandit12@bandit:/tmp/testr4f4$ file data6
data6: POSIX tar archive (GNU)
```

Encontramos otro archivo tar llamado **data6**, renombramos y descomprimimos de nuevo:
```bash
bandit12@bandit:/tmp/testr4f4$ mv data6 data6.tar
bandit12@bandit:/tmp/testr4f4$ tar xvf data6.tar
data8.bin
bandit12@bandit:/tmp/testr4f4$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 49
```

Dentro hay un archivo **gzip** (es el último) lo renombramos y descomprimimos:
```bash
bandit12@bandit:/tmp/testr4f4$ mv data8.bin data8.gz
bandit12@bandit:/tmp/testr4f4$ gzip -d data8.gz
bandit12@bandit:/tmp/testr4f4$ ls
data5.tar  data6.tar  data8  data.tar  data.txt
bandit12@bandit:/tmp/testr4f4$ file data8
data8: ASCII text
bandit12@bandit:/tmp/testr4f4$ cat data8
The password is wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
```

### Nivel 13 -> 14:
Nos conectamos como usuario **bandit13**
```bash
sshpass -p 'wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw' ssh bandit13@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se encuentra en el fichero `etc/bandit_pass/bandit14` y puede ser leído por el usuario bandit14, no necesitamos conseguir la contraseña, pero si acceder al siguiente nivel con una **clave privada SSH**:
```bash
bandit13@bandit:~$ ls
sshkey.private
bandit13@bandit:~$ ssh -i sshkey.private bandit14@localhost -p 2220
```

```bash
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
```

### Nivel 14 -> 15:
Nos conectamos como usuario **bandit14**
```bash
sshpass -p 'fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq' ssh bandit14@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se puede recuperar enviando la contraseña del nivel actual al puerto 30000 de la máquina local

```bash
bandit14@bandit:~$ nc localhost 30000
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
Correct!
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
```

### Nivel 15 -> 16:
Nos conectamos como usuario **bandit15**
```bash
sshpass -p 'jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt' ssh bandit15@bandit.labs.overthewire.org -p 2220
```

La contraseña para el siguiente nivel se puede recuperar enviando la contraseña del nivel actual al puerto 30001 de localhost usando la encriptación SSL

```bash
bandit15@bandit:~$ ncat --ssl localhost 30001
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
Correct!
JQttfApK4SeyHwDlI9SXGR50qclOAil1
```

### Nivel 16 -> 17:
Nos conectamos como usuario **bandit16**
```bash
sshpass -p 'JQttfApK4SeyHwDlI9SXGR50qclOAil1' ssh bandit16@bandit.labs.overthewire.org -p 2220
```
La contraseña para el siguiente nivel se puede recupearr enviando la contraseña del nivel actual a un puerto del localhost entre el 31000 y el 32000

```bash
bandit16@bandit:~$ ncat --ssl 127.0.0.1 31790
JQttfApK4SeyHwDlI9SXGR50qclOAil1
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

La respuesta es este texto que es una clave privada, la debemos de meter en un fichero de texto con extension **private** o **rsa** y con el siguiente comando nos conectamos como bandit17

```bash
bandit16@bandit:/tmp/tmp.v3ynzMVtMe$ ssh -i key.rsa bandit17@localhost -p 2220
```

### Nivel 17 -> 18:
Nos conectamos como usuario **bandit17**

Hay dos ficheros en el directorio **Home** llamados **passwords.old** y **passwords.new**. La contraseña se encuentra en **passwords.new** y es la única línea que ha sido cambianda entre los dos ficheros.

Con el comando **diff** podemos ver que líneas han sido cambiadas entre dos ficheros:

```bash
bandit17@bandit:~$ diff passwords.new passwords.old
42c42
< hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg
---
> glZreTEH1V3cGKL6g4conYqZqaEj0mte
bandit17@bandit:~$
```

### Nivel 18 -> 19:
Nos conectamos como usuario **bandit18**

```bash
sshpass -p 'hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg' ssh bandit18@bandit.labs.overthewire.org -p 2220
```

Al conectarnos a **bandit18** nos damos cuenta de que nos cierra la conexión, pero vemos un mensaje que dice **Byebye!**

Entonces lo que debemos hacer será conectarnos a bandit18 pero enviar un comando durante la conexión

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 ls
                      _                     _ _ _
                     | |__   __ _ _ __   __| (_) |_
                     | '_ \ / _` | '_ \ / _` | | __|
                     | |_) | (_| | | | | (_| | | |_
                     |_.__/ \__,_|_| |_|\__,_|_|\__|

		     This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password:
readme 
```
Vemos que hay un archivo llamado **readme**, le haremos un cat

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
                      _                     _ _ _
                     | |__   __ _ _ __   __| (_) |_
                     | '_ \ / _` | '_ \ / _` | | __|
                     | |_) | (_| | | | | (_| | | |_
                     |_.__/ \__,_|_| |_|\__,_|_|\__|


                   This is an OverTheWire game server.
          More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password:
awhqfNnAbc1naukrpqDYcF95h7HoMTrC
```
### Nivel 19 -> 20:
Nos conectamos como usuario **bandit19**
```bash
sshpass -p 'awhqfNnAbc1naukrpqDYcF95h7HoMTrC' ssh bandit19@bandit.labs.overthewire.org -p 2220
```

Para ganar acceso al siguiente nivel, debes usar el binario **setuid** en el directorio Home. Ejecútalo sin argumentos para averiguar como se usa. La contraseña de este nivel se encuentra en un sitio usual (/etc/bandit_pass) después de que uses el binario **setuid**

Usamos el binario de la siguiente forma:

```bash
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
VxCazJaVykI6W36BkBU0mJTCM8rR95XT
bandit19@bandit:~$
```

### Nivel 20 -> 21:
Nos conectamos como usuario **bandit20**

```bash
sshpass -p 'VxCazJaVykI6W36BkBU0mJTCM8rR95XT' ssh bandit20@bandit.labs.overthewire.org -p 2220
```
Hay un binario setuid en el directorio Home que hace lo siguiente, crea una conexión hacia localhost en el puerto que le especifiques como argumento. Luego lee una línea de texto de la conexión y la compara con la contraseña de **bandit20**, si la contraseña es correcta, imprimirá la contraseña de **bandit21**

En este nivel vamos a tener dos terminales abiertas con el usuario **bandit21**

En una ejecutaremos el siguiente comando:

```bash
bandit20@bandit:~$ nc -lvnp 4646
Listening on 0.0.0.0 4646
```

Se quedará esperando una conexión

En la otra ventana ejecutaremos el siguiente comando:

```bash
bandit20@bandit:~$ ./suconnect 4646
```

Y si copiamos la contraseña de **bandit20** en la ventana del primer comando nos saldrá esto:

```bash
bandit20@bandit:~$ nc -lvnp 4646
Listening on 0.0.0.0 4646
Connection received on 127.0.0.1 59130
VxCazJaVykI6W36BkBU0mJTCM8rR95XT
NvEJF7oVjkddltPSrdKEFOllh9V1IBcq
bandit20@bandit:~$
```

Y esto en la otra ventana:

```bash
bandit20@bandit:~$ ./suconnect 4646
Read: VxCazJaVykI6W36BkBU0mJTCM8rR95XT
Password matches, sending next password
bandit20@bandit:~$
```

### Nivel 21 -> 22:
Nos conectamos como usuario **bandit21**

```bash
sshpass -p 'NvEJF7oVjkddltPSrdKEFOllh9V1IBcq' ssh bandit21@bandit.labs.overthewire.org -p 2220
```

Un programa está corriendo automáticamente en intervalos regulares con **cron**. Hecha un vistazo en `/etc/cron.d/`, mira la configuración y el comando que se está ejecutando

```bash
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:/etc/cron.d$
```

Nos fijamos que está ejecutando un script llamado **cronjob_bandit22.sh**, veamos el contenido de ese script.

```bash
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:/etc/cron.d$
```

El script guarda la contraseña en un fichero en la carpeta `/tmp`, así que vamos a hacerle un cat a ese fichero

```bash
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
WdDozAdTM2z9DiFEQ2mGlwngMfj4EZff
bandit21@bandit:/etc/cron.d$
```

Y nos muestra la contraseña de **bandit22**

### Nivel 22 -> 23:
Nos conectamos como usuario **bandit22**

```bash
sshpass -p 'WdDozAdTM2z9DiFEQ2mGlwngMfj4EZff' ssh bandit22@bandit.labs.overthewire.org -p 2220
```

Este nivel es similar al anterior

```bash
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:/etc/cron.d$
```

Esta tarea está ejecutando un script llamado **cronjob_bandit23.sh**

El contenido del script:

```bash
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
bandit22@bandit:/etc/cron.d$
```

El script simplemente lo que está haciendo es crear dos variables y una de ellas está encriptada, se averigua muy fácil

```bash
bandit22@bandit:/usr/bin$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G
bandit22@bandit:/usr/bin$
```

### Nivel 23 -> 24
Nos conectamos como usuario **bandit23**

```bash
sshpass -p 'QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G' ssh bandit23@bandit.labs.overthewire.org -p 2220
```

Un programa está corriendo con cron, en este nivel deberemos de crear nuestro propio script y añadirlo a una ruta específica para darnos acceso al siguiente nivel

La tarea cron:

```bash
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit23@bandit:/etc/cron.d$
```

El script que se está ejecutando:

```bash
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo || exit 1
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -rf ./$i
    fi
done

bandit23@bandit:/etc/cron.d$
```

Así que deberemos crear un script, meterlo en `/var/spool/bandit24/foo` para que sea ejecutado

El script:

```bash
bandit23@bandit:/tmp/tmp.HIvvMQds5Z$ cat myScript.sh
#!/bin/bash

cp /etc/bandit_pass/bandit24 > /tmp/tmp.HIvvMQds5Z/bandit_pass24

chmod o+r /tmp/tmp.HIvvMQds5Z/bandit_pass24
bandit23@bandit:/tmp/tmp.HIvvMQds5Z$
```

Este script copiará la contraseña de bandit24 al directorio que le hemos especificado, el cuál hemos creado previamente, posteriormente le damos permisos de **ejecución** y lo copiamos en `/var/spool/bandit24/foo/`

# Continuará :)
