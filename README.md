<p align="center">
  <img src="https://img.shields.io/badge/Windows%20Server-2025-0078D4?style=for-the-badge&logo=windows&logoColor=white" />
  <img src="https://img.shields.io/badge/RDS-RemoteApp-2D7D9A?style=for-the-badge" />
  <img src="https://img.shields.io/badge/RD%20Web-Access-2563EB?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Web%20Client-HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" />
  <img src="https://img.shields.io/badge/IIS-Web%20Server-5E5E5E?style=for-the-badge" />
  <img src="https://img.shields.io/badge/NPS-RADIUS-6D28D9?style=for-the-badge" />
  <img src="https://img.shields.io/badge/AAA-Cisco-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white" />
  <img src="https://img.shields.io/badge/Kali-Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/GNS3-Lab-F97316?style=for-the-badge" />
</p>

# Laboratorio RDS, IIS, NPS/RADIUS y AAA Cisco

**Autor:** Michael David Robles Fermín  
**Matrícula:** 2025-0845  
**Asignatura:** Seguridad de Redes  
**Servidor:** Windows Server 2025  
**Dominio:** `michael20250845.local`  
**FQDN RDS/RDWeb/RD Gateway:** `srv-rds-nps.redes.local`  
**Video de YouTube:** [Ver video](https://youtu.be/Ss2mVlgVoF8)  
**Link directo del video:** https://youtu.be/Ss2mVlgVoF8  

---

## Enlaces del laboratorio

- Página IIS personalizada: `http://10.8.45.130/`
- RD Web Access clásico: `https://srv-rds-nps.redes.local/RDWeb/Pages/en-US/login.aspx`
- RD Web Client HTML5: `https://srv-rds-nps.redes.local/RDWeb/webclient/index.html`
- SSH hacia R1: `10.8.45.1`

---

## Objetivo

El objetivo de este laboratorio es implementar una infraestructura de acceso remoto usando Windows Server 2025, Remote Desktop Services, RemoteApp, IIS, RD Web Access clásico, RD Web Client HTML5 y RD Gateway.

Además, se configuró Windows Server NPS como servidor RADIUS para que un router Cisco IOSv autentique usuarios mediante AAA. Con esta configuración, el router no depende únicamente de usuarios locales, sino que consulta al servidor NPS para validar credenciales y asignar niveles de privilegio diferentes según los grupos de Active Directory.

El laboratorio demuestra dos partes principales:

1. La publicación de una página IIS personalizada mediante RemoteApp.
2. La autenticación SSH del router R1 usando RADIUS/NPS con privilegio 15 y privilegio 1.

---

## Estructura del repositorio

```text
RDS_NPS_RADIUS_20250845/
├── README.md
├── images/
│   ├── 01-topologia.png
│   ├── 02-iis-pagina-personalizada.png
│   ├── 03-rdweb-access-login.png
│   ├── 04-rdweb-access-work-resources.png
│   ├── 05-xfreerdp3-comando-rdp.png
│   ├── 06-rdp-sesion-iniciando.png
│   ├── 07-remoteapp-iis-desde-rdp.png
│   ├── 08-rdwebclient-html5-login.png
│   ├── 09-rdwebclient-html5-work-resources.png
│   ├── 10-rdwebclient-html5-recurso-abierto.png
│   ├── 11-nps-radius-clients.png
│   ├── 12-nps-network-policies.png
│   ├── 13-nps-policy-privilege-15.png
│   ├── 14-nps-policy-privilege-1.png
│   ├── 15-r1-login-local.png
│   ├── 16-kali-ssh-admin15.png
│   ├── 17-kali-ssh-operador1.png
│   ├── 18-r1-show-privilege-operador1.png
│   ├── 19-r1-show-privilege-admin15.png
│   ├── 20-r1-show-aaa-servers-1.png
│   ├── 21-r1-show-aaa-servers-2.png
│   ├── 22-r1-show-aaa-sessions.png
│   └── 23-debug-aaa-radius-activo.png
├── configs/
│   ├── R1.cfg
│   ├── SW1.cfg
│   └── SW2.cfg
└── docs/
    └── resumen-laboratorio-20250845.txt
```

---

## Topología y direccionamiento

La topología se diseñó con un router central R1, dos switches, un cliente Kali Linux y un Windows Server 2025. R1 funciona como punto de enrutamiento entre la red del cliente y la red del servidor.

Del lado izquierdo se encuentra Kali Linux, que se utiliza para probar el acceso web, RemoteApp y SSH hacia el router. Del lado derecho se encuentra el Windows Server, donde se configuraron IIS, RDS, RD Web Access, RD Web Client HTML5, RD Gateway, Active Directory, DNS y NPS/RADIUS.

![Topología del laboratorio](images/01-topologia.png)

| Dispositivo | Interfaz | Dirección IP | Descripción |
|---|---|---:|---|
| R1 | Gi0/0 | `10.8.45.1/25` | Red cliente hacia SW1/Kali |
| R1 | Gi0/1 | `10.8.45.129/25` | Red servidor hacia SW2/Windows |
| SW1 | VLAN 20 | `10.8.45.2/25` | Administración de SW1 |
| SW2 | VLAN 10 | `10.8.45.131/25` | Administración de SW2 |
| Kali Linux | eth0 | `10.8.45.10/25` | Cliente de pruebas |
| Windows Server | Ethernet0 2 | `10.8.45.130/25` | IIS, RDS, RD Gateway y NPS |

La red `10.8.45.0/25` corresponde al segmento del cliente Kali. La red `10.8.45.128/25` corresponde al segmento del Windows Server. R1 permite la comunicación entre ambos segmentos y también actúa como cliente RADIUS frente al servidor NPS.

---

## Servicios configurados

En Windows Server 2025 se configuraron los siguientes servicios:

- Active Directory Domain Services.
- DNS.
- IIS con página personalizada.
- Remote Desktop Services.
- RemoteApp.
- RD Web Access clásico.
- RD Web Client HTML5.
- RD Gateway.
- NPS como servidor RADIUS.

En el router R1 se configuró:

- Usuario local de respaldo.
- SSH para administración remota.
- AAA.
- Autenticación por RADIUS.
- Autorización por RADIUS.
- Accounting hacia RADIUS.
- Verificación con `show aaa servers`.
- Verificación con `show aaa sessions`.
- Debug de AAA y RADIUS.

---

## IIS y página personalizada

IIS funciona como servidor web del laboratorio. La página personalizada identifica la práctica de Seguridad de Redes y contiene accesos hacia los servicios RDP publicados.

![Página personalizada de IIS](images/02-iis-pagina-personalizada.png)

En esta evidencia se observa que la página web personalizada responde correctamente desde el cliente. La página muestra información del laboratorio y enlaces hacia los accesos de RemoteApp y RD Web Client HTML5.

La página IIS se encuentra publicada en:

```text
http://10.8.45.130/
```

---

## RD Web Access clásico

RD Web Access clásico es el portal tradicional de Remote Desktop Services. Desde este portal el usuario inicia sesión y visualiza los recursos publicados en la colección de RemoteApps.

![Inicio de sesión en RD Web Access clásico](images/03-rdweb-access-login.png)

En esta captura se muestra el inicio de sesión al portal clásico usando el usuario `MICHAEL20250845\admin15`. Este usuario pertenece al dominio del laboratorio y tiene permiso para abrir los recursos publicados.

![Recurso publicado en RD Web Access](images/04-rdweb-access-work-resources.png)

Aquí se observa el recurso **Laboratorio IIS RDS NPS AAA**. Este recurso no es un escritorio completo, sino una RemoteApp que ejecuta Microsoft Edge para abrir la página personalizada de IIS.

---

## Prueba de RemoteApp con xfreerdp3

Desde Kali Linux se utilizó `xfreerdp3` para abrir el archivo `.rdp` generado desde RD Web Access clásico.

![Ejecución del archivo RDP con xfreerdp3](images/05-xfreerdp3-comando-rdp.png)

Comando usado:

```bash
xfreerdp3 cpub-LabIIS-LabRemoteApps-CmsRdsh.rdp /u:admin15 /p:'Lab20250845*' /d:MICHAEL20250845 /cert:ignore /size:1366x768 /log-level:ERROR
```

Este comando abre el recurso publicado desde Kali. Se especifica el usuario `admin15`, el dominio `MICHAEL20250845` y se ignora la validación del certificado con `/cert:ignore` para evitar errores de confianza dentro del entorno de laboratorio.

![Inicio de sesión RDP](images/06-rdp-sesion-iniciando.png)

Esta imagen muestra la sesión remota iniciándose. El cliente RDP autentica al usuario contra el entorno de Windows Server y carga la RemoteApp publicada.

![Página IIS abierta desde RemoteApp](images/07-remoteapp-iis-desde-rdp.png)

Después de autenticarse, la RemoteApp abre Microsoft Edge y carga la página de IIS del laboratorio. Esto confirma que la página personalizada fue publicada correctamente mediante RemoteApp.

---

## RD Web Client HTML5

RD Web Client HTML5 es el cliente moderno de Remote Desktop Services. Permite abrir los recursos publicados directamente desde el navegador, sin depender de descargar manualmente un archivo `.rdp`.

![Inicio de sesión en RD Web Client HTML5](images/08-rdwebclient-html5-login.png)

La captura muestra el inicio de sesión en el cliente HTML5 usando el mismo dominio y usuario del laboratorio.

![Recurso publicado en RD Web Client HTML5](images/09-rdwebclient-html5-work-resources.png)

Aquí se observa el recurso disponible dentro del Web Client HTML5. Esto confirma que el feed de RemoteApps se entrega correctamente al usuario autenticado.

![RemoteApp abierta desde Web Client HTML5](images/10-rdwebclient-html5-recurso-abierto.png)

Al abrir el recurso desde el Web Client, el navegador remoto carga directamente la página de IIS. Esta prueba valida el funcionamiento de RD Web Client HTML5, RD Gateway y la RemoteApp publicada.

---

## NPS/RADIUS en Windows Server

NPS funciona como el servidor RADIUS del laboratorio. Su función es recibir solicitudes de autenticación enviadas por el router R1, validar los usuarios contra Active Directory y devolver una autorización de privilegio según la política correspondiente.

El flujo real de autenticación es el siguiente:

```text
Kali Linux 10.8.45.10
        |
        | SSH
        v
R1 10.8.45.1
        |
        | RADIUS
        v
Windows Server NPS 10.8.45.130
```

Kali no se autentica directamente contra NPS. Kali se conecta por SSH al router R1. Luego R1 consulta al servidor NPS usando RADIUS para validar el usuario y recibir el nivel de privilegio.

![Cliente RADIUS R1 en NPS](images/11-nps-radius-clients.png)

En esta evidencia se observa el cliente RADIUS **R1**, configurado con la IP `10.8.45.129`. Esta IP corresponde a la interfaz de R1 del lado del Windows Server. Es importante porque NPS identifica al router por esa dirección cuando recibe las solicitudes RADIUS.

Datos principales del cliente RADIUS:

```text
Nombre: R1
Dirección IP: 10.8.45.129
Vendor: Cisco
Shared secret: Radius20250845*
Estado: Enabled
```

---

## Políticas NPS para privilegio Cisco

En NPS se configuraron dos políticas de red para separar los niveles de acceso al router.

![Políticas de red NPS](images/12-nps-network-policies.png)

En esta evidencia se observan las políticas **Cisco AAA Privilege 15** y **Cisco AAA Privilege 1**. Estas políticas son las que permiten que NPS no solo autentique el usuario, sino que también devuelva el nivel de privilegio que R1 debe asignar.

### Política Cisco AAA Privilege 15

![Política Cisco AAA Privilege 15](images/13-nps-policy-privilege-15.png)

La política de privilegio 15 aplica al grupo:

```text
MICHAEL20250845\RADIUS_PRIV15
```

El usuario asociado a este grupo es:

```text
admin15
```

La política devuelve al router el atributo Cisco:

```text
Cisco-AV-Pair = shell:priv-lvl=15
```

Esto significa que, cuando `admin15` se conecta por SSH, NPS valida sus credenciales y le indica al router que debe entrar con privilegio 15. En Cisco IOS, el privilegio 15 representa acceso administrativo completo.

### Política Cisco AAA Privilege 1

![Política Cisco AAA Privilege 1](images/14-nps-policy-privilege-1.png)

La política de privilegio 1 aplica al grupo:

```text
MICHAEL20250845\RADIUS_PRIV1
```

El usuario asociado a este grupo es:

```text
operador1
```

La política devuelve al router el atributo Cisco:

```text
Cisco-AV-Pair = shell:priv-lvl=1
```

Esto significa que `operador1` puede autenticarse correctamente, pero entra con permisos básicos. Esta diferencia demuestra que RADIUS no solo permite o niega acceso, sino que también puede autorizar distintos niveles de privilegio.

---

# AAA/RADIUS en R1

AAA significa:

```text
Authentication
Authorization
Accounting
```

En este laboratorio:

- **Authentication** valida el usuario y la contraseña.
- **Authorization** define qué nivel de privilegio tendrá el usuario después de autenticarse.
- **Accounting** registra eventos de inicio y cierre de sesión en el servidor RADIUS.

La configuración relevante del router se encuentra en:

```text
configs/R1.cfg
```

---

## Configuración AAA principal en R1

### Activación del modelo AAA

```cisco
aaa new-model
```

Este comando habilita el modelo AAA en Cisco IOS. Sin `aaa new-model`, el router no usaría listas de autenticación, autorización ni accounting definidas para consultar un servidor externo como RADIUS.

---

### Grupo de servidores RADIUS

```cisco
aaa group server radius NPS-GROUP
 server name NPS-SERVER
```

Este bloque crea el grupo `NPS-GROUP`, que representa al servidor RADIUS/NPS configurado en Windows Server. La idea es que las líneas de autenticación y autorización no apunten directamente a una IP, sino a un grupo de servidores RADIUS.

En este laboratorio el servidor real es:

```text
10.8.45.130
```

---

### Servidor RADIUS

```cisco
radius server NPS-SERVER
 address ipv4 10.8.45.130 auth-port 1812 acct-port 1813
 key Radius20250845*
```

Este bloque define el servidor NPS como servidor RADIUS para R1.

- `10.8.45.130` es la IP del Windows Server.
- `auth-port 1812` se usa para autenticación RADIUS.
- `acct-port 1813` se usa para accounting RADIUS.
- `key Radius20250845*` es el secreto compartido entre R1 y NPS.

El secreto compartido debe coincidir en ambos lados. Si el secreto configurado en R1 no coincide con el de NPS, el servidor puede rechazar o ignorar las solicitudes del router.

---

### Interfaz origen para RADIUS

```cisco
ip radius source-interface GigabitEthernet0/1
```

Este comando hace que R1 envíe las solicitudes RADIUS usando como origen la IP de `GigabitEthernet0/1`, que en este laboratorio es:

```text
10.8.45.129
```

Esto es importante porque en NPS el cliente RADIUS R1 fue registrado con la IP `10.8.45.129`. Si el router enviara las solicitudes desde otra IP, NPS podría no reconocerlo como cliente autorizado.

---

### Autenticación de login

```cisco
aaa authentication login default group NPS-GROUP local
```

Esta línea define cómo se autentican los usuarios cuando intentan entrar al router.

El orden es:

```text
1. Primero consultar RADIUS mediante NPS-GROUP.
2. Si RADIUS no responde, usar la base local del router.
```

Por eso existe el usuario local `localadmin`. Ese usuario funciona como respaldo administrativo si el servidor RADIUS no está disponible.

---

### Autorización EXEC

```cisco
aaa authorization exec default group NPS-GROUP local if-authenticated
```

Esta línea es una de las más importantes del laboratorio. La autenticación solo valida si el usuario y la contraseña son correctos. La autorización EXEC define qué permisos recibirá el usuario al entrar.

En este caso, R1 consulta a NPS y espera recibir un atributo Cisco como:

```text
shell:priv-lvl=15
```

o

```text
shell:priv-lvl=1
```

Gracias a esta parte, `admin15` entra con privilegio 15 y `operador1` entra con privilegio 1. Sin esta configuración, el router podría autenticar el usuario, pero no aplicar correctamente el nivel de privilegio enviado por NPS.

---

### Accounting de sesiones

```cisco
aaa accounting exec default start-stop group NPS-GROUP
aaa accounting system default start-stop group NPS-GROUP
```

El accounting registra eventos relacionados con las sesiones. Con `start-stop`, el router envía información cuando una sesión inicia y cuando termina.

Esto permite que el servidor RADIUS tenga evidencia de los accesos realizados. En la práctica, esta parte se valida con los contadores que aparecen en `show aaa servers`.

---

### Identificador común de sesión AAA

```cisco
aaa session-id common
```

Este comando permite que los registros AAA usen un identificador común de sesión. Ayuda a correlacionar eventos de autenticación, autorización y accounting dentro del router.

---

## Configuración SSH y líneas VTY

Para que Kali pueda conectarse remotamente al router, R1 fue configurado para aceptar SSH en las líneas VTY.

```cisco
ip ssh version 2
ip ssh time-out 60
line vty 0 4
 transport input ssh
 login authentication default
 authorization exec default
 accounting exec default
```

Esta configuración hace lo siguiente:

- `ip ssh version 2` habilita SSH versión 2.
- `transport input ssh` permite únicamente SSH en las líneas VTY.
- `login authentication default` aplica la lista AAA de autenticación.
- `authorization exec default` aplica la autorización AAA para asignar privilegios.
- `accounting exec default` registra la sesión mediante accounting.

En otras palabras, cuando Kali se conecta por SSH a R1, la línea VTY usa AAA. R1 recibe el intento de acceso, consulta a NPS por RADIUS y luego asigna el nivel de privilegio correspondiente.

---

## Usuario local de respaldo

```cisco
username localadmin privilege 15 secret Local20250845*
enable secret Enable20250845*
```

El usuario `localadmin` se configuró como respaldo local. Esto es importante porque, si RADIUS falla o el Windows Server no responde, el router todavía puede ser administrado localmente.

El `enable secret` protege el acceso al modo privilegiado del router.

---

## Validación con usuario local

![Inicio de sesión local en R1](images/15-r1-login-local.png)

Esta evidencia muestra el acceso al router con el usuario local `localadmin`. Esta prueba valida que existe una cuenta de respaldo en R1 y que el equipo no depende únicamente del servidor RADIUS para administración.

Esta parte se relaciona con la configuración:

```cisco
username localadmin privilege 15 secret Local20250845*
aaa authentication login default group NPS-GROUP local
```

El `local` al final de la línea AAA indica que la base local del router puede usarse como respaldo si RADIUS no responde.

---

## Validación SSH por RADIUS desde Kali

Para conectarse desde Kali fue necesario usar opciones SSH heredadas porque Cisco IOSv utiliza algoritmos antiguos que OpenSSH moderno bloquea por defecto.

### Prueba SSH con admin15

![SSH desde Kali con admin15](images/16-kali-ssh-admin15.png)

En esta evidencia, Kali se conecta por SSH hacia R1 usando el usuario `admin15`.

Comando usado:

```bash
ssh -o KexAlgorithms=+diffie-hellman-group14-sha1 \
    -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    admin15@10.8.45.1
```

Esta prueba se relaciona directamente con estas configuraciones del router:

```cisco
line vty 0 4
 transport input ssh
 login authentication default
 authorization exec default
 accounting exec default
```

Cuando `admin15` intenta entrar, R1 recibe la conexión SSH y usa la lista AAA `default`. Luego consulta al servidor NPS mediante RADIUS. Como `admin15` pertenece al grupo `RADIUS_PRIV15`, NPS devuelve:

```text
shell:priv-lvl=15
```

Por eso el usuario entra con permisos administrativos.

---

### Prueba SSH con operador1

![SSH desde Kali con operador1](images/17-kali-ssh-operador1.png)

En esta evidencia, Kali se conecta por SSH hacia R1 usando el usuario `operador1`.

Comando usado:

```bash
ssh -o KexAlgorithms=+diffie-hellman-group14-sha1 \
    -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    operador1@10.8.45.1
```

`operador1` también se autentica mediante RADIUS, pero pertenece al grupo `RADIUS_PRIV1`. Por eso NPS devuelve:

```text
shell:priv-lvl=1
```

Esto permite que el usuario entre al router, pero con privilegios limitados. La prueba demuestra que el acceso no es igual para todos los usuarios y que la autorización se controla desde NPS.

---

## Verificación de privilegios asignados

### Privilegio de operador1

![show privilege operador1](images/18-r1-show-privilege-operador1.png)

Con `show privilege` se confirma que `operador1` quedó en nivel 1.

Esto valida que la política **Cisco AAA Privilege 1** funcionó correctamente y que R1 aplicó el atributo enviado por NPS:

```text
shell:priv-lvl=1
```

El nivel 1 representa acceso básico. El usuario puede entrar al router, pero no tiene permisos administrativos completos.

---

### Privilegio de admin15

![show privilege admin15](images/19-r1-show-privilege-admin15.png)

Con `show privilege` se confirma que `admin15` quedó en nivel 15.

Esto valida que la política **Cisco AAA Privilege 15** funcionó correctamente y que R1 aplicó el atributo enviado por NPS:

```text
shell:priv-lvl=15
```

El nivel 15 representa acceso administrativo completo en Cisco IOS.

---

## Verificación de servidores RADIUS en R1

![show aaa servers parte 1](images/20-r1-show-aaa-servers-1.png)

El comando `show aaa servers` confirma que R1 tiene comunicación con el servidor RADIUS `10.8.45.130`.

Esta evidencia valida la parte de configuración:

```cisco
radius server NPS-SERVER
 address ipv4 10.8.45.130 auth-port 1812 acct-port 1813
 key Radius20250845*
```

En la salida se observa que el servidor se encuentra en estado operativo y que se han procesado solicitudes de autenticación.

![show aaa servers parte 2](images/21-r1-show-aaa-servers-2.png)

La continuación del comando muestra contadores relacionados con autenticación y accounting. Estos contadores demuestran que R1 no solo tiene configurado el servidor RADIUS, sino que realmente lo utilizó durante las pruebas.

Los contadores más importantes son:

- Solicitudes de autenticación.
- Respuestas aceptadas.
- Respuestas rechazadas.
- Transacciones exitosas.
- Solicitudes de accounting.
- Registros de inicio y cierre de sesión.

Esto se relaciona con:

```cisco
aaa accounting exec default start-stop group NPS-GROUP
aaa accounting system default start-stop group NPS-GROUP
```

---

## Verificación de sesiones AAA

![show aaa sessions](images/22-r1-show-aaa-sessions.png)

El comando `show aaa sessions` muestra sesiones AAA activas o recientes dentro del router. En esta evidencia se observan usuarios como `localadmin`, `admin15` y `operador1`.

Esta prueba confirma que las sesiones de acceso fueron procesadas por el sistema AAA del router. También demuestra que las pruebas SSH desde Kali quedaron registradas dentro del estado AAA de R1.

---

## Debug AAA y RADIUS

```cisco
debug aaa authentication
debug aaa authorization
debug aaa accounting
debug radius
```

Estos comandos permiten observar en tiempo real el proceso AAA y la comunicación RADIUS del router.

![Activación de debug AAA y RADIUS en R1](images/23-debug-aaa-radius-activo.png)

En esta evidencia se muestra la activación de los comandos de depuración solicitados para el proceso AAA y RADIUS en R1. Se ejecutan `debug aaa authentication`, `debug aaa authorization` y `debug radius`.

Esta captura se incluye como evidencia complementaria de los mecanismos de depuración usados durante la práctica. Aunque algunos subtipos internos de RADIUS aparecen como `off`, el debug principal del protocolo RADIUS queda activo, junto con la depuración de autenticación y autorización AAA.

Estos debug permiten validar que R1 estaba preparado para mostrar eventos relacionados con:

- Intentos de autenticación.
- Respuestas del servidor NPS.
- Asignación de autorización.
- Comunicación RADIUS.
- Procesamiento AAA del router.

Para apagar los debug:

```cisco
undebug all
show debugging
```

---

## Credenciales de laboratorio

> Estas credenciales son exclusivamente para el entorno controlado del laboratorio.

| Uso | Usuario | Contraseña |
|---|---|---|
| Kali Linux | `kali` | `kali` |
| RD Web / RADIUS privilegio 15 | `MICHAEL20250845\admin15` | `Lab20250845*` |
| RD Web / RADIUS privilegio 1 | `MICHAEL20250845\operador1` | `Lab20250845*` |
| Router usuario local | `localadmin` | `Local20250845*` |
| Router enable secret | `enable` | `Enable20250845*` |
| RADIUS shared secret | R1/NPS | `Radius20250845*` |

---

## Comandos útiles

### Prueba SSH con admin15

```bash
ssh -o KexAlgorithms=+diffie-hellman-group14-sha1 \
    -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    admin15@10.8.45.1
```

### Prueba SSH con operador1

```bash
ssh -o KexAlgorithms=+diffie-hellman-group14-sha1 \
    -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    operador1@10.8.45.1
```

### Verificación en R1

```cisco
show ip interface brief
show running-config | section aaa
show running-config | section radius
show running-config | section line vty
show aaa servers
show aaa sessions
show users
show privilege
```

### Debugs solicitados

```cisco
terminal monitor
debug aaa authentication
debug aaa authorization
debug aaa accounting
debug radius
```

### Apagar debug

```cisco
undebug all
show debugging
```

---

## Resumen de validación

| Requisito | Estado |
|---|---|
| Página IIS personalizada | Completado |
| Publicación de IIS mediante RemoteApp | Completado |
| Acceso por RD Web Access clásico | Completado |
| Acceso por RD Web Client HTML5 | Completado |
| RD Gateway funcional | Completado |
| NPS como servidor RADIUS | Completado |
| Cliente RADIUS R1 registrado en NPS | Completado |
| Grupo de privilegio 15 | Completado |
| Grupo de privilegio 1 | Completado |
| Usuario `admin15` con privilegio 15 | Completado |
| Usuario `operador1` con privilegio 1 | Completado |
| SSH desde Kali hacia R1 usando RADIUS | Completado |
| `show aaa servers` verificado | Completado |
| `show aaa sessions` verificado | Completado |
| Debug AAA/RADIUS activado | Completado |

---

## Conclusión

El laboratorio cumple con los requerimientos principales de la práctica. Se configuró una página personalizada de IIS y se publicó mediante RemoteApp, RD Web Access clásico y RD Web Client HTML5.

También se implementó NPS como servidor RADIUS para centralizar la autenticación del router Cisco IOSv. R1 fue configurado con AAA para autenticar usuarios por RADIUS, autorizar niveles de privilegio mediante `Cisco-AV-Pair` y registrar sesiones mediante accounting.

Las pruebas desde Kali confirmaron que `admin15` entra con privilegio 15 y `operador1` entra con privilegio 1. Además, los comandos `show aaa servers`, `show aaa sessions` y los debug de AAA/RADIUS validan que el router procesó correctamente la autenticación, autorización y comunicación con el servidor NPS.
