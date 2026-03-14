# Manual Técnico -- Proyecto de Red Empresarial

## Curso: Redes 2

**Estudiante:** Luis Fernando Gomez\
**Carnet:** 201801391

------------------------------------------------------------------------

# 1. Objetivo del proyecto

Diseñar e implementar una red empresarial segmentada utilizando **Cisco
Packet Tracer**, aplicando conceptos de:

-   VLANs
-   Enlaces troncales (Trunk)
-   Spanning Tree Protocol
-   Switch Multilayer
-   InterVLAN Routing
-   DHCP centralizado
-   DHCP Relay (ip helper-address)

El objetivo principal es lograr **segmentación lógica de la red y
asignación automática de direcciones IP** para los dispositivos
conectados.

------------------------------------------------------------------------

# 2. Arquitectura de la red

La red fue diseñada utilizando una **arquitectura jerárquica** compuesta
por:

### Capa Core / Multilayer

Switches encargados de:

-   InterVLAN Routing
-   Gestión de tráfico entre VLANs
-   Conexión entre edificios
-   Conexión a servidores

### Capa de Distribución

Switches que conectan múltiples switches de acceso y agregan enlaces
hacia la capa core.

### Capa de Acceso

Switches donde se conectan:

-   PCs
-   Laptops
-   Dispositivos finales

------------------------------------------------------------------------

# 3. Topología general

La topología incluye:

-   2 servidores DHCP
-   switches multilayer
-   switches de acceso
-   múltiples PCs y laptops
-   enlaces redundantes entre switches
-   enlaces troncales entre capas

Los servidores están conectados a la **VLAN de administración (VLAN
99)**.

------------------------------------------------------------------------

# 4. VLANs implementadas

  VLAN   Nombre                     Red
  ------ -------------------------- -----------------
  10     VLAN_NARANJA_EdificioIZQ   192.168.10.0/24
  20     VLAN_VERDE_EdificioIZQ     192.168.20.0/24
  30     VLAN_NARANJA_EdificioDER   192.168.30.0/24
  40     VLAN_VERDE_EdificioDER     192.168.40.0/24
  99     VLAN_ADMIN                 192.168.99.0/24

Cada VLAN representa un **segmento lógico de red independiente**.

------------------------------------------------------------------------

# 5. Creación de VLANs

Las VLANs fueron creadas en los switches mediante:

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

Verificación:

``` bash
show vlan brief
```

------------------------------------------------------------------------

# 6. Configuración de enlaces troncales

Los enlaces entre switches fueron configurados como **trunk** para
permitir el transporte de múltiples VLANs.

``` bash
interface gigabitEthernet1/0/1
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 10,20,30,40,99
```

Configuración aplicada a múltiples interfaces usando:

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

# 7. Configuración de EtherChannel

Para mejorar la capacidad de los enlaces y proporcionar redundancia se
configuró **EtherChannel**.

``` bash
interface range gigabitEthernet1/0/1-2
channel-group 2 mode active
```

Configuración del Port Channel:

``` bash
interface port-channel2
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 10,20,30,40,99
```

------------------------------------------------------------------------

# 8. Spanning Tree Protocol

El protocolo **STP** fue utilizado para prevenir bucles de red causados
por enlaces redundantes.

Verificación:

``` bash
show spanning-tree
```

En el resultado se puede observar:

-   Root Bridge
-   Puertos en estado Forwarding
-   Puertos bloqueados para evitar loops

------------------------------------------------------------------------

# 9. Switch Multilayer y Routing

Para permitir comunicación entre VLANs se habilitó **IP routing** en el
switch multilayer.

``` bash
configure terminal
ip routing
```

------------------------------------------------------------------------

# 10. Interfaces VLAN (SVI)

Cada VLAN tiene una interfaz virtual que actúa como **gateway de la
red**.

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

# 11. Servidores DHCP

La red utiliza **dos servidores DHCP** para distribuir direcciones IP
automáticamente.

### Server0

Proporciona direcciones para:

-   VLAN 10
-   VLAN 20

Configuración de pools:

**VLAN10**

-   Gateway: 192.168.10.1
-   Start IP: 192.168.10.10
-   Mask: 255.255.255.0
-   DNS: 8.8.8.8

**VLAN20**

-   Gateway: 192.168.20.1
-   Start IP: 192.168.20.10
-   Mask: 255.255.255.0
-   DNS: 8.8.8.8

------------------------------------------------------------------------

### Server1

Proporciona direcciones para:

-   VLAN 30
-   VLAN 40

**VLAN30**

-   Gateway: 192.168.30.1
-   Start IP: 192.168.30.10
-   Mask: 255.255.255.0
-   DNS: 8.8.8.8

**VLAN40**

-   Gateway: 192.168.40.1
-   Start IP: 192.168.40.10
-   Mask: 255.255.255.0
-   DNS: 8.8.8.8

------------------------------------------------------------------------

# 12. Configuración de DHCP Relay

Debido a que los servidores DHCP se encuentran en otra VLAN, fue
necesario configurar **ip helper-address**.

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

Esto permite que los broadcasts DHCP sean enviados hacia los servidores
correspondientes.

------------------------------------------------------------------------

# 13. Verificación de funcionamiento

Comandos utilizados para verificar la red.

### VLANs

``` bash
show vlan brief
```

### Trunks

``` bash
show interfaces trunk
```

### Interfaces

``` bash
show ip interface brief
```

### Spanning Tree

``` bash
show spanning-tree
```

------------------------------------------------------------------------

# 14. Pruebas de conectividad

Se realizaron pruebas de conectividad utilizando **ping**.

Ejemplo:

``` bash
ping 192.168.99.2
ping 192.168.99.3
```

Resultados observados:

-   conectividad exitosa con los servidores
-   asignación automática de IP mediante DHCP
-   comunicación entre VLANs gracias al multilayer switch

------------------------------------------------------------------------

# 15. Resultados obtenidos

Durante las pruebas se verificó:

-   correcto funcionamiento de DHCP
-   asignación automática de direcciones IP
-   funcionamiento de enlaces troncales
-   funcionamiento de STP
-   comunicación entre VLANs mediante routing
