*Este proyecto ha sido creado como parte del curso de estudios 42 por amarlasc*

# Born2beroot

## Descripción

Born2beroot es un proyecto de administración de sistemas cuyo objetivo es introducir los conceptos fundamentales de virtualización, sistemas operativos, seguridad, usuarios, permisos, redes y administración de servidores.

El proyecto consiste en crear y configurar una máquina virtual utilizando VirtualBox como hypervisor y Debian como sistema operativo. El hipervisor es una capa de software que permite crear y ejecutar varias máquinas virtuales en una sola computadora.

Durante el proyecto se configura un sistema seguro y funcional, trabajando principalmente desde la terminal y aprendiendo a administrar el sistema mediante herramientas y servicios de Linux.

## Virtualización

Una máquina virtual permite ejecutar un sistema operativo dentro de otro sistema operativo. Las máquinas virtuales tienen varios propósitos: ofrecen **aislamiento**, permitiendo probar o desarrollar software en un entorno seguro sin afectar al sistema anfitrión; mejoran la **eficiencia de recursos**, ya que permiten ejecutar varios sistemas operativos sobre una misma máquina física; aportan **independencia de plataforma**, al poder correr distintos sistemas operativos sobre el mismo hardware; facilitan la **recuperación ante fallos**, gracias a la posibilidad de hacer copias de seguridad y restaurar el estado completo del sistema; y permiten dar **soporte a sistemas heredados** (legacy), ejecutando sistemas operativos o aplicaciones antiguas que ya no serían compatibles de forma nativa.

VirtualBox actúa como hypervisor y proporciona a la máquina virtual recursos del ordenador físico, como CPU, memoria RAM, almacenamiento y red.

### VirtualBox vs UTM

| | VirtualBox | UTM |
|---|---|---|
| Tipo | Hypervisor de tipo 2 (software) | Hypervisor basado en QEMU (con soporte de virtualización nativa Apple Hypervisor en Mac) |
| Plataformas | Windows, Linux, macOS | Principalmente macOS (Intel y Apple Silicon) |
| Rendimiento | Bueno, algo más pesado en macOS Apple Silicon al emular x86 | Mejor rendimiento en Apple Silicon gracias a la virtualización nativa de ARM |
| Comunidad/documentación | Muy amplia, mucha documentación y soporte | Más reciente, comunidad más pequeña |
| Uso en este proyecto | Elegido por ser multiplataforma, gratuito, con mucha documentación y por ser el estándar usado en 42 | No usado, pero es la alternativa habitual para Mac con Apple Silicon |

He elegido VirtualBox por su compatibilidad multiplataforma, por ser gratuito y de código abierto, y por la enorme cantidad de documentación y comunidad disponible, lo que facilita resolver problemas durante el desarrollo del proyecto.

## Elección del sistema operativo

Debian es la distribución GNU/Linux utilizada como sistema operativo guest. La razón para elegirlo es que ofrece una experiencia sencilla y accesible para usuarios, lo que facilita el aprendizaje y la administración del sistema. Además, Debian cuenta con una amplia comunidad, una gran cantidad de documentación y un extenso repositorio de paquetes, lo que facilita encontrar información y soluciones durante el desarrollo del proyecto.

Para comprobar el sistema operativo utilizado, el comando es `hostnamectl` o `cat /etc/os-release`.

### apt vs aptitude

Ambos son gestores de paquetes para sistemas basados en Debian, pero con diferencias:

- **APT**: gestor de más bajo nivel, solo línea de comandos, más ligero y rápido. Es el que se usa por defecto en scripts y automatización.
- **Aptitude**: construido sobre APT, ofrece tanto línea de comandos como interfaz de texto interactiva, mejor resolución de dependencias y gestión más inteligente de paquetes huérfanos.

En este proyecto se ha utilizado APT por ser el estándar y el más directo para las tareas de instalación y actualización necesarias.

### Debian vs Rocky Linux

| | Debian | Rocky Linux |
|---|---|---|
| Origen | Distribución independiente, una de las más antiguas de GNU/Linux | Fork comunitario de RHEL (Red Hat Enterprise Linux), nace tras la desaparición de CentOS |
| Gestor de paquetes | APT (`.deb`) | DNF (`.rpm`) |
| Filosofía | Estabilidad, software libre, ciclo de lanzamiento propio | Compatibilidad total con el ecosistema empresarial de Red Hat |
| Comunidad | Muy grande y veterana, mucha documentación | Más orientada a entornos empresariales, comunidad más reciente |
| Seguridad | AppArmor por defecto | SELinux por defecto |
| Firewall | UFW (sobre iptables/nftables) | firewalld |
| Ventajas | Sencillez, ligereza, gran cantidad de paquetes y guías | Muy usado en producción empresarial, compatible con certificaciones RHEL |
| Desventajas | Menos orientado a entornos corporativos "enterprise" | Curva de aprendizaje algo mayor (SELinux, DNF), comunidad más joven |

Rocky Linux es una alternativa muy utilizada en entornos empresariales y es compatible con el ecosistema de Red Hat. Para este proyecto he preferido Debian por su sencillez y por ser una distribución especialmente adecuada para familiarizarme con la administración de sistemas Linux.

### AppArmor vs SELinux

AppArmor y SELinux son dos módulos de seguridad del kernel de Linux (LSM, *Linux Security Modules*) que restringen lo que pueden hacer los programas, aunque con enfoques distintos:

| | AppArmor (Debian) | SELinux (Rocky) |
|---|---|---|
| Enfoque | Basado en **rutas de archivo** (perfiles por ruta) | Basado en **etiquetas** (contextos de seguridad) asociadas a cada archivo y proceso |
| Complejidad | Más sencillo de configurar y entender | Más granular pero también más complejo de administrar |
| Perfiles | Se definen en `/etc/apparmor.d/` | Se gestionan con políticas y contextos (`chcon`, `semanage`, etc.) |
| Ventaja | Curva de aprendizaje más suave, buena para empezar | Control muy fino, estándar en entornos corporativos que usan RHEL |
| Desventaja | Menos granular que SELinux | Más difícil de depurar cuando bloquea algo inesperadamente |

En este proyecto, al usar Debian, el sistema de seguridad obligatorio disponible es AppArmor.

## Nombre de host

El hostname es la etiqueta única que se le asigna a un dispositivo para identificarlo dentro de una red de computadoras.

- `hostname`: comando para visualizar el hostname.
- `hostnamectl`: comando para visualizar el hostname y otra información del sistema. Si se combina con otros comandos permite hacer cambios en la configuración del sistema. Por ejemplo, para cambiar el hostname: `sudo hostnamectl set-hostname nuevo_nombre`.

Para poder visualizar el cambio se puede bien con el comando `sudo systemctl restart systemd-hostnamed` sin reiniciar el equipo, o reiniciando el equipo.

## Partición del disco

La partición de un disco consiste en dividir un disco físico o virtual en diferentes partes independientes llamadas particiones. Cada partición puede tener una función diferente, por ejemplo, almacenar el sistema operativo, los archivos personales o utilizarse como espacio de intercambio.

### ¿Qué es LVM?

LVM (Logical Volume Manager) es un sistema de gestión de volúmenes lógicos que añade una capa de abstracción sobre las particiones físicas, permitiendo redimensionar, mover o combinar espacio de almacenamiento sin necesidad de desmontar las particiones. Su estructura se compone de tres niveles:

- **Physical Volumes (PV)**: discos o particiones físicas.
- **Volume Groups (VG)**: agrupan uno o varios PV en un mismo "pool" de almacenamiento.
- **Logical Volumes (LV)**: particiones virtuales creadas dentro de un VG, que es lo que finalmente se formatea y se monta (por ejemplo, `root`, `swap` y `home` en este proyecto).

La principal ventaja frente al particionado tradicional es la flexibilidad: se puede redimensionar en caliente, mover datos entre discos o crear snapshots para copias de seguridad consistentes.

La partición del disco en este proyecto es la siguiente:

- **sda**: es el disco virtual completo de 12 GB.
- **sda1**: contiene `/boot`, necesario para arrancar Debian.
- **sda2**: espacio técnico reservado para el arranque mediante GRUB, que es el gestor de arranque de Linux. El flujo de arranque es el siguiente: primero, la BIOS busca algo que arrancar, encuentra a GRUB, GRUB localiza el sistema Linux y sus archivos de arranque, y GRUB inicia Debian. Como el sistema está cifrado con LUKS, después aparece la petición de contraseña para desbloquear el disco. En definitiva, GRUB es un gestor de arranque que se encarga de iniciar el sistema operativo y permite seleccionar qué sistema operativo o kernel queremos arrancar.
- **sda5**: contiene la mayor parte del espacio del disco y sirve como base para el cifrado.
- **sda5_crypt**: es sda5 cifrada mediante LUKS; necesitas la contraseña para desbloquearla.
  - **root**: contiene el sistema operativo, programas y configuraciones.
  - **swap**: espacio del disco utilizado como memoria de intercambio cuando la RAM no es suficiente.
  - **home**: contiene los archivos personales de los usuarios, como `/home/amarlasc`.

```
sda                              12G
├─ sda1                         791M
├─ sda2                           1K
└─ sda5                        11.2G
   └─ sda5_crypt               11.2G
      ├─ amarlasc42--vg-root    7.1G
      ├─ amarlasc42--vg-swap    624M
      └─ amarlasc42--vg-home    3.5G
```

## Usuarios y permisos

El sistema utiliza usuarios y grupos para controlar el acceso a recursos. Los principales comandos utilizados son:

- `adduser`: crea un nuevo usuario en el sistema, y también para añadir un usuario a un grupo.
- `usermod`: modifica la configuración de un usuario existente.
- `addgroup`: crea un nuevo grupo.
- `groups`: muestra los grupos a los que pertenece un usuario.
- `id`: muestra la información de un usuario, como su UID, GID y grupos.
- `chmod`: modifica los permisos de acceso de archivos y directorios.
- `chown`: cambia el propietario y/o grupo de un archivo o directorio.
- `getent group nombre_grupo`: verifica los usuarios pertenecientes a un grupo.

- `sudo userdel nombre_usuario`: borra un usuario.
- `getent passwd`: muestra todos los usuarios incluidos en el sistema.
- `sudo usermod -aG nombre_grupo nombre_usuario`: añade un usuario a un grupo.
- `sudo groupdel nombre_grupo`: borra un grupo.

## Sudo

`sudo` permite ejecutar determinados comandos con privilegios de administrador. Hay que aclarar la diferencia entre `su` y `sudo`: `su` cambia de sesión al usuario root pidiendo su contraseña, mientras que `sudo` ejecuta una sola orden temporalmente usando la contraseña del usuario actual. Para comprobar la versión de sudo instalada, el comando es `sudo --version`.

Si queremos añadir un usuario al grupo sudo, el comando sería `sudo usermod -aG sudo amarlasc`.

Su configuración se puede realizar tanto mediante `sudo visudo` como con `sudo nano /etc/sudoers.d/sudo_config`, permitiendo definir:
- Número máximo de intentos de autenticación.
- Mensajes personalizados.
- Registro de comandos ejecutados.
- `secure_path`.
- Tiempo de validez de la autenticación.

Si queremos comprobar donde se encuentra el archivo `sudo_config`, el comando sería `sudo ls -la /var/log/sudo`. Además del archivo `sudo_config`, está el archivo `seq`, que utiliza `sudo` para mantener una secuencia de los registros de las sesiones `sudo`.

El parámetro `secure_path` limita los directorios donde `sudo` puede buscar ejecutables:

```
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

## Política de contraseñas

La política de contraseñas se puede configurar en los archivos `/etc/login.defs` y `/etc/pam.d/common-password`. Para configurar este segundo hay que instalar la librería `libpam-pwquality`.

En `login.defs` se han modificado los siguientes parámetros:

- `PASS_MAX_DAYS`: máximo de días antes de que caduque la contraseña.
- `PASS_MIN_DAYS`: mínimo de días antes de que se pueda cambiar la contraseña.
- `PASS_WARN_AGE`: número de días antes de que caduque la contraseña para mostrar la advertencia.

En `common-password` se han modificado:

- `minlen`: establece la longitud mínima que debe tener la contraseña.
- `ucredit`: controla cuántas letras mayúsculas debe contener la contraseña (-1 = al menos 1).
- `dcredit`: controla cuántos números debe contener (-1 = al menos 1).
- `lcredit`: controla cuántas letras minúsculas debe contener (-1 = al menos 1).
- `maxrepeat`: limita cuántas veces seguidas se puede repetir el mismo carácter.
- `reject_username`: impide utilizar el nombre de usuario dentro de la contraseña.
- `difok`: establece cuántos caracteres deben ser diferentes respecto a la contraseña anterior.
- `enforce_for_root`: hace que estas reglas de contraseña también se apliquen al usuario root.

La política de contraseña elegida para el proyecto reduce la superficie de exposición del sistema ante un compromiso de credenciales: al forzar la caducidad cada 30 días se limita el tiempo que una contraseña filtrada sigue siendo válida, mientras que el mínimo de 2 días entre cambios evita que un usuario revierta rápidamente a una contraseña anterior para saltarse la caducidad. La longitud mínima de 10 caracteres con mayúscula, minúscula y número, junto con el límite de 3 caracteres idénticos consecutivos, dificulta los ataques de fuerza bruta y diccionario. Prohibir el nombre de usuario dentro de la contraseña evita que un atacante la deduzca por ingeniería social, y exigir al menos 7 caracteres distintos respecto a la anterior impide reutilizar variaciones mínimas de la misma contraseña. Aplicar estas reglas también a root tiene sentido porque es la cuenta con más privilegios del sistema y, por tanto, la que más daño puede causar si se compromete.

**Comandos relevantes**
- `sudo passwd nombre_usuario`: establece la contraseña de un usuario.
- `sudo passwd -S nombre_usuario`: resumen del estado de la contraseña.
- `sudo chage -l nombre_usuario`: información detallada sobre la caducidad.

## Firewall

Se utiliza UFW (Uncomplicated Firewall) para controlar las conexiones de red. El firewall permite definir qué conexiones entrantes están permitidas y cuáles deben bloquearse. Se caracteriza por utilizar comandos sencillos.

Comandos útiles:

- `sudo ufw allow puerto`: añadir un puerto.
- `sudo ufw status numbered`: listar las reglas con su número correspondiente.
- `sudo ufw delete número_regla`: elimina la regla indicando el número.
- `sudo ufw delete allow puerto`: borrar el puerto escribiendo la regla completa.
- `sudo ufw status`: comprobar el estado.

### UFW vs Firewalld

| | UFW (Debian) | Firewalld (Rocky) |
|---|---|---|
| Motor subyacente | iptables / nftables | nftables (con zonas) |
| Filosofía | Reglas simples y directas, pensado para facilidad de uso | Basado en "zonas" (trusted, public, etc.) que agrupan reglas según el nivel de confianza de la red |
| Sintaxis | Muy sencilla: `ufw allow <puerto>` | Algo más compleja, orientada a zonas: `firewall-cmd --add-port=<puerto>/tcp --permanent` |
| Recarga de reglas | Aplica los cambios directamente | Necesita `--reload` para aplicar cambios permanentes |
| Uso típico | Escritorio y servidores sencillos | Entornos empresariales con necesidades de segmentación de red más complejas |

He usado UFW por ser el firewall por defecto en Debian y por su sencillez de configuración y mantenimiento.

## SSH

SSH (Secure Shell) permite acceder remotamente a la máquina mediante una conexión cifrada. En este proyecto se configura el servicio SSH para que escuche en el puerto 4242 dentro de la máquina virtual (guest). La razón para cambiar SSH a un puerto no estandar, más allá de que el subject lo exija, es porque así se reduce la exposición a escaneos automáticos. Normalmente, los bots y ataques automatizados de fuerza bruta escanean primero los puertos conocidos, como el puerto 22 que viene por defecto. Esta técnica se conoce con el nombre de *security through obscurity*, y no hace el sistema inexpugnable, pero reduce drásticamente el ruido de ataques automáticos.

El comando para ver el estado del servicio de SSH es: `sudo systemctl status ssh`.

La máquina virtual utiliza una conexión NAT (Network Address Translation) en VirtualBox. NAT hace que VirtualBox actúe como un router invisible entre la VM y la red física: la VM tiene su propia IP privada dentro de una red interna, y cuando sale a internet, VirtualBox traduce ese tráfico para que parezca originarse en el propio host. Como consecuencia, la VM queda aislada y no es accesible desde fuera por defecto, por lo que es necesario configurar manualmente una regla de redirección de puertos (*port forwarding*) para poder conectarse a ella por SSH:


- Host Port: 4241
- Guest Port: 4242

Esto significa que las conexiones que llegan al puerto 4241 del ordenador anfitrión se redirigen al puerto 4242 de la máquina virtual, donde está escuchando el servicio SSH.

Por tanto, para conectarme desde el host a la máquina virtual utilizo:

```
ssh amarlasc@localhost -p 4241
```

El puerto 4242 es el puerto de SSH dentro de Debian, mientras que 4241 es el puerto utilizado en el ordenador anfitrión para acceder a él.

Esquema conceptual:

```
Host 4241 → VirtualBox NAT → Guest 4242 → Servicio SSH
```

La configuración de SSH se encuentra en `/etc/ssh/sshd_config` y `/etc/ssh/ssh_config`.

Para comprobar el estado: `sudo service ssh status`.

## Monitoring script

El proyecto incluye un script `monitoring.sh` encargado de mostrar información sobre el estado del sistema. Este script se ejecuta cada 10 minutos. La configuración del mismo se realiza a través del comando `sudo crontab -u root -e`.

### ¿Qué es cron?

Cron es un demonio (servicio en segundo plano) de Linux que permite programar la ejecución automática de tareas (llamadas *cron jobs*) a intervalos regulares, sin necesidad de intervención manual. Cada usuario puede tener su propia tabla de tareas programadas (*crontab*), y root puede programar tareas que se ejecuten con privilegios de administrador.

### Configuración del cron job

La línea añadida al crontab de en este caso es la siguiente:

AÑADELA MENDRUGAAAAAA


Esto se lee así: minuto (`*/10` = cada 10 minutos), hora (`*` = cualquiera), día del mes (`*`), mes (`*`) y día de la semana (`*` = todos). El resultado del script se envía a `wall` para que se muestre a todos los usuarios con sesión abierta en la terminal.

El script se encuentra en `CAMBIALO`, con permisos de ejecución (`COMPRUEBALO` o similar) y propietario `CHECKEALO`.

### Comprobación dinámica

Para verificar que el script se ejecuta correctamente sin esperar 10 minutos, se puede editar temporalmente el crontab (`sudo crontab -u root -e`) y cambiar `*/10` por `*/1`, de forma que se ejecute cada minuto. Una vez comprobado su correcto funcionamiento, se revierte el cambio a `*/10 * * * *` — todo esto se hace modificando únicamente la entrada del crontab, sin tocar el contenido del script `monitoring.sh`.

Tras reiniciar el sistema, se puede comprobar que el script sigue existiendo en la misma ruta, que sus permisos no han cambiado y que su contenido no ha sido modificado, por ejemplo con:

```
ls -la LA RUTA!!!!
sudo crontab -u root -l
```

El script utiliza diferentes herramientas de Linux para obtener esta información y mostrarla periódicamente al usuario. También se puede ver ejecutando el comando `sudo sh monitoring.sh`.

Este caso, los datos monitorizados que se muestran son los siguientes:

- Arquitectura del sistema.
- Número de CPUs físicas y virtuales.
- Memoria RAM utilizada.
- Uso de disco.
- Porcentaje de uso de CPU.
- Último reinicio.
- Estado de LVM.
- Número de conexiones TCP.
- Número de usuarios conectados.
- Dirección IP y MAC.
- Número de comandos ejecutados mediante sudo.

## Instrucciones

1. Abrir la terminal en el ordenador local y escribir `virtualbox` o buscar en las aplicaciones Oracle VM VirtualBox.
2. Introducir la contraseña de partición de disco.
3. Luego introducir el login y la contraseña.
4. ¡Ya estamos dentro!

## Resources

- [Debian Documentation](https://www.debian.org/doc/)
- [Debian Wiki](https://wiki.debian.org/)
- [GNU/Linux man pages](https://man7.org/linux/man-pages/)
- [UFW Documentation](https://help.ubuntu.com/community/UFW)
- [OpenSSH Documentation](https://www.openssh.com/manual.html)
