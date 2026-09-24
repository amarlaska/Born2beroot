*Este proyecto ha sido creado como parte del currículo de 42 por amarlasc*

# Born2beroot

## Descripción

**Born2beroot** es un proyecto de administración de sistemas cuyo objetivo es introducir los conceptos fundamentales de virtualización, sistemas operativos, seguridad, usuarios, permisos, redes y administración de servidores.

El proyecto consiste en crear y configurar una máquina virtual utilizando **VirtualBox** como hypervisor y **Debian** como sistema operativo. El hipervisor es una capa de software que permite crear y ejecutar varias máquinas virtuales en una sola computadora. 

Durante el proyecto se configura un sistema seguro y funcional, trabajando principalmente desde la terminal y aprendiendo a administrar el sistema mediante herramientas y servicios de Linux.


### Virtualización

Una **máquina virtual** permite ejecutar un sistema operativo dentro de otro sistema operativo.

**VirtualBox** actúa como hypervisor y proporciona a la máquina virtual recursos del ordenador físico, como CPU, memoria RAM, almacenamiento y red.

**Debian** es la distribución GNU/Linux utilizada como sistema operativo guest. La razón para elegirlo es que ofrece una experiencia sencilla y accesible para usuarios, lo que facilita el aprendizaje y la administración del sistema. Además, Debian cuenta con una amplia comunidad, una gran cantidad de documentación y un extenso repositorio de paquetes, lo que facilita encontrar información y soluciones durante el desarrollo del proyecto.

Para comprobar el sistema operativo utilizado, el comando es `hostnamectl` o `cat /etc/os-release`.

**Rocky Linux** es una alternativa muy utilizada en entornos empresariales y es compatible con el ecosistema de Red Hat, para este proyecto he preferido Debian por su sencillez y por ser una distribución especialmente adecuada para familiarizarme con la administración de sistemas Linux.

### Hostname

El **hostname** es la etiqueta única que se le asigna a un dispositivo para identificarlo dentro de una red de computadoras.

- `hostname`: comando para visualizar el hostname.
- `hostnamectl`: comando para visualizar el hostname y otra inforamción del sistema. Si se combina con otros comandos permite hacer cambios en la configuración del del sistema. Por ejemplo, para cambiar el hostname: 
`sudo hostnamectl set-hostname nuevo_nombre`

Para poder visualizar el cambio se puede bien con el comando `sudo systemctl restart systemd-hostnamed` sin reiniciar el el equipo o reiniciando el equipo.

### Partición del disco

La partición de un disco consiste en dividir un disco físico o virtual en diferentes partes independientes llamadas particiones. Cada partición puede tener una función diferente, por ejemplo, almacenar el sistema operativo, los archivos personales o utilizarse como espacio de intercambio.

- `sda`: es el disco virtual completo de 12 GB.
- `sda1`: contiene `/boot`, necesario para arrancar Debian.
- `sda2`: espacio técnico reservado para el arranque mediante GRUB que es el gestor de arranque de Linux. El flujo del arranque es el siguiente, primero, la **BIOS** busca algo que arrancar, encuentra a **GRUB**, **GRUB** localiza el sistema Linux y sus archivos de arranque. **GRUB** inicia **Debian**. Como el sistema está cifrado con LUKS, después aparece la petición de contraseña para desbloquear el disco. En definitiva, GRUB es un gestor de arranque que se encarga de iniciar el sistema operativo y permite seleccionar qué sistema operativo o kernel queremos arrancar.
- `sda5`: contiene la mayor parte del espacio del disco y sirve como base para el cifrado.
- `sda5_crypt`: es sda5 cifrada mediante **LUKS**; necesitas la contraseña para desbloquearla.
- `root`: contiene el sistema operativo, programas y configuraciones.
- `swap`: espacio del disco utilizado como memoria de intercambio cuando la RAM no es suficiente.
- `home`: contiene los archivos personales de los usuarios, como `/home/amarlasc`.


```
sda                      		   12G
├─ sda1                 		  791M
├─ sda2                   		    1K
└─ sda5                			 11.2G
   └─ sda5_crypt       			 11.2G
      ├─ amarlasc42--vg-root	  7.1G
      ├─ amarlasc42--vg-swap      624M
      └─ amarlasc42--vg-home      3.5G
```

### Usuarios y permisos

El sistema utiliza usuarios y grupos para controlar el acceso a recursos. Los principales comandos utilizados son:
- `adduser`: Crea un nuevo usuario en el sistema, y también para añadir un usuario a un grupo.
- `usermod`: Modifica la configuración de un usuario existente.
- `addgroup`: Crea un nuevo grupo.
- `groups`: Muestra los grupos a los que pertenece un usuario.
- `id`: Muestra la información de un usuario, como su UID, GID y grupos.
- `chmod`: Modifica los permisos de acceso de archivos y directorios.
- `chown`: Cambia el propietario y/o grupo de un archivo o directorio.
- `getent group nombre_grupo`: Verrificar los usuarios pertenecientes a un grupo.
- `sudo passwd nombre_usuario`: Establecer la contraseña de un usuario.
- `sudo passwd -S nombre_usuario`: Resumen del estado de la contraseña.
- `sudo chage -l nombre_usuario`: Información detallada sobre la caducidad.
- `sudo userdel nombre_usuario`: Borra un usuario.
- `getent passwd`: Muestra todos los usuarios incluidos en el sistema.
- `sudo usermod -aG nombre_grupo nombre_usuario`: Añade un usuario a un grupo.
- `sudo groupdel nombre_grupo`: Borra un grupo.

### Sudo

`sudo` permite ejecutar determinados comandos con privilegios de administrador. Hay que aclarar la diferencia entre `su` y `sudo`. Su cambia de sesión al usurio `root`pidiendo su contraseña, mientras que `sudo` ejecuta una sola orden temporalmente usando la contraseña del usuario actual. Para comprobar la versión de `sudo` instalada el comando es `sudo --version`.

Si queremos añadir un usuario al grupo `sudo`el comando sería `sudo usermod -aG sudo amarlasc`.


Su configuración se puede realizar tanto mediante `sudo visudo`o `sudo nano /etc/sudoers.d/sudo_config`
- Número máximo de intentos de autenticación.
- Mensajes personalizados.
- Registro de comandos ejecutados.
- `secure_path`
- Tiempo de validez de la autenticación.

Para acceder a la carpeta donde se encuentra `sudo_config` el comando sería `sudo ls -la /var/log/sudo`. A parte de encontrar el archivo `sudo_config`, está el archivo `seq` que utiliza sudo para mantener una secuencia de los registros de las sesiones sudo.

El parámetro `secure_path` limita los directorios donde sudo puede buscar ejecutables:

`/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`

### Política de contraseñas

La política de contraseñas se puede configurar en los documentos `/etc/login.defs` y `/etc/pam.d/common-password`. Para poder configurar este segundo hay que instalar la librería libpam-pwquality.

En `login.defs` se han modificado los siguientes: 
- **PASS_MAX_DAYS:** Máximo de día antes de que caduque la contraseña.
- **PASS_MIN_DAYS:** Mínimo de días antes de que se pueda cambiar la contraseña.
- **PASS_WARN_AGE:** Número de días antes de que caduque la contraseña para mostrar la advertencia.

En `common-password`se han modificado: 
 - `minlen`: establece la longitud mínima que debe tener la contraseña.
- `ucredit`: controla cuántas letras mayúsculas debe contener la contraseña (-1 = al menos 1).
dcredit: controla cuántos números debe contener (-1 = al menos 1).
- `lcredit`: controla cuántas letras minúsculas debe contener (-1 = al menos 1).
- `maxrepeat`: limita cuántas veces seguidas se puede repetir el mismo carácter.
- `reject_username`: impide utilizar el nombre de usuario dentro de la contraseña.
- `difok`: establece cuántos caracteres deben ser diferentes respecto a la contraseña anterior.
- `enforce_for_root`: hace que estas reglas de contraseña también se apliquen al usuario root.

### Firewall

Se utiliza **UFW (Uncomplicated Firewall)** para controlar las conexiones de red. El firewall permite definir qué conexiones entrantes están permitidas y cuáles deben bloquearse. Se caracteriza por utilizar comandos sencillos.

**Comando útiles**

- `sudo ufw allow <puerto>`: Añadir un puerto.
- `sudo ufw status numbered`: Listar las reglas con su número correspondiente.
- `sudo ufw delete <número>`: Elimina la regla indicando el número.
- `sudo ufw delte allow <puerto>`: Borrar el puerto escribiendo la regla completa.
- `sudo ufw status`: Para comprobar el estado.

### SSH

**SSH (Secure Shell)** permite acceder romotamente a la máquina meidanta una conexión cifrada. En este proyecto se configura el servicio SSH para que escuche en el puerto `4242` dentro de la máquina virtual (guest).

El comando para ver el estado del servicio de SSH es:
`sudo systemctl status ssh`

Como la máquina virtual utiliza una conexión NAT en VirtualBox, he configurado una regla de redirección de puertos:

- **Host Port:** `4241`
- **Guest Port:** `4242`

Esto significa que las conexiones que llegan al puerto `4241` del ordenador anfitrión se redirigen al puerto 4242 de la máquina virtual, donde está escuchando el servicio SSH.

Por tanto, para conectarme desde el host a la máquina virtual utilizo:

`ssh amarlasc@localhost -p 4241`

El puerto `4242` es el puerto de SSH dentro de Debian, mientras que `4241` es el puerto utilizado en el ordenador anfitrión para acceder a él.

**Esquema conceptual**
```
Host 4241 → VirtualBox NAT → Guest 4242 → Servicio SSH
```

La configuración de SSH se encuentra en:
`/etc/ssh/sshd_config` y `/etc/ssh/ssh_config`.

Para comprobar el estado:
`sudo service ssh status`

### Monitoring script

El proyecto incluye un script `monitoring.sh` encargado de mostrar información sobre el estado del sistema. Este script se ejecuta cada 10 minutos. La configuración del mismo se realiza a través del comando `sudo crontab -u root -e`.

El objetivo del script es monitorizar periódicamente los principales recursos y características del sistema, permitiendo comprobar de forma rápida su estado y detectar posibles problemas de funcionamiento o consumo de recursos. Entre los datos monitorizados se encuentran:

* Arquitectura del sistema.
* Número de CPUs físicas y virtuales.
* Memoria RAM utilizada.
* Uso de disco.
* Porcentaje de uso de CPU.
* Último reinicio.
* Estado de LVM.
* Número de conexiones TCP.
* Número de usuarios conectados.
* Dirección IP y MAC.
* Número de comandos ejecutados mediante `sudo`.

El script utiliza diferentes herramientas de Linux para obtener esta información y mostrarla periódicamente al usuario. También se puede ver ejecutando el comando `sudo sh monitoring.sh`.

## Instrucciones

- Abrir la terminal en el ordenador local y escribir `virtualbox`o buscar en las aplicaciones `Oracle VM VirtualBox`.
- Introducir la contraseña de partición de disco. 
- Luego introducir el login y la contraseña. 
- ¡Ya estamos dentro!

## Resources

[Debian Documentation](https://www.debian.org/doc/)

[Debian Wiki](https://www.debian.org/doc/)

[GNU/Linux man pages](https://man7.org/linux/man-pages/)

[UFW Documentation](https://help.ubuntu.com/community/UFW)

[OpenSSH Documentation](https://www.openssh.org/manual.html)
