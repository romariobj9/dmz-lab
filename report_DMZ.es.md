# Informe de configuración de DMZ con Cisco Packet Tracer

**1. Objetivo del laboratorio**

Configurar una DMZ segura utilizando un router Cisco ISR como firewall, implementando NAT estático
para exponer un servidor web a Internet y aplicando Listas de Control de Acceso (ACLs) para controlar el
tráfico entre la red interna (LAN), la DMZ y la red externa (Internet), garantizando que solo el tráfico
web sea permitido desde Internet hacia la DMZ y bloqueando completamente el acceso desde la DMZ
hacia la LAN interna.

**2. Topología implementada**
    - **Cantidad de redes:** 3 redes separadas (LAN Interna, DMZ, Red Externa)
    - **Dispositivos usados:**
       o 1 Router Cisco ISR (2911/4331) como Router_FW

```
o 3 Switches Cisco 2960 (SW_Internal, SW_DMZ, SW_External)
o PC_Internal (LAN)
```
```
o Server-PT Web_DMZ (DMZ)
o PC_External (Internet)
```
- **Descripción de zonas:**
    o **LAN Interna (192.168.1.0/24):** Red privada donde se encuentran los usuarios internos
       de la organización.
    o **DMZ (192.168.2.0/24):** Zona desmilitarizada donde se aloja el servidor web accesible
       desde Internet.
    o **Red Externa (192.168.3.0/24):** Simula Internet, desde donde los clientes externos
       acceden a los servicios.
**3. Plan de direccionamiento IP**

```
Dispositivo IP Máscara Gateway
```
```
PC_Internal 192.168.1.10 255.255.255.0 192.168.1.
```
```
Server_DMZ 192.168.2.10 255.255.255.0 192.168.2.
```
```
PC_External 192.168.3.10 255.255.255.0 192.168.3.
```

```
Dispositivo IP Máscara Gateway
```
```
Router_FW Gi0/0 (LAN) 192.168.1.1 255.255.255.0 N/A
```
```
Router_FW Gi0/1 (DMZ) 192.168.2.1 255.255.255.0 N/A
```
```
Router_FW Gi0/2 (Ext) 192.168.3.1 255.255.255.0 N/A
```
**4. Configuración aplicada (resumen)**
    - **Configuración de interfaces:**
       interface GigabitEthernet0/

```
ip address 192.168.1.1 255.255.255.
no shutdown
```
```
interface GigabitEthernet0/
```
```
ip address 192.168.2.1 255.255.255.
no shutdown
```
```
interface GigabitEthernet0/
```
```
ip address 192.168.3.1 255.255.255.
no shutdown
```
- **Configuración de NAT estático:**
    interface GigabitEthernet0/

```
ip nat inside
```
```
interface GigabitEthernet0/
ip nat outside
```
```
ip nat inside source static 192.168.2.10 192.168.3.
```

- **Configuración de ACLs de seguridad:**

```
! ACL para permitir solo HTTP desde Internet a DMZ
access-list 101 permit tcp any host 192.168.3.1 eq 80
```
```
access-list 101 deny ip any any
interface GigabitEthernet0/
```
```
ip access-group 101 in
```
```
! ACL para bloquear tráfico de DMZ a LAN
access-list 102 deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.
```
```
access-list 102 permit ip any any
interface GigabitEthernet0/
```
```
ip access-group 102 in
```
**5. Verificaciones realizadas**
    - **Ping desde dispositivos a su gateway:**

```
o PC_Internal → 192.168.1.1: - > Completado
```
```
o Server_DMZ → 192.168.2.1: -> Completado
```
```
o PC_External → 192.168.3.1: - > Completado
```
- **Acceso web desde PC_External (antes de ACLs):**

```
o Navegador a 192.168.3.1: -> Página web cargada correctamente
```
- **Acceso web desde PC_Internal:**

```
o Navegador a 192.168.2.10: -> Página web cargada correctamente
```
- **Pruebas de seguridad después de ACLs:**

```
o PC_External → Acceso web a 192.168.3.1: -> Página web accesible
```
```
o PC_External → Ping a 192.168.3.1: -> Request timed out (ICMP bloqueado por ACL 101)
```
```
o Server_DMZ → Ping a 192.168.1.10: -> Request timed out (Tráfico DMZ→LAN
bloqueado por ACL 102)
```

**6. Conclusiones y recomendaciones**

**Aprendizajes obtenidos:**

- Se logró configurar exitosamente una DMZ funcional y segura utilizando NAT estático para
    exponer servicios internos.
- Se comprendió la importancia de las ACLs para controlar el flujo de tráfico entre zonas de
    seguridad diferentes.
- Se verificó que es posible permitir acceso específico (HTTP) desde Internet mientras se bloquea
    otros protocolos no deseados.
- Se confirmó la efectividad del aislamiento de la DMZ, impidiendo que servidores potencialmente
    comprometidos accedan a la red interna.

**Recomendaciones de mejora:**

- Implementar NAT dinámico para los usuarios de la LAN interna que requieran acceso a Internet.
- Considerar la creación de ACLs adicionales para permitir acceso administrativo seguro desde la
    LAN a la DMZ.
- Configurar logging en las ACLs para monitorear intentos de acceso no autorizados.
- Realizar pruebas exhaustivas con diferentes tipos de tráfico para validar completamente las
    políticas de seguridad.
- Documentar todas las reglas de firewall con comentarios descriptivos para facilitar el
    mantenimiento futuro.

**7. Capturas de evidencia**

_(Las capturas de evidencia serán adjuntadas en un archivo como parte del informe final)_