# Manual Técnico

## Proyecto 1 -- Redes 2

**Estudiante:** Luis Gomez\
**Carnet:** 201801391

------------------------------------------------------------------------

# 1. Descripción del proyecto

El proyecto consiste en el diseño e implementación de una red
empresarial utilizando **Cisco Packet Tracer**, empleando una
arquitectura basada en **VLANs, enlaces troncales, Spanning Tree y DHCP
centralizado mediante servidores**.

La red está dividida en múltiples capas de switches siguiendo un modelo
jerárquico:

-   Capa de Acceso\
-   Capa de Distribución\
-   Capa Core / Multilayer

Cada VLAN representa un segmento lógico de red independiente que permite
separar el tráfico entre diferentes áreas.

------------------------------------------------------------------------

# 2. VLANs implementadas

  VLAN   Nombre                     Red
  ------ -------------------------- -----------------
  10     VLAN_NARANJA_EdificioIZQ   192.168.10.0/24
  20     VLAN_VERDE_EdificioIZQ     192.168.20.0/24
  30     VLAN_NARANJA_EdificioDER   192.168.30.0/24
  40     VLAN_VERDE_EdificioDER     192.168.40.0/24
  99     VLAN_ADMIN                 192.168.99.0/24

------------------------------------------------------------------------

# 3. Creación de VLANs

``` bash
enable
configure terminal

vlan 10
name VLAN_NARANJA_EdificioIZQ_201801391

vlan 20
name VLAN_VERDE_EdificioIZQ_201801391

vlan 30
name VLAN_NARANJA_EdificioDER_201801391

vlan 40
name VLAN_VERDE_EdificioDER_201801391

vlan 99
name VLAN_ADMIN_201801391

end
write memory
```

------------------------------------------------------------------------

# 4. Configuración de enlaces troncales

``` bash
interface range gigabitEthernet1/0/1-3
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 10,20,30,40,99
```

Verificación:

``` bash
show interfaces trunk
```

------------------------------------------------------------------------

# 5. Spanning Tree

Para evitar bucles de red se utiliza el protocolo Spanning Tree.

``` bash
show spanning-tree
```

------------------------------------------------------------------------

# 6. Activación de routing en switch multilayer

``` bash
configure terminal
ip routing
```

------------------------------------------------------------------------

# 7. Interfaces VLAN (Gateways)

``` bash
interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown

interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown

interface vlan 30
ip address 192.168.30.1 255.255.255.0
no shutdown

interface vlan 40
ip address 192.168.40.1 255.255.255.0
no shutdown

interface vlan 99
ip address 192.168.99.1 255.255.255.0
no shutdown
```

Verificación:

``` bash
show ip interface brief
```

------------------------------------------------------------------------

# 8. Configuración DHCP

## Server0

Pools configurados:

### VLAN10

-   Default Gateway: 192.168.10.1\
-   Start IP: 192.168.10.10\
-   Subnet Mask: 255.255.255.0\
-   DNS: 8.8.8.8

### VLAN20

-   Default Gateway: 192.168.20.1\
-   Start IP: 192.168.20.10\
-   Subnet Mask: 255.255.255.0\
-   DNS: 8.8.8.8

------------------------------------------------------------------------

## Server1

Pools configurados:

### VLAN30

-   Default Gateway: 192.168.30.1\
-   Start IP: 192.168.30.10\
-   Subnet Mask: 255.255.255.0\
-   DNS: 8.8.8.8

### VLAN40

-   Default Gateway: 192.168.40.1\
-   Start IP: 192.168.40.10\
-   Subnet Mask: 255.255.255.0\
-   DNS: 8.8.8.8

------------------------------------------------------------------------

# 9. DHCP Relay

``` bash
interface vlan 10
ip helper-address 192.168.99.2

interface vlan 20
ip helper-address 192.168.99.2

interface vlan 30
ip helper-address 192.168.99.3

interface vlan 40
ip helper-address 192.168.99.3
```

------------------------------------------------------------------------

# 10. Verificación

``` bash
show vlan brief
show interfaces trunk
show ip interface brief
```

Pruebas de conectividad:

``` bash
ping 192.168.99.2
ping 192.168.99.3
```

------------------------------------------------------------------------

# 11. Resultado

Los dispositivos conectados a cada VLAN reciben direcciones IP
automáticamente mediante DHCP y pueden comunicarse entre diferentes
redes gracias al **InterVLAN Routing** implementado en el switch
multilayer.

La red implementa:

-   Segmentación mediante VLAN\
-   Enlaces troncales\
-   Spanning Tree para prevención de loops\
-   DHCP centralizado\
-   Routing entre VLANs
