Para el proyecto B
La red asignada es 192.168.20.0, es una clase C
Los hosts, deben tener asignadas IP's de esta red a partir de la vigesima primera IP disponible.
EL gw de cada una es la primera (1a) IP disponible


| Dispositivo | Interfaz                         | IP/Mask                                               | Gateway (si es que se requiere) | VLAN           |
| ----------- | -------------------------------- | ----------------------------------------------------- | ------------------------------- | -------------- |
| PrA1        | -                                | 192.168.10.11/24                                      | 192.168.10.1                    | 10             |
| PrA2        | -                                | 192.168.10.12/24                                      | 192.168.10.1                    | 10             |
| PrB1        | -                                | 192.168.20.21/24                                      | 192.168.20.1                    | 20             |
| PrB2        | -                                | 192.168.20.22/24                                      | 192.168.20.1                    | 20             |
| SA1         | VLAN99                           | 192.168.99.2/24                                       | 192.168.99.1                    |                |
| SA2         | VLAN99                           | 192.168.99.3/24                                       | 192.168.99.1                    |                |
| SD1         | VLAN99                           | 192.168.99.4/24                                       | 192.168.99.1                    |                |
| R1          | Gi0/0.10<br>Gi0/0.20<br>Gi0/0.99 | 192.168.10.1/24<br>192.168.20.1/24<br>192.168.99.1/24 |                                 | 10<br>20<br>99 |
| Adm1        | -                                | 192.168.99.10/24                                      | 192.168.99.1                    | 99             |
| Prn1        | -                                | 192.168.99.11/24                                      | 192.168.99.1                    | 99             |

Tabla de asignacion de VLANS

| VLAN | Nombre     | Tipo   | Puertos                                                              | Device          |
| ---- | ---------- | ------ | -------------------------------------------------------------------- | --------------- |
| 10   | Proyecto A | Datos  | Fa0/5-10                                                             | SA1, SA2        |
| 20   | Proyecto B | Datos  | Fa0/11-15                                                            | SA1, SA2        |
| 99   | Management | Admin  | Fa0/16-20                                                            | SA1, SA2, SD1   |
| 999  | ParkingLot | Datos  | Fa0/1-4                                                              | SA1, SA2        |
| 1000 | Nativa     | Nativa | Fa0/21-24, Gi0/1-2<br>Fa0/1-15,21-24, Gi0/1-2<br>(Todos los Gigabit) | SA1, SA2<br>SD1 |


```ciscoIOS
!Configuracion basicas de un switch
!Lab Redes II
!
enable
conf t
!Asignar un nombre distintivo al dispositivo
hostname SA1
!Asignar un dominio de trabajo
ip domain-name s8.lrc2.uaz.mx
!Deshabilitar la resolucion de nombres
no ip domain-lookup
service password-encryption
!Diseniar un banner o una etiqueta de presentacion
banner motd $
[+]-----------------------[+]
 | LabRedes2 - CCNAv7 SRWE |
 | Ejercicio M3 y M4       |
 | 5 de Semptiembre de 2024|
 | SA1                     |
 | Acceso restringido !!!  |
[+]-----------------------[+]
$
!Crear una base de datos local para usuario
username admin secret cisco123
!Asignar una password al nivel privilegiado
enable secret class123
!Habilitar el acceso por SSH
! 1. Generar una llave para el cifrado de datos
crypto key generate rsa
1024
! 2. Habilitar el ssh version 2
ip ssh version 2
! 3. Configurar las lineas de acceso (admin), consola y terminales virtuales
line console 0
! - Se use la base de datos local para acceso
login local
! - Se sincronizen los mensajes a consola
loggin synchronous
! - Tiempo de actividad 
exec-timeout 15
! - Lineas virtuales (vty)
line vty 0 15
login local
exec-timeout 15
! - Habilitar el acceso con ssh
transport input ssh


vlan 10
name ProyectoA
vlan 20
name ProyectoB
vlan 99
name Management
vlan 999
name ParkingLot
vlan 1000
name Native


interface range fa0/5-10
switchport mode access
switchport access vlan 10

interface range fa0/11-15
switchport mode access
switchport access vlan 20

interface range fa0/16-20
switchport mode access
switchport access vlan 99

interface range fa0/1-4
switchport mode access
switchport access vlan 999
shutdown

interface range fa0/5-10
switchport mode access
switchport access vlan 10

interface range fa0/21-24, gi0/1-2
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,99

interface vlan99
ip address 192.168.99.2 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.99.1

```

```
end
copy run start
sh vlan brief


```

```ciscoIOS
!Configuracion basicas de un switch
!Lab Redes II
!
enable
conf t
!Asignar un nombre distintivo al dispositivo
hostname SA2
!Asignar un dominio de trabajo
ip domain-name s8.lrc2.uaz.mx
!Deshabilitar la resolucion de nombres
no ip domain-lookup
service password-encryption
!Diseniar un banner o una etiqueta de presentacion
banner motd $
[+]-----------------------[+]
 | LabRedes2 - CCNAv7 SRWE |
 | Ejercicio M3 y M4       |
 | 5 de Semptiembre de 2024|
 | SA2                     |
 | Acceso restringido !!!  |
[+]-----------------------[+]
$
!Crear una base de datos local para usuario
username admin secret cisco123
!Asignar una password al nivel privilegiado
enable secret class123
!Habilitar el acceso por SSH
! 1. Generar una llave para el cifrado de datos
crypto key generate rsa
1024
! 2. Habilitar el ssh version 2
ip ssh version 2
! 3. Configurar las lineas de acceso (admin), consola y terminales virtuales
line console 0
! - Se use la base de datos local para acceso
login local
! - Se sincronizen los mensajes a consola
loggin synchronous
! - Tiempo de actividad 
exec-timeout 15
! - Lineas virtuales (vty)
line vty 0 15
login local
exec-timeout 15
! - Habilitar el acceso con ssh
transport input ssh


vlan 10
name ProyectoA
vlan 20
name ProyectoB
vlan 99
name Management
vlan 999
name ParkingLot
vlan 1000
name Native


interface range fa0/5-10
switchport mode access
switchport access vlan 10

interface range fa0/11-15
switchport mode access
switchport access vlan 20

interface range fa0/16-20
switchport mode access
switchport access vlan 99

interface range fa0/1-4
switchport mode access
switchport access vlan 999
shutdown

interface range fa0/5-10
switchport mode access
switchport access vlan 10

interface range fa0/21-24, gi0/1-2
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,99

interface vlan99
ip address 192.168.99.3 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.99.1
```



```ciscoIOS
!Configuracion basicas de un switch
!Lab Redes II
!
enable
conf t
!Asignar un nombre distintivo al dispositivo
hostname SD1
!Asignar un dominio de trabajo
ip domain-name s8.lrc2.uaz.mx
!Deshabilitar la resolucion de nombres
no ip domain-lookup
service password-encryption
!Diseniar un banner o una etiqueta de presentacion
banner motd $
[+]-----------------------[+]
 | LabRedes2 - CCNAv7 SRWE |
 | Ejercicio M3 y M4       |
 | 5 de Semptiembre de 2024|
 | SD1                     |
 | Acceso restringido !!!  |
[+]-----------------------[+]
$
!Crear una base de datos local para usuario
username admin secret cisco123
!Asignar una password al nivel privilegiado
enable secret class123
!Habilitar el acceso por SSH
! 1. Generar una llave para el cifrado de datos
crypto key generate rsa
1024
! 2. Habilitar el ssh version 2
ip ssh version 2
! 3. Configurar las lineas de acceso (admin), consola y terminales virtuales
line console 0
! - Se use la base de datos local para acceso
login local
! - Se sincronizen los mensajes a consola
loggin synchronous
! - Tiempo de actividad 
exec-timeout 15
! - Lineas virtuales (vty)
line vty 0 15
login local
exec-timeout 15
! - Habilitar el acceso con ssh
transport input ssh


vlan 10
name ProyectoA
vlan 20
name ProyectoB
vlan 99
name Management
vlan 999
name ParkingLot
vlan 1000
name Native




interface range fa0/16-20
switchport mode access
switchport access vlan 99


interface range fa0/1-15,fa0/21-24,gi0/1-2
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,99

interface vlan99
ip address 192.168.99.4 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.99.1
```