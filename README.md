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
La configuración descrita a continuación se hace desde la consola de comandos a la cual accedió por SSH debido a que esta opción proporciona un mejor control sobre el dispositivo.


