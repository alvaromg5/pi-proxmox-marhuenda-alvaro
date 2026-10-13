# Ficha inicial

    | Campo | 
       | Contenido |
        |---|---|
# | Identificador del alumno
    | Álvaro Marhuenda |
# | URL GitHub
    | https://github.com/USUARIO/pi-proxmox-apellido-nombre |
# | Acceso docente comprobado
    | PENDIENTE |
# | Modalidad fisica o virtualizada
    | PENDIENTE |
# | Recursos conocidos
    | Recursos conocidos | Equipo físico: Intel Pentium G4560 (2 núcleos / 4 hilos, VT-x), 8 GB DDR4 a 2400 MT/s (2 × 4 GB en dual channel, 7,6 GiB visibles), HDD TOSHIBA DT01ACA050 de 500 GB (465,8 GiB, 7200 rpm, SATA), red Realtek RTL8111H de 1 Gbit/s y placa ASUS H110I-PLUS (BIOS AMI 0810, UEFI). Sin VirtualBox. Detalle y evidencias en el [evidencias](../evidencias/Sesion02/capturas-o-salidas/) |
# | Nombre del nodo 
    | pve-marhuenda |
# | Servicio ASO previsto
    | PENDIENTE | (a elegir entre Samba, NFS, SSH, LDAP, CUPS o Nginx administrado) |
# | Red de laboratorio
    | PENDIENTE | Subred observada con DHCP en el equipo: 172.30.4.0/26 (`ip -br addr`). Gateway, DNS y direcciones fijas del nodo y de los invitados: PENDIENTE, se definirán en el [diagrama de red](02-diagrama-red.md). VLAN: no procede |

# | Riesgos y dudas iniciales |
- Configuración óptima de redes en Proxmox.
- Requisitos mínimos para el nodo.
- Flujo de automatización con Ansible en fases posteriores.

 | Solo 2 núcleos físicos que compartirán host e invitados. 8 GB de RAM (7,6 GiB visibles): el reparto deja unos 1,6 GiB libres. Disco duro mecánico, único y sin redundancia: clonados y copias lentos y copias locales que no protegen ante su fallo. El disco contiene 317 GiB de datos actuales que Proxmox borrará al instalar. Una sola tarjeta de red. BIOS de 2017. El instalador de Proxmox debe reconocer la tarjeta de red (driver `r8169`) |


[Volver al indice](../README.md)