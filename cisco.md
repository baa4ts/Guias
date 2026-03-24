# Guía de Redes y Subredes en Cisco Packet Tracer

## Índice

1. [Conceptos básicos](#1-conceptos-básicos)
2. [Direccionamiento IP](#2-direccionamiento-ip)
3. [Subredes (Subnetting)](#3-subredes-subnetting)
4. [Topología básica](#4-topología-básica)
5. [Configuración del Router](#5-configuración-del-router)
6. [Configuración del Switch](#6-configuración-del-switch)
7. [Configuración de PCs](#7-configuración-de-pcs)
8. [DHCP — IPs automáticas](#8-dhcp--ips-automáticas)
9. [Verificación y diagnóstico](#9-verificación-y-diagnóstico)
10. [Comandos útiles](#10-comandos-útiles)

---

## 1. Conceptos básicos

| Término | Descripción |
|---|---|
| **IP Address** | Identificador único de cada dispositivo en la red |
| **Máscara de subred** | Define qué parte de la IP es red y qué parte es host |
| **Gateway** | IP del router — puerta de salida de la red |
| **DHCP** | Protocolo que asigna IPs automáticamente |
| **DNS** | Traduce nombres de dominio a IPs |

---

## 2. Direccionamiento IP

### Clases de redes

| Clase | Rango | Máscara por defecto | Uso |
|---|---|---|---|
| **A** | 1.0.0.0 – 126.255.255.255 | /8 → 255.0.0.0 | Redes muy grandes |
| **B** | 128.0.0.0 – 191.255.255.255 | /16 → 255.255.0.0 | Redes medianas |
| **C** | 192.0.0.0 – 223.255.255.255 | /24 → 255.255.255.0 | Redes pequeñas |

### Rangos privados (para uso interno)

| Rango | CIDR |
|---|---|
| 10.0.0.0 – 10.255.255.255 | /8 |
| 172.16.0.0 – 172.31.255.255 | /12 |
| 192.168.0.0 – 192.168.255.255 | /16 |

---

## 3. Subredes (Subnetting)

El subnetting divide una red grande en redes más pequeñas.

### Fórmulas clave

```
Hosts por subred  = 2^(bits de host) - 2
Número de subredes = 2^(bits prestados)
```

### Tabla de máscaras más usadas

| CIDR | Máscara | Hosts útiles | Subredes de una /24 |
|---|---|---|---|
| /24 | 255.255.255.0 | 254 | 1 |
| /25 | 255.255.255.128 | 126 | 2 |
| /26 | 255.255.255.192 | 62 | 4 |
| /27 | 255.255.255.224 | 30 | 8 |
| /28 | 255.255.255.240 | 14 | 16 |
| /29 | 255.255.255.248 | 6 | 32 |
| /30 | 255.255.255.252 | 2 | 64 |

### Ejemplo — dividir 192.168.1.0/24 en 4 subredes (/26)

| Subred | Red | Rango de hosts | Broadcast | Gateway |
|---|---|---|---|---|
| 1 | 192.168.1.0/26 | .1 – .62 | .63 | .1 |
| 2 | 192.168.1.64/26 | .65 – .126 | .127 | .65 |
| 3 | 192.168.1.128/26 | .129 – .190 | .191 | .129 |
| 4 | 192.168.1.192/26 | .193 – .254 | .255 | .193 |

> **Regla:** La IP de red y el broadcast **no se asignan** a hosts.

---

## 4. Topología básica

```
PC0 (192.168.1.10)
    |
    | Fa0/1 (cable directo)
    |
Switch 2960
    |
    | G0/1 (cable directo)
    |
Router 2911 — G0/0 → 192.168.1.1 (gateway)
```

### Tipos de cable

| Cable | Cuándo usarlo |
|---|---|
| Copper Straight-Through (negro sólido) | PC↔Switch, Switch↔Router |
| Copper Cross-Over (negro punteado) | PC↔PC, Switch↔Switch |
| Serial DTE/DCE (celeste) | Router↔Router (WAN) |

---

## 5. Configuración del Router

### Router 2911 — interfaz LAN

```
Router> enable
Router# configure terminal
Router(config)# hostname R1

R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# end
R1# write memory
```

### Router 1841/2811 — interfaz FastEthernet

```
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
```

### Configurar dos subredes en el mismo router

```
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.192
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip address 192.168.1.65 255.255.255.192
R1(config-if)# no shutdown
R1(config-if)# exit
```

---

## 6. Configuración del Switch

El Switch 2960 funciona en **capa 2** y no necesita configuración para redes básicas. Solo conectá los cables.

### Comandos útiles en el switch

```
Switch> enable
Switch# show mac address-table          — ver tabla MAC
Switch# show interfaces status          — ver estado de puertos
Switch# configure terminal
Switch(config)# hostname SW1
SW1(config)# end
SW1# write memory
```

### Asignar IP de gestión al switch (opcional)

```
SW1(config)# interface Vlan1
SW1(config-if)# ip address 192.168.1.2 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit
SW1(config)# ip default-gateway 192.168.1.1
```

---

## 7. Configuración de PCs

En Packet Tracer: **clic en la PC → Desktop → IP Configuration → Static**

| Campo | Valor ejemplo |
|---|---|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |
| DNS Server | 8.8.8.8 (opcional) |

### Desde la terminal de la PC

```
C:\> ipconfig              — ver IP actual
C:\> ping 192.168.1.1      — probar conectividad al router
C:\> ping 192.168.1.20     — probar conectividad a otra PC
```

---

## 8. DHCP — IPs automáticas

Configurá el router para que asigne IPs automáticamente a las PCs.

```
R1(config)# ip dhcp pool LAN
R1(dhcp-config)# network 192.168.1.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.1.1
R1(dhcp-config)# dns-server 8.8.8.8
R1(dhcp-config)# exit

R1(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
```

> `excluded-address` reserva IPs que no se asignarán (para el router, switches, servidores).

En la PC: **IP Configuration → DHCP** — la IP se asigna sola.

### Verificar DHCP

```
R1# show ip dhcp pool
R1# show ip dhcp binding        — ver qué IP se asignó a cada MAC
```

---

## 9. Verificación y diagnóstico

### En el router

```
R1# show ip interface brief          — estado de todas las interfaces
R1# show running-config              — configuración actual completa
R1# show ip route                    — tabla de enrutamiento
R1# show version                     — info del equipo
```

### Interpretando show ip interface brief

```
Interface          IP-Address    OK? Method Status    Protocol
GigabitEthernet0/0 192.168.1.1  YES manual up        up       ← OK
GigabitEthernet0/1 unassigned   YES unset  admin down down     ← sin config
```

| Status / Protocol | Significado |
|---|---|
| up / up | Enlace activo — correcto |
| up / down | Problema de capa 2 (cable, otro extremo) |
| admin down / down | Interface apagada — falta `no shutdown` |

### Indicadores de cable en Packet Tracer

| Color | Significado |
|---|---|
| 🟢 Verde | Enlace activo |
| 🔴 Rojo | Problema — interface apagada o cable incorrecto |
| 🟡 Naranja | Negociando — esperar unos segundos |

---

## 10. Comandos útiles

### Navegación entre modos

```
Router>           — modo usuario (solo lectura)
Router> enable    — entrar a modo privilegiado
Router#           — modo privilegiado
Router# configure terminal   — entrar a configuración global
Router(config)#   — modo configuración global
Router(config)# interface Gi0/0   — entrar a configurar interfaz
Router(config-if)#
Router(config-if)# end     — volver a modo privilegiado
Router# exit               — salir
```

### Abreviaciones aceptadas por IOS

| Comando completo | Abreviación |
|---|---|
| `enable` | `en` |
| `configure terminal` | `conf t` |
| `interface GigabitEthernet0/0` | `int gi0/0` |
| `interface FastEthernet0/0` | `int fa0/0` |
| `no shutdown` | `no sh` |
| `show ip interface brief` | `sh ip int br` |
| `write memory` | `wr` |

### Guardar configuración

```
R1# write memory
— o —
R1# copy running-config startup-config
```

> Si no guardás, la configuración se pierde al reiniciar.

---

*Guía elaborada para Cisco Packet Tracer — modelos 2911 y 2960-24TT*
