# Bitacora de sesiones

La bitacora es el registro cronologico del trabajo. Anade una entrada al final
de cada sesion y conserva las anteriores. Escribe datos comprobables y enlaza
las evidencias utiles; evita frases generales como "he trabajado en Proxmox".

## Sesion 01 - 22/09/2026

- Objetivo: iniciar GitHub y la documentacion Markdown.
- Acciones realizadas: Creación estructura con el script y primer commit: configuraciones, cambios o      investigacion.
- Decisiones y motivos: PENDIENTE: eleccion realizada y alternativas valoradas.
- Pruebas y resultados: PENDIENTE: resultado esperado, comando o metodo y resultado obtenido.
- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [evidencias](../evidencias/Sesion01/capturas-o-salidas)
- Incidencias y solucion o estado pendiente: PENDIENTE.
- Siguiente paso: Preparar comandos para recopilar la información.

## Sesion 02 - 13/10/2026

- Objetivo: Investigar inventario y viabilidad [inventario-hardware](01-inventario-hardware.md)
- Acciones realizadas: Investigación del equipo y recopilación de sus datos. Contestación a las preguntas.
- Decisiones y motivos:
Se trabaja con el equipo físico y no con VirtualBox, para investigar el hardware y ver si es utilizable. Se prevé instalar con ext4 sobre LVM y no con ZFS, porque ZFS consume más RAM para su caché y solo hay un disco y poca memoria. Se asigna 1 vCPU a cada invitado y se prefiere LXC para el servidor, que consume menos que una VM completa.
- Pruebas y resultados: ***Resultado esperado:***
Confirmamos que la CPU y la placa permiten virtualizar.
***Método:***
Visualización de las especificaciones del equipo mediante la BIOS y comandos a través de la terminal.
***Resultado obtenido:***
VT-x soportado y salidas de los comandos con las especificaciones guardadas en archivos txt.[evidencias](../evidencias/Sesion02/capturas-o-salidas)
- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [evidencias](../evidencias/Sesion02/capturas-o-salidas). [01-inventario-hardware](./01-inventario-hardware.md)
- Incidencias y solucion o estado pendiente:
    - No teníamos permisos ni de administrador ni de root en los equipos, por lo que no podíamos ejecutar los comandos necesarios. Por lo que ejecutamos con un USB booteable un Debian Live en el que sí tenemos permisos.
    - El primer borrador incluía datos sin evidencia de comandos (tipo de disco y número de módulos de RAM). Se comprobaron y corrigieron el 13-10-2026
- Siguiente paso: Guardar las salidas de los comandos (lscpu, free, lsblk, df, ip, lspci, dmidecode y smartctl) y completar el inventario.

Anadir una entrada con esta estructura en cada sesion. El historial de
GitHub registra los commits; no hace falta incluir el hash del propio
commit dentro de su entrada de bitacora.

[Volver al indice](../README.md)

## Sesion 03 - 20/10/2026

- Objetivo: Realizar el diagrama de red.
- Acciones realizadas: Diseño de red, y documentación.
- Decisiones y motivos: PENDIENTE: eleccion realizada y alternativas valoradas.
- Pruebas y resultados: PENDIENTE: resultado esperado, comando o metodo y resultado obtenido.
- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md). [evidencias](../evidencias/Sesion03/capturas-o-salidas)
- Incidencias y solucion o estado pendiente: PENDIENTE.
- Siguiente paso: Instalación y configuración proxmox y hypervisor.
