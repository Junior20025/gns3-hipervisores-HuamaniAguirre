# GNS3 + Hipervisores en Windows 11

![Estado](https://img.shields.io/badge/Estado-En_Proceso-yellow)
![Plataforma](https://img.shields.io/badge/Plataforma-GNS3%20%2B%20Windows11-blue)

---

## Introducción
Este repositorio presenta la investigación sobre la implementación de laboratorios de red utilizando GNS3 en Windows 11, integrando hipervisores tipo 1 (ESXi) y tipo 2 (VirtualBox).

---

# 1. Arquitectura de Virtualización en Windows 11

## Aislamiento de Núcleo y VBS
El **Aislamiento de Núcleo (Core Isolation)** y la **Virtualization-Based Security (VBS)** son mecanismos de seguridad que utilizan virtualización para proteger el sistema.

🔴 Problema:
- Activan Hyper-V de forma interna
- Bloquean VirtualBox y GNS3

✅ Solución:
- Desactivar "Aislamiento de núcleo" en Seguridad de Windows

---

# 2. GNS3 VM: El Motor de Simulación

## KVM (Kernel-based Virtual Machine)
KVM es una tecnología de virtualización que permite a GNS3 ejecutar dispositivos con alto rendimiento usando el kernel de Linux.

🔴 Importante:
Debe aparecer como:
KVM support: True

Si aparece en False:
- No hay virtualización anidada
- El rendimiento será bajo

---

## Configuración de Recursos

Para evitar que Windows 11 se vuelva lento, se recomienda:

- Si tienes 8 GB RAM → asignar 4 GB a GNS3
- Usar entre 2 a 4 núcleos de CPU

Esto permite estabilidad entre el sistema host y la máquina virtual.

---

# 3. Integración con VirtualBox (Local)

## Configuración de Red (Host-Only)

Pasos:
1. Ir a VirtualBox
2. Crear un adaptador Host-Only
3. Asignar una IP (ejemplo: 192.168.56.1)
4. Conectar la GNS3 VM a esa red

Esto permite la comunicación entre GNS3 y el sistema.

---

## Modo Promiscuo

El modo promiscuo permite que una máquina virtual capture todo el tráfico de red.

Es necesario porque:
- GNS3 trabaja con switches virtuales
- Se necesita tráfico de capa 2

Configuración:
Modo Promiscuo → Permitir todo

---

# 4. Integración con VMware ESXi (Remoto)

## Arquitectura Cliente-Servidor

En este modelo:

Laptop (GNS3) → Servidor ESXi → Máquinas virtuales → Red

Esto permite que el procesamiento se haga en un servidor físico, mejorando el rendimiento.

---

## Seguridad en vSwitch

Para que GNS3 funcione correctamente en ESXi se deben configurar:

- Promiscuous Mode → Accept
- MAC Address Changes → Accept
- Forged Transmits → Accept

Estas opciones permiten el tráfico dinámico dentro de redes virtuales.

---

# 5. Troubleshooting

| Error | Causa Técnica | Solución |
|------|-------------|----------|
| KVM support False | No hay virtualización anidada | Activar VT-x en BIOS |
| Sin conectividad | Modo promiscuo desactivado | Activar "Permitir todo" |
| Error puerto 3080 | Firewall bloqueando | Abrir puertos en Windows |

---

## Comando útil

```bash

VBoxManage modifyvm "GNS3 VM" --nested-hw-virt on

