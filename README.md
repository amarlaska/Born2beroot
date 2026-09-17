*Este proyecto ha sido creado como parte del currículo de 42 por amarlasc*

# Born2beroot

## Descripción

**Born2beroot** es un proyecto de administración de sistemas cuyo objetivo es introducir los conceptos fundamentales de virtualización, sistemas operativos, seguridad, usuarios, permisos, redes y administración de servidores.

El proyecto consiste en crear y configurar una máquina virtual utilizando **VirtualBox** como hypervisor y **Debian** como sistema operativo.

Durante el proyecto se configura un sistema seguro y funcional, trabajando principalmente desde la terminal y aprendiendo a administrar el sistema mediante herramientas y servicios de Linux.

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

### Virtualización

Una **máquina virtual** permite ejecutar un sistema operativo dentro de otro sistema operativo.

**VirtualBox** actúa como hypervisor y proporciona a la máquina virtual recursos del ordenador físico, como CPU, memoria RAM, almacenamiento y red.

**Debian** es la distribución GNU/Linux utilizada como sistema operativo guest. La razón para elegirlo es que ofrece una experiencia sencilla y accesible para usuarios, lo que facilita el aprendizaje y la administración del sistema. Además, Debian cuenta con una amplia comunidad, una gran cantidad de documentación y un extenso repositorio de paquetes, lo que facilita encontrar información y soluciones durante el desarrollo del proyecto.

Rocky Linux es una alternativa muy utilizada en entornos empresariales y es compatible con el ecosistema de Red Hat, para este proyecto he preferido Debian por su sencillez y por ser una distribución especialmente adecuada para familiarizarme con la administración de sistemas Linux.

### Usuarios y permisos

El sistema utiliza usuarios y grupos para controlar el acceso a recursos. Los principales comandos utilizados son:
- `useradd` → Crea un nuevo usuario en el sistema.
- `usermod` → Modifica la configuración de un usuario existente.
- `groupadd` → Crea un nuevo grupo.
- `groups`→ Muestra los grupos a los que pertenece un usuario.
- `id` → Muestra la información de un usuario, como su UID, GID y grupos.
- `chmod` → Modifica los permisos de acceso de archivos y directorios.
- `chown` → Cambia el propietario y/o grupo de un archivo o directorio.

### Sudo

`sudo` permite ejecutar determinados comandos con privilegios de administrador.

Su configuración se puede realizar tanto mediante `sudo visudo`o `sudo nano /etc/sudoers.d/


## Instrucciones

El comando para ejecutar el monitoring script es:

`sudo bash monitoring.sh`

El script muestra periódicamente información sobre el sistema para poder comprobar su estado.

## Resources

[Debian Documentation](https://www.debian.org/doc/)

[Debian Wiki](https://www.debian.org/doc/)

[GNU/Linux man pages](https://man7.org/linux/man-pages/)

[UFW Documentation](https://help.ubuntu.com/community/UFW)

[OpenSSH Documentation](https://www.openssh.org/manual.html)
