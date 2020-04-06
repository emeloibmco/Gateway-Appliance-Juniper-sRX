# Gateway-Appliance-Juniper-sRX
## Descripción general
---
IBM Cloud ™ Juniper vSRX le permite enrutar selectivamente el tráfico de red pública y privada, a través de un firewall de nivel empresarial con todas las funciones que funciona con características de software de JunOS, como pilas de enrutamiento completas, QoS y tráfico compartido, enrutamiento basado en políticas, y VPN.

A continuación se detalla la configuración de una VPN basada en ruta entre dos *sitios.* En esta configuración de muestra, el Servidor 1 (Sitio A) puede comunicarse con el Servidor 2 (Sitio B), y cada cad uno de estos utiliza el protocolo de autenticación IPSEC de dos fases.

Para configurar su gateway appliance necesitará conocer su IP pública, ara esto aceda al menú de hamburguesa o de tres lineas de su cuenta IBM, seleccione _intraestructura clásica_, luego en la sección _network_ seleccione _Gateway appliance_, al seleccionar su dispositivo encontrará una descripción de este, tome nota de la direccion _IP-gatewayappliance_ y de la _clave-de-acceso_, luego de esto podrá acceder a el mediante las siguientes dos modalidades:

### A. Interfaz J-web
En su buscador ingrese la IP de su gateway appliance junto con el puerto 8443 _Ejemplo:_ 169.62.191.154:8443 De esta forma ingresará a la interfaz gráfica y deberá especificar el usuario: _admin_ y  su contraseña: _clave de acceso_. Encontrará las especificaciones necesarias para la configuración en la sección _Configuration -> security services -> ipsec_ VPN.

### B. Conexión SSH
Abra un Power Shell en su equipo e ingrese el comando de conexión ssh:
```sh
ssh admin@<IP-gatewayappliance>
```
Cuando se le pida la contraseña ingrese la clave anotada como _clave-de-acceso_. Podra rectificar que se encuentra en la cosnola del dispositivo al ver la etiqueta con el nombre que le dio a este.

## Configuración
---
La configuración descrita a continuación se hace desde la consola de comandos a la cual accedió por SSH debido a que esta opción proporciona un mejor control sobre el dispositivo. Esta consola trabaja en dos modalidades, la de operación y la de configuración, todos los pasos a continuación se hara en la modalidad de configuración, para acceder a esta, desde la CLI ingrese: 
```sh
configure
```
Para la configuración del túnel VPN ipsec juniper hace uso del protocolo de intercambio de claves de internet (IKE) el cual trabaja en dos fases:

### FASE 1:
En esta fase se establece un canal Seguro para la comunicación entre dispositivos. Ingrese los siguientes comandos en ambos gateways para la configuración de la fase 1 del protocolo, teniendo en cuenta que la dirección IP-GATEWAY-OP es la dirección ip privada de la interfaz ge-0/0/1 del vSRX opuesto al que esta configurando, la cual podrá ver en el primer comando de los listados a continuación:
```sh
show interfaces
```
```sh
set security ike proposal IKE-PROP lifetime-seconds 3600
set security ike proposal IKE-PROP authentication-method pre-shared-keys
set security ike proposal IKE-PROP authentication-algorithm sha1
set security ike proposal IKE-PROP encryption-algorithm aes-128-cbc
set security ike proposal IKE-PROP dh-group group5

set security ike policy IKE-POL proposals IKE-PROP
set security ike policy IKE-POL mode main
set security ike policy IKE-POL pre-shared-key ascii-text juniper

set security ike gateway IKE-GW ike-policy IKE-POL
set security ike gateway IKE-GW address <IP-GATEWAY-OP>
set security ike gateway IKE-GW external-interface ge-0/0/1

set security zones security-zone
SL-PUBLIC host-inbound-traffic system-services ike
```
### FASE 2:
En esta fase se establece el túnel VPN para el tráfico de red. Ingrese los siguientes comandos en ambos gateways para la configuración de la fase 2 del protocolo, fíjese que se hará uso de la interfaz st0.1 como se mostró en el diagrama de arquitectura.

```sh
set security ipsec proposal IPSEC-PROP lifetime-seconds 3600
set security ipsec proposal IPSEC-PROP protocol esp
set security ipsec proposal IPSEC-PROP authentication-algorithm hmac-sha1-96
set security ipsec proposal IPSEC-PROP encryption-algorithm aes-128-cbc

set security ipsec policy IPSEC-POL proposals IPSEC-PROP
set security ipsec policy IPSEC-POL perfect-forwad-secrecy keys group5

set security ipsec vpn IPSEC-VPN ike gateway IKE-GW
set security ipsec vpn IPSEC-VPN ike ipsec-policy IPSEC-POL
set security ipsec vpn IPSEC-VPN vpn-monitor
set security ipsec vpn IPSEC-VPN establish-tunnels immediately

set security ipsec vpn IPSEC-VPN bind-interface st0.1
```
### Enrutamiento
### Políticas de seguridad

## Comprobación
---
Utilice los siguientes comandos para ver que el tunel ha sido creado y que se encuentra activo, además de identificar las configuraciones anteriormente establecidas.

```sh
show security IKE security-associations
show security ipsec security-associations
```
En caso de que el estado se encuentre en _DOWN_ siga los pasos de la [documantación](https://kb.juniper.net/InfoCenter/index?page=content&id=KB10100&actp=search) proporcionada por juniper.

En caso de necesitar regresar a la configuración incial del vSRX revise los commits realizados usando el comando:
```sh
show system commit
```
Guíese por las fechas en que se realizaron los commits y regrese al _Commit-number_ que necesite con el comando:
```sh
rollback <commit-number>
```
