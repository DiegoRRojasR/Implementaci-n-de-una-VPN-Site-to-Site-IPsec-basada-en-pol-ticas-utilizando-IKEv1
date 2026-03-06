# Implementación de una VPN Site-to-Site IPsec basada en políticas utilizando IKEv1 :contentReference[oaicite:0]{index=0}

## Video demostrativo :contentReference[oaicite:1]{index=1}

[![Ver video en YouTube](https://img.youtube.com/vi/jmrIUCubxno/maxresdefault.jpg)](https://youtu.be/jmrIUCubxno)

---

## I-) Topología de red
La topología implementada consiste en dos redes locales conectadas a través de routers que actúan como peers de la VPN, con un router adicional que representa el proveedor de servicios de Internet (ISP). Cada sitio posee una red LAN con un host que permite validar la conectividad a través del túnel VPN.  
La estructura general de la topología es la siguiente:

<img width="930" height="759" alt="Screenshot 2026-03-05 211415" src="https://github.com/user-attachments/assets/22bf829a-3b47-4528-a568-dd5f8a94b818" />

---

## II-)Plan de direccionamiento IP
Para el desarrollo del laboratorio se empleó un esquema de direccionamiento basado en la matrícula 20240918, lo cual permite generar un direccionamiento único dentro de la práctica.  
Las redes utilizadas en el laboratorio son las siguientes:  
Este esquema permite separar claramente las redes LAN de las conexiones WAN utilizadas para el establecimiento del túnel VPN.

<img width="655" height="281" alt="Screenshot 2026-03-05 211844" src="https://github.com/user-attachments/assets/9d955a9c-e49b-407f-9fbe-32c681b8f320" />

---

## III-) Configuración de los hosts
Los hosts utilizados en cada red local fueron configurados mediante VPCS, asignando direcciones IP estáticas junto con su respectiva puerta de enlace predeterminada. Esto permite que el tráfico generado desde cada host pueda ser encaminado hacia el router correspondiente y posteriormente hacia la VPN.  
La configuración realizada en los hosts es la siguiente:  

<img width="640" height="152" alt="Screenshot 2026-03-05 211941" src="https://github.com/user-attachments/assets/175236df-9cef-4587-b5cd-8b566966ce06" />

<img width="628" height="124" alt="Screenshot 2026-03-05 212007" src="https://github.com/user-attachments/assets/0b4a9301-fad0-4ac8-acd5-2923a5564a32" />

Esta configuración permite que cada host tenga conectividad con su router local antes de establecer el túnel VPN.

---

## IV-) Configuración del router ISP
El router ISP actúa únicamente como dispositivo de tránsito entre ambos routers. No participa en el establecimiento del túnel IPsec, sino que simplemente enruta el tráfico entre las redes WAN de los routers R1 y R2.  
Las interfaces fueron configuradas con direcciones pertenecientes a las subredes WAN y se habilitó el encaminamiento de paquetes entre ellas.  
Configuración aplicada:

<img width="603" height="131" alt="Screenshot 2026-03-05 212146" src="https://github.com/user-attachments/assets/6bd8a264-6b22-4938-a407-3ccbc1b5083a" />

<img width="612" height="141" alt="Screenshot 2026-03-05 212503" src="https://github.com/user-attachments/assets/c2e501ff-cda0-4254-ae29-15da91783d8c" />

---

## V-) Configuración básica de los routers VPN
Antes de implementar la VPN fue necesario configurar las interfaces de red, las direcciones IP correspondientes y las rutas estáticas que permiten la conectividad entre ambos sitios a través del router ISP.

Configuración de R1  
● El router R1 fue configurado con una interfaz WAN conectada al ISP y una interfaz LAN conectada a la red local 10.9.18.0/24.  
● También se añadieron rutas estáticas que permiten alcanzar la red remota y una ruta por defecto hacia el ISP.  

Configuración aplicada en el R1 y R2:

<img width="557" height="339" alt="Screenshot 2026-03-05 212522" src="https://github.com/user-attachments/assets/146dbe7a-e9a6-493b-8c13-0d68c83661db" />

<img width="567" height="337" alt="Screenshot 2026-03-05 212542" src="https://github.com/user-attachments/assets/2e413fc0-0ce0-4f41-9a26-54204e7b5931" />

---

## VI-) Implementación de la VPN IPsec basada en políticas
La implementación de la VPN se basa en una arquitectura policy-based, donde el tráfico que debe ser protegido se define mediante listas de acceso. Este tráfico es identificado por el router y posteriormente cifrado mediante IPsec antes de ser enviado al peer remoto.

El proceso de establecimiento del túnel se divide en dos fases:  
Fase 1 (IKEv1): negociación de parámetros de seguridad para el canal de control.  
Fase 2 (IPsec): creación del túnel cifrado para proteger el tráfico entre las redes.

Configuración en R1  
En el router R1 se definió una lista de acceso que identifica el tráfico entre las redes 10.9.18.0/24 y 10.9.19.0/24. Posteriormente se configuró la política ISAKMP, la clave compartida y el transform set utilizado por IPsec.  
Configuración aplicada:

<img width="419" height="213" alt="Screenshot 2026-03-05 212745" src="https://github.com/user-attachments/assets/ae47a3ba-27f6-42e1-b09a-cdf5423cd3a4" />

El router R2 fue configurado de forma similar, invirtiendo las redes en la lista de acceso y utilizando la dirección IP del peer correspondiente.

<img width="409" height="216" alt="Screenshot 2026-03-05 212848" src="https://github.com/user-attachments/assets/ff1d9514-7a1c-4d2c-a640-1f45f2a53b4c" />

---

## VII-) Verificación del establecimiento del túnel VPN
Una vez configurado el túnel IPsec, se generó tráfico entre las redes LAN para activar el proceso de negociación y verificar la conectividad.  
Desde el host PC-A se realizó un ping hacia el host PC-B:  

<img width="434" height="143" alt="Screenshot 2026-03-05 213341" src="https://github.com/user-attachments/assets/37b71113-de1e-4c96-a236-272123365310" />

Es normal que los primeros paquetes ICMP fallen debido a que durante ese momento se está realizando la negociación de seguridad entre los routers.

---

## VIII-) Verificación del estado de la VPN
Para confirmar que la VPN se estableció correctamente se utilizaron comandos de verificación en los routers con el comando:  
show crypto isakmp sa  

negociación se completaron correctamente y que el túnel está listo para transportar tráfico cifrado.

<img width="492" height="78" alt="Screenshot 2026-03-05 213602" src="https://github.com/user-attachments/assets/72f05440-ec4e-416e-a25b-4f0339ccad81" />

<img width="440" height="71" alt="Screenshot 2026-03-05 213612" src="https://github.com/user-attachments/assets/ab7475ec-ef08-4f5d-8cab-4050b9d90b66" />

Posteriormente se utilizó el comando:  
show crypto ipsec sa  

Este comando muestra estadísticas del túnel IPsec, incluyendo los contadores encaps y decaps, los cuales representan los paquetes cifrados y descifrados respectivamente.  
El incremento de estos contadores confirma que el tráfico entre las redes está siendo protegido correctamente mediante IPsec.

<img width="607" height="316" alt="Screenshot 2026-03-05 213701" src="https://github.com/user-attachments/assets/e8a9e5ec-a73b-415a-acd6-bf971740e215" />

<img width="629" height="353" alt="Screenshot 2026-03-05 213730" src="https://github.com/user-attachments/assets/92893d36-934e-4aee-adbc-2e03076a4b6b" />

---

## IX-) Conclusión
En este laboratorio se implementó exitosamente una VPN Site-to-Site basada en políticas utilizando IPsec con IKEv1, permitiendo la comunicación segura entre dos redes LAN a través de una red pública simulada.

Mediante la configuración de políticas ISAKMP, transform sets y crypto maps, se logró establecer un túnel cifrado entre los routers R1 y R2. Las pruebas de conectividad realizadas entre los hosts confirmaron el correcto funcionamiento del túnel, mientras que los comandos de verificación demostraron que las asociaciones de seguridad se establecieron correctamente y que el tráfico fue cifrado y descifrado de forma adecuada.

Este tipo de implementación es ampliamente utilizado en entornos empresariales para interconectar sucursales de forma segura a través de Internet, garantizando la confidencialidad, integridad y autenticidad de la información transmitida.
