# Implementación de una VPN Site-to-Site IPsec basada en políticas utilizando IKEv1 :contentReference[oaicite:0]{index=0}

## Video demostrativo :contentReference[oaicite:1]{index=1}

[![Ver video en YouTube](https://img.youtube.com/vi/jmrIUCubxno/maxresdefault.jpg)](https://youtu.be/jmrIUCubxno)

---

## I-) Topología de red :contentReference[oaicite:2]{index=2}
La topología implementada consiste en dos redes locales conectadas a través de routers que actúan como peers de la VPN, con un router adicional que representa el proveedor de servicios de Internet (ISP). Cada sitio posee una red LAN con un host que permite validar la conectividad a través del túnel VPN.  
La estructura general de la topología es la siguiente:

---

## II-)Plan de direccionamiento IP :contentReference[oaicite:3]{index=3}
Para el desarrollo del laboratorio se empleó un esquema de direccionamiento basado en la matrícula 20240918, lo cual permite generar un direccionamiento único dentro de la práctica.  
Las redes utilizadas en el laboratorio son las siguientes:  
Este esquema permite separar claramente las redes LAN de las conexiones WAN utilizadas para el establecimiento del túnel VPN.

---

## III-) Configuración de los hosts :contentReference[oaicite:4]{index=4}
Los hosts utilizados en cada red local fueron configurados mediante VPCS, asignando direcciones IP estáticas junto con su respectiva puerta de enlace predeterminada. Esto permite que el tráfico generado desde cada host pueda ser encaminado hacia el router correspondiente y posteriormente hacia la VPN.  
La configuración realizada en los hosts es la siguiente:  
Esta configuración permite que cada host tenga conectividad con su router local antes de establecer el túnel VPN.

---

## IV-) Configuración del router ISP :contentReference[oaicite:5]{index=5}
El router ISP actúa únicamente como dispositivo de tránsito entre ambos routers. No participa en el establecimiento del túnel IPsec, sino que simplemente enruta el tráfico entre las redes WAN de los routers R1 y R2.  
Las interfaces fueron configuradas con direcciones pertenecientes a las subredes WAN y se habilitó el encaminamiento de paquetes entre ellas.  
Configuración aplicada:

---

## V-) Configuración básica de los routers VPN :contentReference[oaicite:6]{index=6}
Antes de implementar la VPN fue necesario configurar las interfaces de red, las direcciones IP correspondientes y las rutas estáticas que permiten la conectividad entre ambos sitios a través del router ISP.

Configuración de R1  
● El router R1 fue configurado con una interfaz WAN conectada al ISP y una interfaz LAN conectada a la red local 10.9.18.0/24.  
● También se añadieron rutas estáticas que permiten alcanzar la red remota y una ruta por defecto hacia el ISP.  

Configuración aplicada en el R1 y R2:

---

## VI-) Implementación de la VPN IPsec basada en políticas :contentReference[oaicite:7]{index=7}
La implementación de la VPN se basa en una arquitectura policy-based, donde el tráfico que debe ser protegido se define mediante listas de acceso. Este tráfico es identificado por el router y posteriormente cifrado mediante IPsec antes de ser enviado al peer remoto.

El proceso de establecimiento del túnel se divide en dos fases:  
Fase 1 (IKEv1): negociación de parámetros de seguridad para el canal de control.  
Fase 2 (IPsec): creación del túnel cifrado para proteger el tráfico entre las redes.

Configuración en R1  
En el router R1 se definió una lista de acceso que identifica el tráfico entre las redes 10.9.18.0/24 y 10.9.19.0/24. Posteriormente se configuró la política ISAKMP, la clave compartida y el transform set utilizado por IPsec.  
Configuración aplicada:  

El router R2 fue configurado de forma similar, invirtiendo las redes en la lista de acceso y utilizando la dirección IP del peer correspondiente.

---

## VII-) Verificación del establecimiento del túnel VPN :contentReference[oaicite:8]{index=8}
Una vez configurado el túnel IPsec, se generó tráfico entre las redes LAN para activar el proceso de negociación y verificar la conectividad.  
Desde el host PC-A se realizó un ping hacia el host PC-B:  

Es normal que los primeros paquetes ICMP fallen debido a que durante ese momento se está realizando la negociación de seguridad entre los routers.

---

## VIII-) Verificación del estado de la VPN :contentReference[oaicite:9]{index=9}
Para confirmar que la VPN se estableció correctamente se utilizaron comandos de verificación en los routers con el comando:  
show crypto isakmp sa  

negociación se completaron correctamente y que el túnel está listo para transportar tráfico cifrado.

Posteriormente se utilizó el comando:  
show crypto ipsec sa  

Este comando muestra estadísticas del túnel IPsec, incluyendo los contadores encaps y decaps, los cuales representan los paquetes cifrados y descifrados respectivamente.  
El incremento de estos contadores confirma que el tráfico entre las redes está siendo protegido correctamente mediante IPsec.

---

## IX-) Conclusión :contentReference[oaicite:10]{index=10}
En este laboratorio se implementó exitosamente una VPN Site-to-Site basada en políticas utilizando IPsec con IKEv1, permitiendo la comunicación segura entre dos redes LAN a través de una red pública simulada.

Mediante la configuración de políticas ISAKMP, transform sets y crypto maps, se logró establecer un túnel cifrado entre los routers R1 y R2. Las pruebas de conectividad realizadas entre los hosts confirmaron el correcto funcionamiento del túnel, mientras que los comandos de verificación demostraron que las asociaciones de seguridad se establecieron correctamente y que el tráfico fue cifrado y descifrado de forma adecuada.

Este tipo de implementación es ampliamente utilizado en entornos empresariales para interconectar sucursales de forma segura a través de Internet, garantizando la confidencialidad, integridad y autenticidad de la información transmitida.
