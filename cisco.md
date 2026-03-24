# 📡 Redes Básicas – Cisco Packet Tracer

---

## 🔹 Sección 1: Máscaras de Subred y Hosts Disponibles

### 🧠 Concepto

La **máscara de subred** define cuántos dispositivos (hosts) pueden existir en una red y qué rango de IPs pertenece a la misma red.

---

### 📊 Tabla de Máscaras Comunes

| Máscara de Subred | CIDR | Hosts Disponibles | Rango de IPs por Red          |
| ----------------- | ---- | ----------------- | ----------------------------- |
| 255.0.0.0         | /8   | 16,777,214        | 192.0.0.1 - 192.255.255.254   |
| 255.255.0.0       | /16  | 65,534            | 192.168.0.1 - 192.168.255.254 |
| 255.255.255.0     | /24  | 254               | 192.168.1.1 - 192.168.1.254   |
| 255.255.255.128   | /25  | 126               | 192.168.1.1 - 192.168.1.126   |
| 255.255.255.192   | /26  | 62                | 192.168.1.1 - 192.168.1.62    |
| 255.255.255.224   | /27  | 30                | 192.168.1.1 - 192.168.1.30    |
| 255.255.255.240   | /28  | 14                | 192.168.1.1 - 192.168.1.14    |
| 255.255.255.248   | /29  | 6                 | 192.168.1.1 - 192.168.1.6     |
| 255.255.255.252   | /30  | 2                 | 192.168.1.1 - 192.168.1.2     |

---

### 🔹 Regla Importante

* Se restan 2 direcciones:

  * 1 para la **red**
  * 1 para **broadcast**

```
Hosts = 2^n - 2
```

---

## 🔹 Sección 2: Configurar IP en Router Cisco 2911

### 🎯 Objetivo

Asignar dirección IP y máscara a una interfaz del router para conectar redes distintas.

---

### 🖥️ Pasos básicos en CLI

1. Entrar al modo privilegiado:

```
enable
```

2. Entrar a configuración global:

```
configure terminal
```

3. Seleccionar interfaz y asignar IP:

```
interface gigabitEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit
```

4. Salvar configuración:

```
copy running-config startup-config
```

* También funciona `write memory`.

---

### 🔹 Verificación de interfaces

```
show ip interface brief
```

* **Status = up / Protocol = up** → interfaz lista
* **Administratively down** → interfaz apagada (`shutdown`)

---

### 🔹 Ejemplo de tabla de redes en router

| Interfaz | IP          | Máscara       | Red         |
| -------- | ----------- | ------------- | ----------- |
| G0/0     | 192.168.1.1 | 255.255.255.0 | 192.168.1.0 |
| G0/1     | 192.168.2.1 | 255.255.255.0 | 192.168.2.0 |

* Cada interfaz corresponde a **una red distinta**
* Router enruta automáticamente entre ellas

---

## 🔹 Sección 3: Configurar DHCP en Router Cisco 2911

### 🧠 Concepto

* **DHCP** = Dynamic Host Configuration Protocol
* Permite que PCs obtengan automáticamente: IP, máscara, gateway y DNS.

---

### 🔧 Pasos de configuración básica

1. Crear un pool DHCP:

```
ip dhcp pool RED1
```

2. Asignar red y máscara:

```
network 192.168.1.0 255.255.255.0
```

3. Asignar gateway (default router):

```
default-router 192.168.1.1
```

4. (Opcional) Configurar DNS:

```
dns-server 8.8.8.8
exit
```

5. Excluir IPs que no quieres que DHCP asigne:

```
ip dhcp excluded-address 192.168.1.1 192.168.1.5
```

---

### 🔹 Verificar DHCP

```
show ip dhcp binding
show running-config
```

* En las PCs, configurar **Obtain IP automatically** → verificar con `ipconfig`.

---

## 🔹 Sección 4: Verificar conexiones y puertos

### 1️⃣ Router

```
show ip interface brief
```

* Muestra IPs, estado y protocolo

### 2️⃣ Switch

```
show interfaces status
```

* Muestra qué puertos están conectados (`connected`), velocidad, VLAN

```
show cdp neighbors
```

* Muestra dispositivos conectados a cada puerto (útil para verificar Router ↔ Switch)

---

### 🔹 Tips finales

* Cada PC necesita **su gateway correcto** para comunicarse con otra red
* Router con interfaces activas enruta entre redes automáticamente
* Switch capa 2 solo mueve tráfico dentro de la misma VLAN

---

## 🔹 Resumen de laboratorio funcional

1. **Router 2911** → G0/0 = 192.168.1.1 /24, G0/1 = 192.168.2.1 /24
2. **Switch 1 → PC1** → IP 192.168.1.2 /24, gateway 192.168.1.1
3. **Switch 2 → PC2** → IP 192.168.2.2 /24, gateway 192.168.2.1
4. Ping funciona entre PCs y routers ✅
5. DHCP opcional para asignar IPs automáticas ✅

---
