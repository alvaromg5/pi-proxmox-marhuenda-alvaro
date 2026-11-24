# Bitacora de sesiones

La bitacora es el registro cronologico del trabajo. Anade una entrada al final
de cada sesion y conserva las anteriores. Escribe datos comprobables y enlaza
las evidencias utiles; evita frases generales como "he trabajado en Proxmox".

## Sesion 01 - 22/09/2026

- Objetivo: Iniciar GitHub y la documentacion Markdown.
- Acciones realizadas: Creación estructura con el script y primer commit: configuraciones, cambios o investigacion.
- Decisiones y motivos: Se ha utilizado el script proporcionado para crear la estructura inicial del repositorio y organizar la documentación del proyecto mediante archivos Markdown. La modalidad de instalación se decidirá después de realizar el inventario del hardware disponible.
- Pruebas y resultados:
    - **Resultado esperado:** Tener el repositorio creado con la estructura y documentación inicial.
    - **Método:** Ejecución del script, edición del README y de la ficha inicial, y realización del primer commit.
    - **Resultado obtenido:** Repositorio creado correctamente y acceso del profesor comprobado.
- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [evidencias](../evidencias/Sesion01/capturas-o-salidas)
- Incidencias y solucion o estado pendiente: No se han producido incidencias destacables. Queda pendiente realizar el inventario del hardware, decidir la modalidad de instalación y elegir el servicio ASO.
- Siguiente paso: Preparar comandos para recopilar la información.

## Sesion 02 - 13/10/2026

- Objetivo: Investigar el hardware disponible, comprobar la viabilidad de la instalación de Proxmox y diseñar la arquitectura de red del laboratorio.

- Acciones realizadas: Investigación del equipo y recopilación de sus especificaciones mediante comandos y desde la BIOS. Realización del inventario de hardware, asignación inicial de recursos, planificación de las direcciones IP, identificación de los puertos necesarios y creación del diagrama de red.

- Decisiones y motivos: Se ha trabajado con el equipo físico para conocer sus especificaciones y comprobar si permite virtualizar. Se ha previsto utilizar ext4 sobre LVM en vez de ZFS debido a la memoria RAM disponible y a que solo se dispone de un disco. Inicialmente se ha planteado asignar 1 vCPU a cada invitado y utilizar LXC para el servidor por su menor consumo de recursos. Para el diseño de red se ha decidido utilizar una red Host-only para la comunicación interna y la administración de Proxmox, y una red NAT para disponer de salida a Internet.

- Pruebas y resultados: Se ha comprobado mediante la BIOS y comandos de terminal que el procesador soporta la virtualización VT-x. Se han obtenido las especificaciones del equipo y guardado las salidas de los comandos en archivos txt. Se ha diseñado el esquema de red con las direcciones IP de cada máquina y los puertos necesarios. La conectividad se comprobará después de la instalación de Proxmox.

- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [01-inventario-hardware](./01-inventario-hardware.md). [02-diagrama-red](./02-diagrama-red.md). [Evidencias](../evidencias/Sesion02/capturas-o-salidas).

- Incidencias y solución o estado pendiente:
    - No teníamos permisos de administrador ni de root en los equipos, por lo que se utilizó un USB booteable con Debian Live para poder ejecutar los comandos necesarios.
    - El primer borrador incluía datos sin evidencias de comandos, como el tipo de disco y el número de módulos de RAM. Estos datos se comprobaron y corrigieron el 13/10/2026.
    - Al final no se puede usar los equipos que se habían traído al principio, por lo que se usará VirtualBox en el equipo de clase.

- Siguiente paso: Crear la máquina virtual en VirtualBox, asignarle los recursos establecidos, configurar los adaptadores de red e instalar Proxmox.

## Sesion 03 - 20/10/2026

- Objetivo: Realizar el diagrama de red.

- Acciones realizadas: Diseño de red, y documentación.

- Decisiones y motivos: e ha decidido utilizar dos adaptadores de red en VirtualBox en vez de utilizar únicamente uno. La red Host-only permitirá la comunicación interna del laboratorio y el acceso al panel de Proxmox desde el equipo anfitrión, mientras que la red NAT permitirá disponer de salida a Internet. Se han establecido direcciones IP fijas para facilitar la identificación y administración de las máquinas.

- Pruebas y resultados:
    - **Resultado esperado:** Obtener un diseño de red que permita la comunicación entre las máquinas del laboratorio, la administración del nodo y la salida a Internet.
    - **Método:** Planificación de las redes, asignación de direcciones IP, identificación de los puertos necesarios y creación del diagrama de red mediante Mermaid.
    - **Resultado obtenido:** Diseño de red realizado y documentado correctamente. Queda pendiente comprobar su funcionamiento real después de la instalación de Proxmox.

- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [02-diagrama-red](./02-diagrama-red.md). [evidencias](../evidencias/Sesion03/capturas-o-salidas)

- Incidencias y solucion o estado pendiente: Sin incidencias.

- Siguiente paso: Instalación y configuración proxmox y hypervisor.

## Sesion 04 - 27/10/2026

- Objetivo: Instalación de Proxmox y configuración de la máquina virtual.

- Acciones realizadas: Creación de la máquina virtual, asignación de recursos, configuración de los adaptadores de red, activación de la virtualización anidada, instalación de Proxmox y documentación del procedimiento.

- Decisiones y motivos: Se ha decidido utilizar VirtualBox para instalar Proxmox en una máquina virtual sin modificar el sistema anfitrión. Se han asignado 4 vCPU, 8 GiB de RAM y 64 GiB de disco dinámico. Se han utilizado dos adaptadores de red, uno NAT para disponer de salida a Internet y otro solo-anfitrión para acceder al panel de Proxmox desde el equipo anfitrión.

- Pruebas y resultados: Se ha comprobado que Proxmox se ha instalado correctamente y que se puede acceder a su panel web desde el navegador del equipo anfitrión mediante la IP de gestión. También se ha comprobado que aparecen las interfaces de red y los almacenamientos configurados.

- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [03-instalacion-proxmox](./03-instalacion-proxmox.md). [Evidencias](../evidencias/Sesion04_05/capturas-o-salidas).

- Incidencias y solución o estado pendiente: Se han producido problemas utilizando Windows como sistema anfitrión. Se valorará rehacer la máquina virtual en el ordenador con Linux de clase si estos problemas continúan.

- Siguiente paso: Revisar la configuración base del nodo Proxmox, comprobar el almacenamiento, las interfaces de red, los usuarios y permisos, y ajustar el reparto de recursos de las futuras máquinas virtuales.

## Sesion 05 - 17/11/2026 - 24/11/2026

- Objetivo: Comprobaciones y revisiones de la configuración base del nodo Proxmox.

- Acciones realizadas: Revisión del almacenamiento, interfaces de red, usuarios, roles y permisos mediante comandos y desde el panel de Proxmox. Creación de un usuario con acceso limitado y actualización del reparto de recursos.

- Decisiones y motivos: Se ha mantenido local para almacenar las ISO, plantillas y copias de seguridad, y local-lvm para los discos de las máquinas virtuales. Se ha creado el rol AulaLectura con el privilegio Sys.Audit para comprobar el funcionamiento de los permisos sin dar acceso completo al usuario. También se ha actualizado el reparto de recursos debido al cambio de equipo anfitrión.

- Pruebas y resultados: Mediante los comandos pvesm status, df -h, lsblk, lvs, ip -br addr e ip route se ha comprobado la configuración del almacenamiento y de la red. Se ha probado el acceso del usuario amg-lectura@pve, obteniendo un HTTP 200 al consultar el estado del nodo y un HTTP 403 al consultar los registros del sistema. Tras retirar la asignación del rol, se ha comprobado que el usuario ya no puede consultar el estado del nodo. Los resultados coinciden con los esperados.

- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [03-instalacion-proxmox](./03-instalacion-proxmox.md). [Evidencias](../evidencias/Sesion04_05/capturas-o-salidas).

- Incidencias y solución o estado pendiente: Debido a los problemas que daba Windows, se decidió rehacer la máquina virtual de Proxmox utilizando VirtualBox en el ordenador con Linux de clase, manteniendo las configuraciones anteriores. Se ha tenido que actualizar el reparto de recursos debido al cambio de hardware. Las comprobaciones posteriores se han realizado correctamente.

- Siguiente paso: Creación y configuración de las máquinas virtuales srv-aso y cli-linux siguiendo el diseño de red y el reparto de recursos establecido.

## Sesion 06 - 

---------------------------------------------------------

[Volver al indice](../README.md)