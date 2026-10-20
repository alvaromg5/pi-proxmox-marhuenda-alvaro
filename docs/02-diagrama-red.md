# 02-diagrama-red

## Objetivo
Diseñar la arquitectura de red lógica y física para el nodo Proxmox y el laboratorio del servicio ASO. El propósito principal es garantizar la conectividad para la gestión del hipervisor.

## Procedimiento


## Comandos usados

```bash

```

## Evidencias
graph TD
    %% Definición de estilos
    classDef external fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef proxmox fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    classDef vms fill:#e8f5e9,stroke:#388e3c,stroke-width:2px;

    Internet((Red del Centro / Internet)):::external

    subgraph Nodo ["Nodo Proxmox (pve-marhuenda)"]
        NIC["NIC Física: enp2s0"]:::proxmox
        
        subgraph Gestión
            vmbr0["Bridge Gestión: vmbr0<br>IP: 172.30.4.49/26"]:::proxmox
        end

        subgraph Laboratorio ["Red Interna Aislada (NAT)"]
            vmbr1["Bridge Laboratorio: vmbr1<br>IP: 10.0.0.1/24"]:::proxmox
            
            srv-aso["srv-aso<br>IP: 10.0.0.10/24"]:::vms
            cli-linux["cli-linux<br>IP: 10.0.0.20/24"]:::vms
        end
    end

    %% Conexiones
    Internet <-->|Cable físico| NIC
    NIC <--> vmbr0
    vmbr0 -. "NAT / Enrutamiento (iptables)" .-> vmbr1
    vmbr1 <--> srv-aso
    vmbr1 <--> cli-linux
## Incidencias

## Conclusion

[Volver al indice](../README.md)