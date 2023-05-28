---
title: "Laboratorio con Firewall en Proxmox"
date: 2023-05-18
image: ../../assets/img/PROXMOX/proxmoxBanner.png
categories: [ Proxmox ]
tags: [Virtualización, Proxmox]
---

En esta práctica vamos a configurar dos máquinas Ubuntu Linux, una será el cliente y la otra actuará como Firewall.

### Esquema de la práctica

![Laboratorio](../../assets/img/PROXMOX/esquema-laboratorio-proxmox.png)

El esquema consiste en:
1. Una máquina que servirá de **Firewall** que será un contenedor `Ubuntu` en nuestro servidor **Proxmox** con dos tarjetas de red, la externa tendrá la IP **192.168.28.200** y la interna la IP **192.168.14.1**.
2. Dentro de la red también habrá un dispositivo Switch con la IP **192.168.14.100**.
3. Un cliente que será otro contenedor Ubuntu y tendrá la IP **192.168.14.10**.

---
## Creando Switch en Proxmox

![Create Linux Bridge](/assets/img/PROXMOX/paso1.png){: width="100%" }

Nos dirigimos a nuestro nodo principal y a **System > Network**, pulsamos en `create` y a **Linux Bridge**.

![Colocar IP al Switch](/assets/img/PROXMOX/paso2.png){: width="600px" }

Y le asignamos la IP **192.168.14.100**.

![Version del Contenedor LXC Ubuntu](/assets/img/PROXMOX/paso3.png){: width="800px" }

Nos dirigimos a nuestro "Storage" del nodo principal y nos descargamos una versión de Ubuntu, en mi caso me he descargado la `Ubuntu 22.04 Jammy`.

![Contenedores Creados](/assets/img/PROXMOX/contenedores-creados.png){: width="400px" }

Una vez creado los contenedores, vamos a ver su configuración de red.

---
### Configuración de red del contenedor Firewall
![Configuracion de red Firewall](/assets/img/PROXMOX/configuracion-red-firewall.png){: width="700px" }

### Configuración de red del contenedor UbuntuCliente
![Configuracion de red Cliente](/assets/img/PROXMOX/configuracion-red-cliente.png)

---
## Probando conectividad en las máquinas

Una vez arrancados los dos contenedores, vamos a empezar por hacer `ping` desde **ClienteUbuntu** hasta el Switch que tiene la IP **192.168.14.100**.

```bash
root@ClienteUbuntu:~# ping -c 4 192.168.14.100
PING 192.168.14.100 (192.168.14.100) 56(84) bytes of data.
64 bytes from 192.168.14.100: icmp_seq=1 ttl=64 time=0.058 ms
64 bytes from 192.168.14.100: icmp_seq=2 ttl=64 time=0.048 ms
64 bytes from 192.168.14.100: icmp_seq=3 ttl=64 time=0.063 ms
64 bytes from 192.168.14.100: icmp_seq=4 ttl=64 time=0.044 ms

--- 192.168.14.100 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3075ms
rtt min/avg/max/mdev = 0.044/0.053/0.063/0.007 ms
root@ClienteUbuntu:~#
```

También comprobaremos que existe conectividad entre **ClienteUbuntu** y las dos patas del **Firewall**, que sus IPs son **192.168.14.1** y **192.168.28.200**.

```bash
root@ClienteUbuntu:~# ping -c 2 192.168.14.1
PING 192.168.14.1 (192.168.14.1) 56(84) bytes of data.
64 bytes from 192.168.14.1: icmp_seq=1 ttl=64 time=0.052 ms
64 bytes from 192.168.14.1: icmp_seq=2 ttl=64 time=0.050 ms

--- 192.168.14.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.050/0.051/0.052/0.001 ms

root@ClienteUbuntu:~# ping -c 2 192.168.28.200
PING 192.168.28.200 (192.168.28.200) 56(84) bytes of data.
64 bytes from 192.168.28.200: icmp_seq=1 ttl=64 time=0.050 ms
64 bytes from 192.168.28.200: icmp_seq=2 ttl=64 time=0.083 ms

--- 192.168.28.200 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1024ms
rtt min/avg/max/mdev = 0.050/0.066/0.083/0.016 ms
root@ClienteUbuntu:~# 
```

La máquina **Firewall** tiene conectividad con la máquina **ClienteUbuntu**, también con el **Switch**, y conectividad con el exterior.

```bash
root@Firewall:~# ping -c 2 192.168.14.10
PING 192.168.14.10 (192.168.14.10) 56(84) bytes of data.
64 bytes from 192.168.14.10: icmp_seq=1 ttl=64 time=0.078 ms
64 bytes from 192.168.14.10: icmp_seq=2 ttl=64 time=0.050 ms

--- 192.168.14.10 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 0.050/0.064/0.078/0.014 ms

root@Firewall:~# ping -c 2 192.168.14.100
PING 192.168.14.100 (192.168.14.100) 56(84) bytes of data.
64 bytes from 192.168.14.100: icmp_seq=1 ttl=64 time=0.066 ms
64 bytes from 192.168.14.100: icmp_seq=2 ttl=64 time=0.049 ms

--- 192.168.14.100 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.049/0.057/0.066/0.008 ms

root@Firewall:~# ping -c 2 8.8.8.8 
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=248 time=12.6 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=248 time=14.7 ms

--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 12.583/13.647/14.712/1.064 ms
root@Firewall:~# 
```

---
## Configuración y activación de Iptables

El siguiente paso será configurar y activar Iptables, pero antes de nada, **¿Qué carajos es Iptables?**

**Iptables** básicamente es un programa que viene por defecto en sistemas `Linux` y nos permite configurar el firewall o cortafuegos.

![Iptables](/assets/img/PROXMOX/iptables.jpg){: width="700px" }

> Antes de nada vamos a hacer una configuración previa.
{: .prompt-info}

Listamos las **interfaces** que tiene nuestra máquina **Firewall** en la ruta `/sys/class/net/`{: .filepath}.

```bash
rootFirewall:~# ls /sys/class/net/
eth0  lo  net14
root@Firewall:~# @
```

Ahora vamos a habilitar el reenvío de paquetes en el Firewall, para que actue como `Router`.
Se hace con el siguiente comando:
```bash
root@Firewall:~# echo 1 > /proc/sys/net/ipv4/ip_forward
root@Firewall:~# 
```
Ahora si hacemos `ping` desde la máquina **ClienteUbuntu** hacia una máquina de nuestra red local, debería funcionar.

```bash
root@ClienteUbuntu:~# ping -c 2 192.168.28.108
PING 192.168.28.108 (192.168.28.108) 56(84) bytes of data.
64 bytes from 192.168.28.108: icmp_seq=1 ttl=64 time=158 ms
64 bytes from 192.168.28.108: icmp_seq=2 ttl=64 time=56.4 ms

--- 192.168.28.108 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 56.433/107.326/158.220/50.893 ms
root@ClienteUbuntu:~#
```

Pero esto que hemos hecho no se queda de forma permanente, cuando reiniciemos el sistema dejará de funcionar.

Para ello, debemos editar el fichero `/etc/sysctl.conf`{: .filepath} y descomentar la línea que pone `#net.ipv4.ip_forward=1`

Para aplicar la configuración que acabamos de hacer vamos a usar el comando `sudo sysctl -p /etc/sysctl.conf`
```bash
sudo sysctl -p /etc/sysctl.conf
```

> Ahora si, pasamos a configurar **Iptables**.
{: .prompt-info}

Listamos las reglas que tiene **Iptables** actualmente:
```bash
root@Firewall:~# iptables -L -nv
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
root@Firewall:~# 
```

Por último vamos a agregar una regla para que la máquina **ClienteUbuntu** tenga acceso a internet.
```bash
root@Firewall:~# iptables -t nat -A POSTROUTING -s 192.168.14.0/24 -o eth0 -j MASQUERADE
root@Firewall:~# 
```

> MASQUERADE permite que la red **192.168.14.0** tenga enmascaramiento y tenga una IP válida para salir al exterior.
{: .prompt-info}

Si hacemos ping a la IP de google (8.8.8.8) funciona correctamente.
```bash
root@ClienteUbuntu:~# ping -c 2 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=247 time=11.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=247 time=10.7 ms

--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 10.696/10.897/11.099/0.201 ms
root@ClienteUbuntu:~# 
```

---
## Guardar configuración de Iptables

Todos los cambios realizados en Iptables se borrarán una vez reiniciemos la máquina **Firewall**, así que vamos a aprender como podemos guardar todos los cambios.

Instalamos el paquete `iptables-persistent` (Cuando nos pregunte **Save current IPv4/IPv6 rules?** le damos a **Yes**)
```bash
root@Firewall:~# apt install iptables-persistent -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
iptables-persistent is already the newest version (1.0.16).
0 upgraded, 0 newly installed, 0 to remove and 117 not upgraded.
root@Firewall:~# 
```

Guardamos la configuración con este comando:
```bash
root@Firewall:~# netfilter-persistent save
run-parts: executing /usr/share/netfilter-persistent/plugins.d/15-ip4tables save
run-parts: executing /usr/share/netfilter-persistent/plugins.d/25-ip6tables save
root@Firewall:~#
```

Si reiniciamos la máquina, nuestras reglas de **Iptables** seguirán como estaban configuradas.

`Y aquí da por finalizado el laboratorio, muchas gracias por leer el post, nos vemos en el próximo :)`
