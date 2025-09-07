<picture>
  <img src="../../../assets/images/banner_sealand.png" alt="Banner" style="width: 100%;">
</picture>

# El Principado de Sealand

Proyecto final de asignatura de redes de módulo de grado medio I.E.S. Galileo
ROUTERS: 2911 CON MÓDULO DE SERIAL
SWITCHES: CISCO CATALYST 2960

**Clock rate:** En interfaces seriales que actúen como DCE se debe configurar `clock rate 4000000`.

Conexión PC0 fa0 a fa0/1 al Switch

Conexión PC1 fa0 a fa0/12 al Switch 

<aside>
💡

Vamos a utilizar las interfaces fa0/1 a fa0/11 para VLAN104 y fa0/12 a fa0/24 para VLAN204

</aside>

Conexión Switch a Router Gig01/Gig01 ademas instalamos un módulo HWIC-2T para interfaces serial que configuraremos más adelante.

# Proyecto Sealand – Redes

## Índice

- [Introducción](#introducción)
- [Routers y Switches](#routers-y-switches)
  - [Switch 0 (s0)](#switch-0-s0)
    - [Cambio de nombre](#cambio-de-nombre)
    - [Crear VLANs](#crear-vlans)
    - [Asignar puertos a las VLANs](#asignar-puertos-a-las-vlans)
    - [Configurar puerto Trunk](#configurar-puerto-trunk)
  - [Router R_SL_JAD1](#router-r_sl_jad1)
    - [Interfaces VLAN](#interfaces-vlan)
    - [Interfaz Serial](#interfaz-serial)
    - [Pools DHCP](#pools-dhcp)
  - [Router R_VITEL_1](#router-r_vitel_1)
    - [Interfaces Serial](#interfaces-serial)
  - [Router R_VITEL_3](#router-r_vitel_3)
    - [Interfaces Serial](#interfaces-serial-1)
  - [Router R_VITEL_2](#router-r_vitel_2)
    - [Interfaces Serial](#interfaces-serial-2)
  - [Router R_SL_JAD2](#router-r_sl_jad2)
    - [Interfaces Serial y Gigabit](#interfaces-serial-y-gigabit)
- [Rutas fijas (IP Route)](#rutas-fijas-ip-route)
  - [Zona 1: R_SL_JAD_1](#zona-1-r_sl_jad_1)
  - [Zona 2: R_VITEL_1 DCE](#zona-2-r_vitel_1-dce)
  - [Zona 3: R_VITEL_3](#zona-3-r_vitel_3)
  - [Zona 4: R_VITEL_2 DCE](#zona-4-r_vitel_2-dce)
  - [Zona 5: Router final R_SL_JAD_2](#zona-5-router-final-r_sl_jad_2)
- [Configuración Servidor DNS](#configuración-servidor-dns)
- [Servidores Web](#servidores-web)
- [Actualización VLAN nativa](#actualización-vlan-nativa)

---

## Introducción

Proyecto final de la asignatura de redes del módulo de grado medio I.E.S. Galileo.  
Se configura la red del Principado de Sealand con routers, switches, VLANs, rutas fijas, servidor DNS y servidores web.

---

## Routers y Switches

### Switch 0 (s0)

#### Cambio de nombre
```cli
s0> enable
s0# configure terminal
s0(config)# hostname s0
```
#### Crear VLANs
```cli
s0(config)# vlan 104
s0(config-vlan)# exit
s0(config)# vlan 204
s0(config-vlan)# exit
```
#### Asignar puertos a las VLANs
*En este caso vamos a asignar las interfaces de la  1 al 11 a la VLAN104 y de la 12-24 a la VLAN204*
```cli
s0(config)# int range FastEthernet0/1-11
s0(config-if-range)# switchport mode access
s0(config-if-range)# switchport access vlan 104
s0(config-if-range)# no shutdown
exit

s0(config)# int range FastEthernet0/12-24
s0(config-if-range)# switchport mode access
s0(config-if-range)# switchport access vlan 204
s0(config-if-range)# no shutdown
exit
```
#### Configurar puerto Trunk
```cli
s0(config)# interface GigabitEthernet0/1
s0(config-if)# switchport mode trunk
s0(config-if)# switchport trunk allowed vlan 104,204
s0(config-if)# switchport trunk native vlan 99
s0(config-if)# no shutdown
exit
```
### Router R_SL_JAD1
#### Interfaces VLAN
```cli
R_SL_JAD1> enable
R_SL_JAD1# configure terminal
R_SL_JAD1(config)# interface GigabitEthernet0/1.104
R_SL_JAD1(config-if)# encapsulation dot1Q 104
R_SL_JAD1(config-if)# ip address 10.100.104.1 255.255.255.0
R_SL_JAD1(config-if)# no shutdown
exit

R_SL_JAD1(config)# interface GigabitEthernet0/1.204
R_SL_JAD1(config-if)# encapsulation dot1Q 204
R_SL_JAD1(config-if)# ip address 10.200.204.1 255.255.255.0
R_SL_JAD1(config-if)# no shutdown
exit
```
#### Interfaz Serial
```cli
R_SL_JAD1(config)# interface Serial0/0/0
R_SL_JAD1(config-if)# ip address 172.1.4.2 255.255.255.0
R_SL_JAD1(config-if)# no shutdown
exit
```
#### Pools DHCP
```cli
R_SL_JAD1(config)# ip dhcp excluded-address 10.100.104.1
R_SL_JAD1(config)# ip dhcp excluded-address 10.200.204.1
R_SL_JAD1(config)# ip dhcp pool vlan104
R_SL_JAD1(dhcp-config)# network 10.100.104.0 255.255.255.0
R_SL_JAD1(dhcp-config)# default-router 10.100.104.1
R_SL_JAD1(dhcp-config)# dns-server 172.5.4.8
exit
R_SL_JAD1(config)# ip dhcp pool vlan204
R_SL_JAD1(dhcp-config)# network 10.200.204.0 255.255.255.0
R_SL_JAD1(dhcp-config)# default-router 10.200.204.1
R_SL_JAD1(dhcp-config)# dns-server 172.5.4.8
exit
```
### Router R_VITEL_1
#### Interfaces Serial
```cli
R_VITEL_1> enable
R_VITEL_1# configure terminal
R_VITEL_1(config)# interface Serial0/0/0
R_VITEL_1(config-if)# ip address 172.1.4.1 255.255.255.0
R_VITEL_1(config-if)# clock rate 4000000
R_VITEL_1(config-if)# no shutdown
exit

R_VITEL_1(config)# interface Serial0/0/1
R_VITEL_1(config-if)# ip address 172.2.4.2 255.255.255.0
R_VITEL_1(config-if)# clock rate 4000000
R_VITEL_1(config-if)# no shutdown
exit
```
### Router R_VITEL_3
#### Interfaces Serial
```cli
R_VITEL_3> enable
R_VITEL_3# configure terminal
R_VITEL_3(config)# interface Serial0/0/0
R_VITEL_3(config-if)# ip address 172.2.4.1 255.255.255.0
R_VITEL_3(config-if)# no shutdown
exit

R_VITEL_3(config)# interface Serial0/0/1
R_VITEL_3(config-if)# ip address 172.4.4.1 255.255.255.0
R_VITEL_3(config-if)# no shutdown
exit
```

### Router R_VITEL_2
#### Interfaces Serial
```cli
R_VITEL_2> enable
R_VITEL_2# configure terminal
R_VITEL_2(config)# interface Serial0/0/0
R_VITEL_2(config-if)# ip address 172.4.4.2 255.255.255.0
R_VITEL_2(config-if)# clock rate 4000000
R_VITEL_2(config-if)# no shutdown
exit

R_VITEL_2(config)# interface Serial0/0/1
R_VITEL_2(config-if)# ip address 172.3.4.1 255.255.255.0
R_VITEL_2(config-if)# clock rate 4000000
R_VITEL_2(config-if)# no shutdown
exit
```

### Router R_SL_JAD2
#### Interfaces Serial y Gigabit
```cli
R_SL_JAD2> enable
R_SL_JAD2# configure terminal
R_SL_JAD2(config)# interface Serial0/0/1
R_SL_JAD2(config-if)# ip address 172.3.4.1 255.255.255.0
R_SL_JAD2(config-if)# clock rate 4000000
R_SL_JAD2(config-if)# no shutdown
exit

R_SL_JAD2(config)# interface Gig0/0
R_SL_JAD2(config-if)# ip address 10.3.4.1 255.255.255.0
R_SL_JAD2(config-if)# no shutdown
exit
```

## Rutas fijas (IP Route)
### Zona 1: R_SL_JAD_1
```cli
ip route 172.5.4.0 255.255.255.0 172.1.4.1
ip route 10.3.4.0 255.255.255.0 172.1.4.1
```
### Zona 2: R_VITEL_1 DCE
```cli
ip route 10.100.104.0 255.255.255.0 172.1.4.2
ip route 10.200.204.0 255.255.255.0 172.1.4.2
ip route 172.5.4.0 255.255.255.0 172.2.4.1
ip route 10.3.4.0 255.255.255.0 172.2.4.1
```
### Zona 3: R_VITEL_3
```cli
ip route 10.100.104.0 255.255.255.0 172.2.4.2
ip route 10.200.204.0 255.255.255.0 172.2.4.2
ip route 10.3.4.0 255.255.255.0 172.4.4.2
```

### Zona 4: R_VITEL_2 DCE
```cli
ip route 172.5.4.0 255.255.255.0 172.4.4.1
ip route 10.3.4.0 255.255.255.0 172.3.4.2
ip route 10.100.104.0 255.255.255.0 172.4.4.1
ip route 10.200.204.0 255.255.255.0 172.4.4.1
```

### Zona 5: Router final R_SL_JAD_2
```cli
ip route 172.5.4.0 255.255.255.0 172.3.4.1
ip route 10.100.104.0 255.255.255.0 172.3.4.1
ip route 10.200.204.0 255.255.255.0 172.3.4.1
```
## Configuración Servidor DNS (172.5.4.8)

Packet Tracer > Servidor > Services > DNS
DNS Service ON
Agregar entradas:
www.jad.org  -> 172.5.4.10 www.sealand.org -> 10.3.4.2

### Servidores Web
#### Servidor Web 1: www.jad.org
IP: 172.5.4.10
Gateway: 172.5.4.1
DNS: 172.5.4.8
HTTP: ON


#### Servidor Web 2: www.sealand.org


IP: 10.3.4.2
Gateway: 10.3.4.1
DNS: 172.5.4.8
HTTP: ON

## Actualización VLAN nativa
```cli
Switch# configure terminal
Switch(config)# vlan 99
Switch(config-vlan)# name NativeVLAN99
Switch(config-vlan)# exit
Switch(config)# interface Gig0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk native vlan 99
Switch(config-if)# exit
```

*Añadimos comando para que el router vea el tráfico "untagged"*
```cli
enable
configure terminal
interface GigabitEthernet0/1.99
 encapsulation dot1Q 99 native
 ip address 192.168.99.1 255.255.255.0
 no shutdown
exit
write memory
```
---

## Referencias

- [Cisco Networking Academy](https://www.netacad.com/) – Documentación oficial y materiales educativos.  
- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) – Herramienta de simulación de redes.  

## Agradecimientos

Proyecto realizado en el marco de la asignatura de Redes en el I.E.S. Galileo.  
Agradecimientos especiales al profesorado por la guía durante el aprendizaje y a Cisco Networking Academy por el material formativo.  

## Licencia

Este proyecto tiene fines exclusivamente **educativos**.  
No es un documento oficial de Cisco Systems.  
Puedes reutilizar el contenido bajo la licencia [MIT](https://opensource.org/licenses/MIT).  

[Volver al Índice](##indice)

