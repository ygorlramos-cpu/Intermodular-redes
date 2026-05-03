# 🏢 Red Empresarial — Cisco Packet Tracer

> **Módulo:** Planificación y Administración de Redes  
> **Herramienta:** Cisco Packet Tracer  
> **Curso:** 2025–2026 | Departamento de IT

---

## 📋 Descripción del proyecto

Diseño e implementación de una **infraestructura de red profesional, segura y escalable** para una empresa de dos plantas, utilizando Cisco Packet Tracer. La red incluye segmentación por VLANs, enrutamiento inter-VLAN, políticas de seguridad mediante ACLs, asignación automática de IPs por DHCP y red inalámbrica con dos SSIDs independientes.

---

## 🗂️ Estructura del repositorio

```
redes/
├── red_empresarial.pkt          ← Archivo Cisco Packet Tracer
├── documentacion.pdf            ← Documentación técnica completa
├── guia_paso_a_paso.pdf         ← Guía de implementación en PT
└── README.md                    ← Este archivo
```

---

## 🏗️ Topología de red

### Distribución por plantas

| Planta | Departamentos |
|--------|--------------|
| **Planta baja** | Recepción, Administración, Dirección, CPD (Sala de servidores) |
| **Primera planta** | Desarrollo, Soporte Técnico, Aula de Formación |

### Arquitectura lógica

```
           INTERNET / ISP
                │
         [Router-Central]
         Cisco 2911
         DHCP · ACLs · WAN
                │
        [SW-Distribucion]
        Switch 3560 — Capa 3
        Inter-VLAN Routing
         ┌──────┴──────┐
    [SW-PB]          [SW-PA]
   Switch 2960      Switch 2960
   Planta Baja     Primera Planta
  ┌──┬──┬──┐       ┌──┬──┬──┐
 ADM DIR SRV      DEV SOP FORM+AP
```

---

## 🔌 VLANs configuradas

| VLAN | Nombre | Departamento | Red IP | Gateway |
|------|--------|-------------|--------|---------|
| 10 | ADMIN | Administración | 192.168.10.0/24 | 192.168.10.1 |
| 20 | DIR | Dirección | 192.168.20.0/24 | 192.168.20.1 |
| 30 | DEV | Desarrollo | 192.168.30.0/24 | 192.168.30.1 |
| 40 | SOPORTE | Soporte Técnico | 192.168.40.0/24 | 192.168.40.1 |
| 50 | FORMACION | Aula de Formación | 192.168.50.0/24 | 192.168.50.1 |
| 60 | SRV | Servidores (CPD) | 192.168.60.0/24 | 192.168.60.1 |
| 99 | MGMT | Gestión de red | 192.168.99.0/24 | 192.168.99.1 |

---

## 🌐 Direccionamiento IP — Dispositivos fijos

| Dispositivo | IP | VLAN | Rol |
|---|---|---|---|
| Router-Central | 192.168.99.1 | 99 | Gateway WAN / DHCP / ACLs |
| SW-Distribucion | 192.168.99.2 | 99 | Switch L3 / Inter-VLAN |
| SW-PB | 192.168.99.3 | 99 | Switch acceso planta baja |
| SW-PA | 192.168.99.4 | 99 | Switch acceso primera planta |
| SRV-DHCP/DNS | 192.168.60.10 | 60 | Servicios de red |
| SRV-Web | 192.168.60.11 | 60 | Aplicaciones web |
| AP-WiFi | 192.168.99.10 | 99 | Punto de acceso inalámbrico |

### Rangos DHCP por VLAN

| VLAN | Rango asignado automáticamente |
|------|-------------------------------|
| ADMIN (10) | 192.168.10.100 – 192.168.10.200 |
| DIR (20) | 192.168.20.100 – 192.168.20.200 |
| DEV (30) | 192.168.30.100 – 192.168.30.200 |
| SOPORTE (40) | 192.168.40.100 – 192.168.40.200 |
| FORMACION (50) | 192.168.50.100 – 192.168.50.200 |

---

## 🔒 Políticas de seguridad (ACLs)

| # | Política | Origen → Destino | Acción |
|---|----------|-----------------|--------|
| 1 | Formación no puede acceder a Dirección | VLAN 50 → VLAN 20 | ❌ DENY |
| 2 | Desarrollo no puede acceder a Administración | VLAN 30 → VLAN 10 | ❌ DENY |
| 3 | Administración sí puede acceder a Servidores | VLAN 10 → VLAN 60 | ✅ PERMIT |
| 4 | Solo MGMT puede administrar dispositivos de red | Otras VLANs → VLAN 99 | ❌ DENY |

Las ACLs se aplican como **ACLs extendidas** en las subinterfaces del Router-Central, en dirección `in` (tráfico entrante de cada VLAN).

---

## 📶 Red inalámbrica (WiFi)

| SSID | VLAN | Seguridad | Acceso |
|------|------|-----------|--------|
| `SSID_EMPRESA` | VLAN 30 (DEV) | WPA2-PSK | Red interna (regulado por ACLs) |
| `SSID_INVITADOS` | VLAN 50 aislada | WPA2 abierta | Solo Internet — bloqueado de redes internas |

---

## ⚙️ Equipamiento utilizado

| Dispositivo | Modelo PT | Cantidad | Función |
|---|---|---|---|
| Router | Cisco 2911 | 1 | WAN, DHCP, ACLs |
| Switch L3 (distribución) | Cisco 3560-24PS | 1 | Inter-VLAN routing |
| Switch acceso planta baja | Cisco 2960-24TT | 1 | Acceso PB |
| Switch acceso primera planta | Cisco 2960-24TT | 1 | Acceso PA |
| PCs departamentos | PC-PT | 10 | Usuarios finales |
| Servidores | Server-PT | 2 | DHCP/DNS + Web |
| Punto de acceso WiFi | AccessPoint-PT | 1 | Red inalámbrica |
| Laptops WiFi | Laptop-PT | 2 | Pruebas WiFi |

---

## ✅ Pruebas realizadas

### Conectividad dentro de la misma VLAN ✔️
```
PC-Admin1> ping 192.168.10.101     → !!! Éxito
PC-Dir1>   ping 192.168.20.101     → !!! Éxito
PC-Dev1>   ping 192.168.30.101     → !!! Éxito
```

### Conectividad inter-VLAN permitida ✔️
```
PC-Admin1> ping 192.168.60.10      → !!! Éxito  (Admin → Servidores)
PC-Dir1>   ping 192.168.10.100     → !!! Éxito  (Dir → Admin)
```

### Bloqueos por ACL ✔️
```
PC-Form1>  ping 192.168.20.100     → ... Fallo  (Formación ✗ Dirección)
PC-Dev1>   ping 192.168.10.100     → ... Fallo  (Desarrollo ✗ Admin)
PC-Admin1> ping 192.168.99.3       → ... Fallo  (No puede administrar switches)
```

### DHCP funcional ✔️
```
PC-Admin1>  ipconfig  →  IP: 192.168.10.100 / GW: 192.168.10.1
PC-Dev1>    ipconfig  →  IP: 192.168.30.100 / GW: 192.168.30.1
```

---

## 🧠 Decisiones técnicas

**Inter-VLAN routing mediante Switch L3:** se eligió un switch multicapa (3560) en la capa de distribución para realizar el enrutamiento entre VLANs en hardware, lo que ofrece mayor rendimiento que hacerlo en el router. El router se mantiene para la gestión del DHCP, las ACLs de seguridad y la futura conexión WAN.

**ACLs extendidas en el router:** permiten filtrar tráfico por IP de origen y destino, dando control granular sobre qué VLANs pueden comunicarse entre sí.

**Segmentación VLAN 99 para gestión:** todos los dispositivos de red (switches y AP) tienen su IP de administración en una VLAN dedicada, inaccesible desde las VLANs de usuario.

---

## 📄 Documentación

- [`documentacion.pdf`]([redes/documentacion.pdf](https://github.com/ygorlramos-cpu/Intermodular-redes/blob/271f945a50346c55bb5053ece047a7b381991a81/INTERMODULAR%20REDES.docx.pdf) — Esquema lógico y físico, tabla de direccionamiento, configuraciones, pruebas y conclusiones
- [`guia_paso_a_paso.pdf`](redes/guia_paso_a_paso.pdf) — Guía detallada de implementación en Cisco Packet Tracer

---

*Proyecto de Planificación y Administración de Redes — Curso 2025–2026*
