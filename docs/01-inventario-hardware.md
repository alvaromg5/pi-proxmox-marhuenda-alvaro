# Inventario investigado y viabilidad

Álvaro Marhuenda Girona 2ºASIR-X

## Contexto de la observacion

- Fecha y sistema utilizado: 13/10/2026, Debian Live (Debian 13, kernel 6.12.94) arrancado desde USB con Ventoy. `systemd-detect-virt` devuelve `none`: se observa el equipo físico y no una VM (evidencia: `systemd.txt`). Primera revisión de especificaciones el 07/10/2026.
- Modalidad, equipo anfitrion y VM VirtualBox si procede: Física, equipo real. VirtualBox: No procede.
- Version de Proxmox prevista: PENDIENTE | Instalaremos la versión 9.2 (confirmar al descargar la ISO).|

## Inventario inicial

| Área | Dato observado y evidencia | Especificación y fuente | Límite o diferencia | Justificación |
|---|---|---|---|---|
| CPU | Intel Pentium G4560 a 3,50 GHz, x86_64 (64 bits), 1 socket, 2 núcleos con 2 hilos cada uno (4 CPU lógicas). `lscpu` indica `Virtualization: VT-x` y el flag `vmx` aparece en la lista. Evidencia: `lscpu.txt` | Proxmox: Intel 64 o AMD64 con el flag Intel VT o AMD-V (web de requisitos). Intel ARK del G4560: 2 núcleos, 4 hilos, VT-x: Sí, VT-d: Sí, socket FCLGA1151, memoria DDR4-2133/2400 y hasta 2 canales | Cumple los requisitos pedidos. Que el flag `vmx` sea visible indica que VT-x está habilitado en la BIOS (lo normal es que el kernel no lo muestre si la BIOS lo desactiva); se confirmará al arrancar KVM en la instalación. Solo hay 2 núcleos físicos: los 4 hilos no equivalen a 4 núcleos | En la web de Proxmox no se indican mínimos de núcleos. En nuestro caso, al tener solo dos núcleos físicos (4 hilos), habrá limitaciones claras para virtualizar, ya que host e invitados comparten esa capacidad de procesamiento. |
| RAM | 8 GB DDR4 formados por 2 módulos CRUCIAL CT4G4DFS824A de 4 GB a 2400 MT/s, uno en `ChannelA-DIMM1` y otro en `ChannelB-DIMM1` (dual channel, sin ECC). El sistema ve 7,6 GiB (`free -h`); la diferencia la reservan el firmware y, probablemente, la gráfica integrada. Swap: 0 B (en el Live). Evidencia: `memory.txt`, `free.txt` | Proxmox recomienda como mínimo 2 GB de RAM para el sistema y sus servicios, más la memoria de los invitados. Mínimo para evaluación: 1 GB. Intel ARK: la CPU admite DDR4-2133/2400, 2 canales y hasta 64 GB | Cumple el mínimo de evaluación y los 2 GB recomendados para el sistema. Descontados esos 2 GB, quedan unos 5,6 GiB para invitados y margen. Con dos módulos se aprovechan los dos canales de la CPU. La placa es mini-ITX con 2 ranuras DIMM DDR4 (hasta 2400 MHz y 32 GB en total, según la ficha de un distribuidor) y ambas están ocupadas (el SMBIOS declara 4 posiciones, pero solo 2 son ranuras físicas), por lo que ampliar la RAM exigiría sustituir los módulos, por ejemplo por 2 × 8 GB | La RAM no es nuestro principal cuello de botella, pero sí limita la memoria disponible para los invitados. Los 2,4 GiB «usados» del Live no representan a Proxmox (el sistema Live y su overlay viven en RAM). |
| Almacenamiento | Disco duro mecánico (HDD) TOSHIBA DT01ACA050: 500 GB (465,8 GiB), 3,5 pulgadas, 7200 rpm, SATA 3.0 negociado a 6,0 Gb/s, sectores de 512 B lógicos y 4096 B físicos; `ROTA=1`. **No es un SSD.** SMART: PASSED, 0 sectores reasignados, 0 pendientes, 5444 horas encendido (≈227 días) y 35 ºC. Una única partición (`sda1`, 458 GiB de sistema de archivos) con 317 GiB usados (73 %): contiene datos. Evidencia: `lsblk.txt`, `smartctl.txt`, `df.txt` | Proxmox recomienda almacenamiento rápido y redundante, con mejores resultados en SSD; mínimo de evaluación: un disco duro. Fabricante (Toshiba, serie DT01ACA): 500 GB, 7200 rpm, SATA 6 Gbit/s, 3,5 pulgadas | Cumple el mínimo (es un disco duro) y sobra capacidad. No cumple lo recomendado: ni es SSD ni hay redundancia | Hay espacio de sobra y el disco está sano ahora mismo, pero un disco mecánico es mucho más lento que un SSD en accesos aleatorios (arranques, clonados y copias) y, al ser único, si falla se pierden Proxmox, las VMs, los datos y las copias locales. Instalar Proxmox borra el disco entero, incluidos los 317 GiB actuales. |
| Red | Controlador Ethernet Realtek RTL8111/8168/8211/8411 (RTL8111H integrado en la placa, ID `10ec:8168`), interfaz `enp2s0` en estado UP, driver en uso `r8169`. El driver informa de `Link is Up - 1Gbps/Full`: 1 Gbit/s en dúplex completo. Evidencia: `ipbr.txt`, `lspci.txt`, `velocidad.txt` | Mínimo para evaluación: una NIC. Recomendado: NIC Gbit redundantes (web de requisitos de Proxmox) | Cumple el mínimo (una NIC) y es Gigabit. No cumple la recomendación de redundancia. El driver `r8169` es del propio kernel de Linux y funciona en Debian Live; se confirmará que Proxmox reconoce la tarjeta durante la instalación | Un solo adaptador: la gestión de Proxmox y el tráfico del servicio comparten la misma interfaz. |
| Plataforma | Placa ASUS H110I-PLUS (`lspci.txt`). Firmware AMI versión 0810, fechado el 09/08/2017, con «UEFI is supported» (`bios.txt`); el sistema arrancó en UEFI (`efivarfs` montado en `df.txt`). VT-x visible en el sistema (`lscpu.txt`) | Proxmox exige CPU y placa con VT-x para KVM (web de requisitos). Documentación del fabricante de la placa: ver Fuentes técnicas | «Soportado» (lo dice Intel) no es lo mismo que «activado» (lo dice la BIOS): aquí está soportado y, por lo observado, activado. La BIOS es antigua (2017) y `lscpu.txt` marca varias entradas como «Vulnerable: ... no microcode» (MDS, SRBDS, MMIO stale data): se tratará en la fase de seguridad (microcódigo y BIOS) | Sin VT-x activado no habría virtualización completa con KVM, solo contenedores LXC. |

---

## 1. Puedo instalar Proxmox en este equipo?

Contrastar el inventario con requisitos oficiales de la version prevista.
Responder por separado y justificar con datos y fuentes:

## | Según la web de proxmox: https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements | ##

### |Recommended Hardware| ###
- Intel 64 or AMD64 with Intel VT or AMD-V CPU flag. Alternatively, 64-bit Arm (Armv9-A or newer processor).

- Memory, minimum 2 GB for OS and Proxmox VE services. Plus designated memory for guests. For Ceph or ZFS additional memory is required, approximately 1 GB memory for every TB used storage.

- Fast and redundant storage, best results with SSD disks.

- OS storage: Hardware RAID with batteries protected write cache (“BBU”) or non-RAID with ZFS and SSD cache.

- VM storage: For local storage use a hardware RAID with battery backed write cache (BBU) or non-RAID for ZFS. Neither ZFS nor Ceph are compatible with a hardware RAID controller. Shared and distributed storage is also possible.

- Redundant Gbit NICs, additional NICs depending on the preferred storage technology and cluster setup – 10 Gbit and higher is also supported.

- For PCI(e) passthrough a CPU with VT-d or AMD-Vi CPU flag is required.

### |Minimum Hardware| ###
- CPU: 64bit (Intel 64 or AMD64). Alternatively, 64-bit Arm (Armv9-A or newer processor).

- For x86 hosts: Intel VT or AMD-V capable CPU and motherboard (required for x86 KVM full virtualization support).

- Minimum 1 GB RAM

- Hard drive

- One NIC
----------------------------------------------------------------------------------------------------------
- **Puedo instalar y arrancar Proxmox:** Sí. El equipo supera los requisitos mínimos oficiales (CPU de 64 bits con soporte VT-x, más de 1 GB de RAM, un disco duro y una tarjeta de red). Aunque no cumple las recomendaciones ideales para producción (carece de SSD, redundancia y más núcleos), es válido para instalar y arrancar el entorno base.

- **Puedo ejecutar el servidor ASO y el cliente del proyecto:** Sí, pero con el rendimiento muy ajustado. La RAM restante (aprox. 5,6 GiB) obliga a limitar drásticamente la memoria asignada al cliente y servidor. La CPU de 2 núcleos compartirá la carga, pero el disco HDD mecánico será el mayor cuello de botella (alto *I/O wait*), ralentizando el sistema al ejecutar dos máquinas virtuales simultáneamente.

- **Condiciones o comprobaciones pendientes:** 

| Comprobación / Condición | Acción a realizar | Justificación técnica |
|---|---|---|
| **Copia de seguridad** | Hacer un backup externo de los 317 GiB actuales. | El instalador de Proxmox formatea y borra todo el contenido del disco `sda`. |
| **Sistema de archivos** | Elegir `ext4` o `xfs` (LVM) en el instalador. | **Evitar ZFS**. ZFS consume mucha RAM y rinde muy mal sobre un único disco mecánico. |
| **Microcódigo de CPU** | Instalar el paquete `intel-microcode` post-instalación. | La BIOS es de 2017 y la CPU reporta vulnerabilidades sin parchear (MDS, SRBDS). |
| **Verificación KVM y Red** | Comprobar el bridge `vmbr0` y el módulo KVM. | Confirmar que Proxmox levanta la interfaz `enp2s0` a 1Gbps y activa la virtualización por hardware sin fallos. |

## 2. Que limitaciones tendre con este hardware?

Analizando la RAM (7,6 GiB libres para el sistema), CPU (2 núcleos, 4 hilos), almacenamiento (HDD de 500 GB, 7200 rpm) y red (1 Gbps en dúplex completo), el equipo soporta el proyecto pero con restricciones. Podré mantener simultáneamente el servidor Proxmox, un servidor ASO y un cliente ligero, pero no podré desplegar laboratorios complejos con múltiples VMs sin saturar la memoria o el disco. 

### Reparto de recursos
*Cálculos basados en: 7,6 GiB de RAM útil y 465 GiB de disco físico disponible tras formateo.*

| Elemento | CPU/vCPU | RAM prevista | Disco previsto | Justificación |
| :--- | :--- | :--- | :--- | :--- |
| **Proxmox y sus servicios** | Compartida | 2 GiB | 32 GiB | Reserva mínima recomendada por Proxmox para estabilidad del hipervisor. |
| **srv-aso** | 2 vCPU | 2.5 GiB | 40 GiB | Servidor principal. Requiere memoria suficiente para evitar Swap. |
| **cli-linux** | 1 vCPU | 1.5 GiB | 20 GiB | Cliente ligero (CLI o entorno mínimo) para verificar servicios. |
| **Plantillas y copias locales** | No aplica | No aplica | 60 GiB | Reserva para ISOs de instalación, plantillas y *backups* locales. |
| **Margen de operación** | *Overcommit* | 1.6 GiB | 313 GiB | Quedan 1,6 GiB libres en el host. Sobra amplio espacio en disco. |

### Análisis de limitaciones

| Limitación o dato pendiente | Evidencia o estimación | Consecuencia para el proyecto | Medida y límite que permanece |
| :--- | :--- | :--- | :--- |
| **I/O Wait (Almacenamiento)** | Disco HDD a 7200 rpm en lugar de SSD. | Las lecturas/escrituras simultáneas ralentizarán drásticamente el sistema. | **Medida:** No usar ZFS. Escalonar arranques. **Límite:** La latencia física no mejora. |
| **RAM muy ajustada** | 7,6 GiB de RAM utilizable. | Imposibilidad de levantar más de 2 o 3 VMs sin usar Swap, colapsando el nodo. | **Medida:** Usar contenedores LXC cuando sea posible. **Límite:** Concurrencia estancada en 2-3 instancias. |
| **Sin redundancia** | Un solo disco y una interfaz de red (SPOF). | Si falla el disco, se pierde la práctica. Si la red se satura, Proxmox no responde. | **Medida:** Externalizar *backups*. **Límite:** Sigue habiendo puntos únicos de fallo. |

----------------------------------------------------------------------------

## 3. Que componentes son mas criticos en mi servidor y por que?

| Prioridad y componente | Función en mi servidor | Efecto si es insuficiente | Efecto si falla | Evidencia y justificación |
| :--- | :--- | :--- | :--- | :--- |
| **1. Almacenamiento (HDD 500GB)** | Persistencia de datos, SO y *backups*. | Cuello de botella severo. *I/O wait* alto causando *timeouts* en servicios. | Pérdida total de integridad y disponibilidad. Se pierde todo. | Al ser un HDD mecánico y único, es el eslabón más débil en virtualización. |
| **2. Memoria RAM (8 GB)** | Espacio volátil para el hipervisor y VMs. | Paginación en disco (Swap), congelando el nodo. | Caída abrupta y pérdida de disponibilidad de las máquinas. | Con 7,6 GiB reales el margen es mínimo. |
| **3. Tarjeta de red (NIC 1 Gbps)** | Tráfico de gestión y servicios. | Alta latencia en conexión a servicios ASO. | Aislamiento total del nodo. Pérdida de servicio. | Carece de redundancia para alta disponibilidad. |

**Componente que mejoraría primero:**
El disco duro, sustituyéndolo por un SSD. En entornos de virtualización, el disco mecánico es el mayor cuello de botella. Un SSD reduce muchísimo el tiempo de espera (I/O Wait) y aumenta en grandes medidas la velocidad, permitiendo que la CPU y RAM actuales operen fluidamente sin esperar constantemente a operaciones de lectura/escritura.

------------------------------------------------------------

## Fuentes tecnicas

| Fuente y URL | Versión/modelo y fecha | Afirmación respaldada | Aplicación al nodo |
| :--- | :--- | :--- | :--- |
| **Requisitos Proxmox VE** (https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements) | Proxmox VE 8.x/9.x (13/10/2026) | Requiere CPU 64 bits con VT-x, mín. 1GB RAM, disco y NIC. | Confirma que el equipo supera el mínimo exigido para evaluar el hipervisor. |
| **Intel ARK** (https://www.intel.com/content/www/us/en/products/sku/97143/intel-pentium-processor-g4560-3m-cache-3-50-ghz/specifications.html) | Intel Pentium G4560 (13/10/2026) | Procesador de 2 núcleos, 4 hilos, soporta Intel VT-x. | Para validar la capacidad real de procesamiento (vCPUs) para repartir cargas. |
| **Salidas de los comandos del Linux** (`lscpu`, `free`, `ip`, `smartctl`...) | Debian Live (13/10/2026) | Red a 1 Gbps/Full, RAM 7,6 GiB, HDD TOSHIBA sano (SMART PASSED). | [evidencias](../evidencias/Sesion02/capturas-o-salidas/)

-----------------------------------------------------------------------

## Dictamen de viabilidad

**Apto con condiciones.**

El equipo físico es apto para actuar como nodo Proxmox y realizar la Práctica del Proyecto Intermodular. Tiene virtualización por hardware (VT-x), memoria para las tres máquinas base exigidas y red Gigabit. 

Condicionado a:
1. **Evitar ZFS** y usar volúmenes lógicos estándar (`ext4`/LVM) por culpa del disco mecánico.
2. **Control estricto de RAM**, sin exceder el uso planificado para evitar colapsos.
3. **Backup previo obligatorio**, porque al formatear el disco se borrarán los 317 GiB actuales.

---------------------------------------------------------------------------------------------

## Evidencias y revision docente

***ZFS*** --> Sistema de archivos y gestor de volúmenes lógicos muy avanzado, diseñado específicamente para servidores de almacenamiento masivo y entornos de virtualización

***I/O Wait*** --> I/O Wait es el porcentaje de tiempo que el procesador se queda inactivo o bloqueado porque está esperando a que un dispositivo de almacenamiento termine de leer o escribir datos.

- [bios](../evidencias/Sesion02/capturas-o-salidas/bios.txt)
- [df](../evidencias/Sesion02/capturas-o-salidas/df.txt)
- [Mem_free](../evidencias/Sesion02/capturas-o-salidas/free.txt)
- [ip addr enp2s0](../evidencias/Sesion02/capturas-o-salidas/ipbr.txt)
- [lsblk](../evidencias/Sesion02/capturas-o-salidas/lsblk.txt)
- [lscpu](../evidencias/Sesion02/capturas-o-salidas/lscpu.txt)
- [lspci](../evidencias/Sesion02/capturas-o-salidas/lspci.txt)
- [memory](../evidencias/Sesion02/capturas-o-salidas/memory.txt)
- [smartctl](../evidencias/Sesion02/capturas-o-salidas/smartctl.txt)
- [systemd-detect-virt](../evidencias/Sesion02/capturas-o-salidas/systemd.txt)
- [velocidad de red (dmesg)](../evidencias/Sesion02/capturas-o-salidas/velocidad.txt)

------------------------------------------------------------------------------

[Volver al indice](../README.md)