# Topología 2

## Cálculo de subredes

| VLAN  | Hosts S. | Host E. | Dirección de red | Máscara | Máscara punteada | Primera IP válida | Última IP válida |    Broadcast    |
| :---: | :------: | :-----: | :--------------: | :-----: | :--------------: | :---------------: | :--------------: | :-------------: |
|  30   |   122    |   126   |  192.168.119.0   |   /25   | 255.255.255.128  |   192.168.119.1   | 192.168.119.126  | 192.168.119.127 |
|  40   |    36    |   62    | 192.168.119.128  |   /26   | 255.255.255.192  |  192.168.119.129  | 192.168.119.190  | 192.168.119.191 |
|  10   |    21    |   30    | 192.168.119.192  |   /27   | 255.255.255.224  |  192.168.119.193  | 192.168.119.222  | 192.168.119.223 |
|  20   |    8     |   14    | 192.168.119.224  |   /28   | 255.255.255.240  |  192.168.119.225  | 192.168.119.238  | 192.168.119.239 |

## Port channels

Switch 1

```bash
configure terminal
interface range fastEthernet 1/1 - 2
channel-group 1 mode on
exit

interface range fastEthernet 1/3 - 4
channel-group 2 mode on
exit
do wri
```

Switch 2

```bash
configure terminal
interface range fastEthernet 1/1 - 2
channel-group 1 mode on
exit

interface range fastEthernet 1/7 - 8
channel-group 3 mode on
exit

interface range fastEthernet 1/10 - 12
channel-group 5 mode on

do wri
```

Switch 3

```bash
configure terminal
interface range fastEthernet 1/3 - 4
channel-group 2 mode on
exit

interface range fastEthernet 1/5 - 6
channel-group 4 mode on
exit

interface range fastEthernet 1/10 - 12
channel-group 5 mode on
exit

do wri
```

Switch 4

```bash
configure terminal
interface range fastEthernet 1/7 - 8
channel-group 3 mode on
exit

interface range fastEthernet 1/5 - 6
channel-group 4 mode on
exit

do wri
```

## VTP

Switch 1, 2 y 3

```bash
vtp domain redes1gp19
vtp password redes1gp19
vtp mode client
do wri
```

Switch 4

```bash
vtp domain redes1gp19
vtp password redes1gp19
vtp mode server
do wri
```

## Creando VLANs

Switch 4

```bash
vlan 10
name RHUMANOS
exit

vlan 20
name CONTABILIDAD
exit

vlan 30
name VENTAS
exit

vlan 40
name INFORMATICA
exit

do wri
```

## Configurando enlaces

Switch 1

```bash
interface fastEthernet 1/0
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface port-channel 1
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface port-channel 2
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

do wri
```

Switch 2

```bash
interface port-channel 1
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface port-channel 3
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface port-channel 5
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface range fastEthernet 1/14 - 15
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

do wri
```

Switch 3

```bash
interface port-channel 2
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface port-channel 4
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface port-channel 5
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface range fastEthernet 1/14 - 15
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

do wri
```

Switch 4

```bash
interface port-channel 3
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface port-channel 4
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

interface range fastEthernet 1/14 - 15
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30,40,1002-1005
no shutdown
exit

do wri
```

## Agregar STP si no está

```bash
configure terminal

spanning-tree vlan 10 root primary
spanning-tree vlan 20 root primary
spanning-tree vlan 30 root primary
spanning-tree vlan 40 root primary
end

sh spanning-tree root
```

## Enrutamiento InterVLAN

Router

```bash
configure terminal

interface fastEthernet 0/0.1
encapsulation dot1Q 30
ip address 192.168.119.1 255.255.255.128
exit

interface fastEthernet 0/0.2
encapsulation dot1Q 40
ip address 192.168.119.129 255.255.255.192
exit

interface fastEthernet 0/0.3
encapsulation dot1Q 10
ip address 192.168.119.193 255.255.255.224
exit

interface fastEthernet 0/0.4
encapsulation dot1Q 20
ip address 192.168.119.225 255.255.255.240
exit

interface fastEthernet 0/0
no shutdown
exit

do wri
```

## DHCP

```bash
interface fastEthernet 0/0.1
ip dhcp pool ventas
network 192.168.119.0 255.255.255.128
default-route 192.168.119.1
exit

interface fastEthernet 0/0.2
ip dhcp pool informatica
network 192.168.119.128 255.255.255.192
default-route 192.168.119.129
exit

interface fastEthernet 0/0.3
ip dhcp pool rhumanos
network 192.168.119.192 255.255.255.224
default-route 192.168.119.193
exit

interface fastEthernet 0/0.4
ip dhcp pool contabilidad
network 192.168.119.224 255.255.255.240
default-route 192.168.119.225
exit

do wri
```

## Rutas estáticas

Router

```bash
configure terminal

interface fastEthernet 0/1
ip address 10.19.0.30 255.255.192.0
no shutdown
exit

do wri

ip route 10.19.0.0 255.255.192.0 10.19.0.60
ip route 10.19.64.0 255.255.192.0 10.19.0.60
ip route 10.19.128.0 255.255.192.0 10.19.0.60
ip route 10.19.192.0 255.255.192.0 10.19.0.60

ip route 192.168.19.0 255.255.255.224 10.19.0.60
ip route 192.168.19.32 255.255.255.224 10.19.0.60
ip route 192.168.19.64 255.255.255.224 10.19.0.60
ip route 192.168.19.96 255.255.255.224 10.19.0.60
ip route 192.168.19.128 255.255.255.224 10.19.0.60

do wri

```
