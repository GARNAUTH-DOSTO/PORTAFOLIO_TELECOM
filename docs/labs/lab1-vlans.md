# 🔄 Lab 1: Configuración de VLANs

## 🎯 Objetivo

Configurar VLANs 10 (VENTAS), 20 (SISTEMAS) y 30 (GERENCIA) en un switch Cisco Catalyst 2960, implementando trunking 802.1Q.

---

## 🖥️ Topología

<div class="lab-card">
**Equipos utilizados:**
- 1x Switch Cisco Catalyst 2960
- 3x PCs
- Cables Ethernet

**VLANs a configurar:**
- VLAN 10: VENTAS - 192.168.10.0/24
- VLAN 20: SISTEMAS - 192.168.20.0/24
- VLAN 30: GERENCIA - 192.168.30.0/24
</div>

---

## ️ Configuración Paso a Paso

### Paso 1: Acceder al switch

```bash
Switch> enable
Switch# configure terminal
Switch(config)#
