# 📡 Redes Básicas – Máscaras y Configuración en Router

---

# 🔹 Sección 1: Máscaras de Subred y Hosts Disponibles

## 🧠 Concepto

La máscara de subred define cuántos dispositivos (hosts) pueden existir en una red y qué rango de IPs pertenece a la misma red.

---

## 📊 Tabla de Máscaras Comunes

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

## 🔥 Regla Importante

* Siempre se restan 2 direcciones:

  * 1 para la red
  * 1 para broadcast

👉 Fórmula:

```
Hosts = 2^n - 2
```

---

## 🧠 Ejemplo

```
/24 → 8 bits para hosts
2^8 = 256 → 256 - 2 = 254 hosts
```

---

## ✅ Recomendación para principiantes

* Usa siempre:

```
255.255.255.0 (/24)
```

✔ Fácil de entender
✔ 254 dispositivos disponibles
✔ Ideal para prácticas en Packet Tracer

---

## 🚀 Tip rápido

| Necesitas...        | Usa       |
| ------------------- | --------- |
| Muchas PCs          | /24       |
| Pocas PCs           | /27 o /28 |
| Solo 2 dispositivos | /30       |

---

# 🔹 Qué hace la máscara de red

* La máscara determina **qué IPs están en la misma red**.
* Ejemplo: `192.168.1.1 /24` → ve todos los hosts de `192.168.1.1` a `192.168.1.254`.
* Todo fuera de esa red necesita que el **router haga enrutamiento**.

---

# 🔧 Sección 2: Configurar IP en Router Cisco 2911

## 🎯 Objetivo

Asignar una dirección IP y máscara a una interfaz del router para conectar redes distintas.

---

## 🖥️ Pasos básicos en CLI

1. Entrar al modo privilegiado:

```
enable
```

2. Entrar a configuración global:

```
configure terminal
```

3. Seleccionar la interfaz:

```
interface gigabitEthernet 0/0
```

4. Asignar IP y máscara:

```
ip address 192.168.1.1 255.255.255.0
```

5. Activar la interfaz:

```
no shutdown
```

6. Salir de la interfaz:

```
exit
```

---

## 💾 Guardar configuración

```
copy running-config startup-config
```

* `write memory` también funciona, es la versión corta.
* Si no guardas, la config se pierde al reiniciar.

---

## 🔍 Verificar configuración

```
show ip interface brief
```

👉 Debe mostrar:

* IP asignada
* Estado: up

---

## 🔹 Reglas para subredes en router

* Cada interfaz = **una red diferente**
* Misma máscara, diferente número de red → ✅ subred válida
* Misma máscara, mismo número de red → ❌ confusión
* Diferente máscara → solo si sabes subnetting avanzado

---

## 🔹 Ejemplo práctico con 2 redes

| Interfaz | IP          | Máscara       | Red         |
| -------- | ----------- | ------------- | ----------- |
| G0/0     | 192.168.1.1 | 255.255.255.0 | 192.168.1.0 |
| G0/1     | 192.168.2.1 | 255.255.255.0 | 192.168.2.0 |

* Ahora sí son **2 redes separadas**
* Router puede enrutar entre ellas
* PCs en cada red usan como gateway la IP de su interfaz correspondiente

---

## 🧠 Tip clave

* IP = “dirección del dispositivo”
* Máscara = “qué parte del vecindario pertenece a la misma red”
* Router = “conecta vecindarios distintos”

---
