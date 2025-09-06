<picture>
  <img src="../../assets/images/banner_sealand.png" alt="Banner" style="width: 100%;">
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

## Configuración del Switch 0

Cambio de nombre

```bash
Switch>enable
Switch#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname s0
```

Crear las VLANs 

```bash
s0(config)#vlan 104
s0(config-vlan)#exit
s0(config)#vlan 204
s0(config-vlan)#exit
```

Asignar puertos a la VLANs seleccionar rango de interfaces

`s0(config)#int range FastEthernet0/1 - 11`

Establecer modo Access 

`s0(config-if-range)#switchport mode access`

Asociar los puertos a la VLAN 104

`s0(config-if-range)#switchport access vlan 104`

`no shutdown`

`exit`

---

Ahora lo mismo para la VLAN 204

```bash
s0(config)#int range FastEthernet0/12 - 24
s0(config-if-range)#swit
s0(config-if-range)#switchport mode access
s0(config-if-range)#switchport access vlan 204
s0(config-if-range)#no shutdown
s0(config-if-range)#exit
```

### Configurar el puerto Trunk del switch

Queremos permitir que las 2 VLANs sean transportadas entre dispositivos

| Acción                                                                 | Comando                                               |
|------------------------------------------------------------------------|--------------------------------------------------------|
| Selecciona el puerto GigabitEthernet0/1                                | `s0(config)#interface GigabitEthernet0/1`             |
| Lo configura en modo TRUNK                                             | `s0(config-if)#switchport mode trunk`                 |
| Permite solo las VLAN 104 y 204 sobre este enlace TRUNK               | `s0(config-if)#switchport trunk allowed vlan 104,204` |
| Establece la VLAN nativa como 99 (opcional pero recomendado)          | `s0(config-if)#switchport trunk native vlan 99`       |
| Activa la interfaz                                                     | `s0(config-if)#no shutdown`                           |
| Terminamos la configuración                                            | `s0(config-if)#exit`                                  |




---

## Configuración de Router R_SL_JAD1 (DTE)

1. Cambio de nombre

```bash
enable
configure terminal
hostname R_SL_JAD1
```

1. Configurar las interfaces para la VLAN 104 y VLAN 204

```bash
interface GigabitEthernet0/1.104
 encapsulation dot1Q 104
 ip address 10.100.104.1 255.255.255.0
 no shutdown
exit

interface GigabitEthernet0/1.204
 encapsulation dot1Q 204
 ip address 10.200.204.1 255.255.255.0
 no shutdown
exit
```

1. Configurar la interface SERIAL que conecta a R_VITEL_1 (DCE)

```bash
interface Serial0/0/0
 ip address 172.1.4.2 255.255.255.0
 no shutdown
exit
```

1. Configurar los pool de DHCP 

> Se excluyen las IP de las Gateway de las VLAN así evitando que accidentalmente se
puedan asignar mediante DHCP a ningún equipo de la red.
> 

```bash
ip dhcp excluded-address 10.100.104.1
ip dhcp excluded-address 10.200.204.1

ip dhcp pool vlan104
 network 10.100.104.0 255.255.255.0
 default-router 10.100.104.1
 dns-server 172.5.4.8
exit

ip dhcp pool vlan204
 network 10.200.204.0 255.255.255.0
 default-router 10.200.204.1
 dns-server 172.5.4.8
exit

```

<aside>
💡

Establezco cada default-router ( la dirección ip de la Gateway) a cada VLAN
y les asigno un DNS, en este caso el que pide el ejercicio,  que es el:  172.5.4.8

</aside>

---

## Configuración del router R_VITEL_1 (DCE)

```bash
Router>enable
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#int serial0/0/0
Router(config-if)#ip address 172.1.4.1 255.255.255.0
Router(config-if)#clock rate 4000000
Router(config-if)#no shutdown

Router(config-if)#
%LINK-5-CHANGED: Interface Serial0/0/0, changed state to up

Router(config-if)#exit
Router(config)#exit
```

```bash
R_VITEL_1#enable
R_VITEL_1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R_VITEL_1(config)#interface se0/0/1
R_VITEL_1(config-if)#ip address 172.2.4.2 255.255.255.0
R_VITEL_1(config-if)#clock rate 4000000
R_VITEL_1(config-if)#no shutdown

%LINK-5-CHANGED: Interface Serial0/0/1, changed state to down
R_VITEL_1(config-if)#exit
R_VITEL_1(config)#
%LINK-5-CHANGED: Interface Serial0/0/1, changed state to up

```

---

## Configuración Router R_VITEL_3 (DTE)

```bash
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#host R_VITEL_3
R_VITEL_3(config)#interface se0/0/0
R_VITEL_3(config-if)#ip address 172.2.4.1 255.255.255.0
R_VITEL_3(config-if)#no shutdown

R_VITEL_3(config-if)#
%LINK-5-CHANGED: Interface Serial0/0/0, changed state to up

```

```bash
R_VITEL_3>enable
R_VITEL_3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R_VITEL_3(config)#interface Se
R_VITEL_3(config)#interface Serial0/0/1
R_VITEL_3(config-if)#ip address 172.4.4.1 255.255.255.0
R_VITEL_3(config-if)#no shutdown
```

---

## Configuración Router R_VITEL 2 (DCE)

```bash
R_VITEL_2(config)#interface se0/0/0
R_VITEL_2(config-if)#ip address 172.4.4.2 255.255.255.0
R_VITEL_2(config-if)#clock rate 4000000
R_VITEL_2(config-if)#no shutdown 
R_VITEL_2(config-if)#exit

R_VITEL_2(config)#interface Se0/0/1
R_VITEL_2(config-if)#ip address 172.3.4.1 255.255.255.0
R_VITEL_2(config-if)#clock rate 4000000
R_VITEL_2(config-if)#no shutdown
```

## Configuración Router R_SL_JAD2 (DTE)

```bash
R_VITEL_2(config)#interface Se0/0/1
R_VITEL_2(config-if)#ip address 172.3.4.1 255.255.255.0
R_VITEL_2(config-if)#clock rate 4000000
R_VITEL_2(config-if)#no shutdown

R_SL_JAD2(config)#interface Gig0/0
R_SL_JAD2(config-if)#ip address 10.3.4.1 255.255.255.0
R_SL_JAD2(config-if)#no shutdown

R_SL_JAD2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
```

Vale ahora vamos a establecer en cada Router las rutas fijas necesarias para que lleguen los paquetes a las redes que nos interesan `ip route`

## Rutas fijas `ip route`

### ZONA 1: R_SL_JAD_1

🔹 Conectado a:

- LAN VLAN104 → `10.100.104.0/24`
- LAN VLAN204 → `10.200.204.0/24`
- `R_VITEL_1` vía `Serial0/0/0` → `172.1.4.2`

🔸 Rutas necesarias:

```bash
ip route 172.5.4.0 255.255.255.0 172.1.4.1
ip route 10.3.4.0 255.255.255.0 172.1.4.1

```

### ZONA 2: R_VITEL_1 DCE

🔹 Conectado a:

- `R_SL_JAD_1` (S0/0/0) ← DCE
- `R_VITEL_3` (S0/0/1) ← DCE

🔸 Rutas necesarias:

```bash
ip route 10.100.104.0 255.255.255.0 172.1.4.2
ip route 10.200.204.0 255.255.255.0 172.1.4.2
ip route 172.5.4.0 255.255.255.0 172.2.4.1
ip route 10.3.4.0 255.255.255.0 172.2.4.1

```

### ZONA 3: R_VITEL_3

🔹 Conectado a:

- LAN de los servidores (DNS 172.5.4.8 y Web 172.5.4.10)
- A `R_VITEL_1` (S0/0/0)
- A `R_VITEL_2` (S0/0/1)

🔸 Rutas necesarias:

```bash
ip route 10.100.104.0 255.255.255.0 172.2.4.2
ip route 10.200.204.0 255.255.255.0 172.2.4.2
ip route 10.3.4.0 255.255.255.0 172.4.4.2
```

### ZONA 4: R_VITEL_2 DCE

🔹 Conectado a:

- A `R_VITEL_3` (S0/0/0 → 172.4.4.2) DCE
- A `Router` (R_SL_JAD_2) (S0/0/1 → 173.3.4.1) DCE

🔸 Rutas necesarias:

```bash
ip route 172.5.4.0 255.255.255.0 172.4.4.1
ip route 10.3.4.0 255.255.255.0 172.3.4.2
ip route 10.100.104.0 255.255.255.0 172.4.4.1
ip route 10.200.204.0 255.255.255.0 172.4.4.1
```

### ZONA 5: Router final (R_SL_JAD_2)

🔹 Conectado a:

- LAN: 10.3.4.0/24
- Serial0/0/0 a `R_VITEL_2` (→ 173.3.4.2)

🔸 Rutas necesarias:

```bash
ip route 172.5.4.0 255.255.255.0 172.3.4.1
ip route 10.100.104.0 255.255.255.0 172.3.4.1
ip route 10.200.204.0 255.255.255.0 172.3.4.1
```

## Configuración del servidor DNS (`172.5.4.8`)

1. Abre el servidor DNS en Packet Tracer

Haz clic en el servidor conectado a `R_VITEL_3` con IP `172.5.4.8`.

2. Pestaña `Services`

1. Ve a la pestaña **"Services"**.
2. En el menú de la izquierda, selecciona **"DNS"**.
3. Asegúrate de que **DNS Service** esté en **ON**.

3. Agrega las entradas DNS

Introduce estas dos entradas:

| **Name** | **Address** | **Record Type** |
| --- | --- | --- |
| `www.jad.org` | `172.5.4.10` | A |
| `www.sealand.org` | `10.3.4.2` | A |

## Servidor Web 1: `www.jad.org`

* IP: `172.5.4.10`

* Gateway: `172.5.4.1`

* DNS: `172.5.4.8`

1. Abre el servidor en Packet Tracer
1.2. Haz clic en el servidor con esa IP.

2. Configura parámetros de red

Pestaña `Desktop` > `IP Configuration`:

- IP Address: `172.5.4.10`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `172.5.4.1`
- DNS Server: `172.5.4.8`

3. Activa el servicio HTTP

Pestaña `Services` > `HTTP`:

- Asegúrate de que **HTTP** esté en **ON**.

## Servidor Web 2: `www.sealand.org`

* IP: `10.3.4.2`

* Gateway: `10.3.4.1`

*  DNS: `172.5.4.8`

1. Abre el servidor en Packet Tracer
1.2. Haz clic en el servidor con esa IP.

2. Configura parámetros de red

Pestaña `Desktop` > `IP Configuration`:

- IP Address: `10.3.4.2`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `10.3.4.1`
- DNS Server: `172.5.4.8`

3. Activa el servicio HTTP

Pestaña `Services` > `HTTP`:

- Verifica que **HTTP** esté en **ON**.

---


## UPDATE 02/07/05 CAMBIAMOS LA VLAN1 NATIVA POR LA VLAN99

<aside>
💡

En vez de usar la VLAN 1 (que es la VLAN nativa por defecto), se cambia la VLAN nativa a VLAN 99 para evitar problemas de seguridad y confusión. El tráfico sin etiqueta que viaja por el enlace troncal se asigna a VLAN 99.

</aside>

```bash
Switch# configure terminal
Switch(config)# vlan 99
Switch(config-vlan)# name NativeVLAN99
Switch(config-vlan)# exit
Switch(config)# interface Gig0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk native vlan 99
Switch(config-if)# exit
Switch# show interfaces Gig0/1 switchport
```

---

Y ahora cambiar en el router para que vea el trafico untagged

```bash
enable
configure terminal

interface GigabitEthernet0/1.99
 encapsulation dot1Q 99 native
 ip address 192.168.99.1 255.255.255.0
 no shutdown

exit
write memory
```
