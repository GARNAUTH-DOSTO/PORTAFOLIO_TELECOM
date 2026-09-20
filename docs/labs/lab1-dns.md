---
title: Configuración de STP en 3 switches
description: Rapid PVST+ en una topología triangular de 3 switches, con root bridge primario y secundario.
---

# Lab: Configuración de STP en 3 switches

> **Fecha:** 2026-09-20 | **Herramienta:** Packet Tracer | **Tecnologías:** Rapid PVST+, trunks, VLAN 10 | **Nivel:** CCNA

## Descripción

<!-- 2-3 líneas: qué se practica y qué se aprende. -->
Se conectan 3 switches en triángulo, lo que crea un loop físico. Se configura Rapid PVST+ para que STP lo bloquee, y se define manualmente SW1 como root bridge y SW2 como respaldo.

**Contenido**

[TOC]

## Objetivos

- Identificar cómo STP elige el root bridge, los root ports y los designated ports.
- Forzar el root bridge con `spanning-tree vlan X root primary`.
- Verificar qué puerto queda en estado `BLK` (alternate) y por qué.

## Topología

<figure markdown="span">
  ![Topología del laboratorio STP](../assets/img/lab-stp/topologia.png){ width="600" loading=lazy }
  <figcaption>Figura 1. Topología triangular con 3 switches.</figcaption>
</figure>

| Dispositivo | Modelo | Interfaz | Conecta a | Función |
|-------------|--------|----------|-----------|---------|
| SW1 | Catalyst 2960 | Gi0/1 | SW2 Gi0/1 | Root bridge (primary) |
| SW1 | Catalyst 2960 | Gi0/2 | SW3 Gi0/1 | |
| SW2 | Catalyst 2960 | Gi0/2 | SW3 Gi0/2 | Root bridge (secondary) |
| SW3 | Catalyst 2960 | Gi0/1, Gi0/2 | SW1, SW2 | Switch de acceso |

## Configuración

### Paso 1: VLAN y trunks

=== "SW1"

    ```text title="SW1"
    enable
    configure terminal
    hostname SW1
    vlan 10
     name USERS
    exit
    interface range gigabitEthernet 0/1 - 2
     switchport mode trunk
    end
    ```

=== "SW2"

    ```text title="SW2"
    enable
    configure terminal
    hostname SW2
    vlan 10
     name USERS
    exit
    interface range gigabitEthernet 0/1 - 2
     switchport mode trunk
    end
    ```

=== "SW3"

    ```text title="SW3"
    enable
    configure terminal
    hostname SW3
    vlan 10
     name USERS
    exit
    interface range gigabitEthernet 0/1 - 2
     switchport mode trunk
    end
    ```

### Paso 2: STP y root bridge

=== "SW1"

    ```text title="SW1"
    configure terminal
    spanning-tree mode rapid-pvst
    spanning-tree vlan 1,10 root primary
    end
    ```

=== "SW2"

    ```text title="SW2"
    configure terminal
    spanning-tree mode rapid-pvst
    spanning-tree vlan 1,10 root secondary
    end
    ```

=== "SW3"

    ```text title="SW3"
    configure terminal
    spanning-tree mode rapid-pvst
    end
    ```

!!! tip "¿Por qué `root primary` y no una prioridad fija?"
    `root primary` calcula la prioridad según la del root actual (24576 o menos), así que sigue funcionando aunque otro switch tenga una prioridad baja. Con `priority 4096` la asignas a mano.

## Verificación

```text title="SW3"
SW3# show spanning-tree vlan 10
```

```text title="Salida de ejemplo (reemplazar por la real)"
VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    24586
             Address     0001.42AA.1111
             Cost        4
             Port        25(GigabitEthernet0/1)

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- ----
Gi0/1            Root FWD 4         128.25   P2p
Gi0/2            Altn BLK 4         128.26   P2p
```

<figure markdown="span">
  ![Salida de show spanning-tree en SW3](../assets/img/lab-stp/show-stp-sw3.png){ width="600" loading=lazy }
  <figcaption>Figura 2. Captura de `show spanning-tree vlan 10` en SW3.</figcaption>
</figure>

!!! note "Otros comandos útiles"
    `show spanning-tree summary`, `show spanning-tree root`, `show interfaces trunk`
    
[Descargar .pkt](../assets/files/lab-stp/lab-stp.pkt){ .md-button .md-button--primary download }
[Descargar configs](../assets/files/lab-stp/configs.txt){ .md-button download }

## Conclusión

<!-- Qué se logró, qué se observó, qué se aprendió. -->
- SW1 fue elegido root bridge y todos sus puertos quedaron como designated.
- El loop se rompió en SW3 Gi0/2 (Alternate/BLK): en el segmento SW2–SW3 ambos tienen el mismo costo hacia el root, y gana el de menor Bridge ID (SW2).
- Fijar el root bridge manualmente evita que un switch nuevo con menor MAC lo reemplace.

**Siguientes pasos:** PortFast + BPDU Guard en puertos de acceso, y balanceo por VLAN con distintos root bridges.
