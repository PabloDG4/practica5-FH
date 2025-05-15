# Tarea 2.3: Montado de un sistema de ficheros con `chroot`

## Descripción

En esta práctica se utilizará una máquina virtual con Debian como sistema base. Se usará una imagen `.iso` de Kali Linux como sistema Live para recuperar el usuario y la contraseña del sistema Debian mediante el comando `chroot`.

---

## Pasos de la tarea

### 1. Instalación de la máquina base

- Realiza una instalación desde cero de una máquina virtual con sistema operativo **Debian**.
- Crea un usuario cuyo **nombre sea tu nombre personal**.

### 2. Configuración en VirtualBox

- En los **ajustes de VirtualBox**, en la parte de **almacenamiento**, añade la imagen de otro sistema operativo: en este caso, **Kali Linux**.
- Asegúrate de que el **orden de arranque** tenga primero la unidad **óptica** y luego el **disco duro**.

### 3. Arrancar la máquina con Kali Linux

- Inicia la máquina virtual.
- Kali Linux se cargará como sistema operativo desde la imagen `.iso`.

---

### 4. Operaciones en la terminal de Kali Linux

A continuación, se realizan los pasos necesarios para preparar el entorno `chroot` desde Kali:

#### 4.1 Cambiar el teclado a castellano

```bash
loadkeys es
```

#### 4.2 Acceder a la consola de root

```bash
sudo su
```

> Asegúrate de tener permisos de sudo configurados. Puedes editar `/etc/sudoers` con `visudo` si es necesario.

#### 4.3 Mostrar los sistemas de ficheros montados

```bash
df -h
```

#### 4.4 Listar la tabla de particiones del disco `/dev/sda`

```bash
fdisk -l /dev/sda
```

#### 4.5 Crear el directorio de montaje

```bash
mkdir /mnt/recuperar
```

#### 4.6 Montar la partición principal de Debian

```bash
mount -t auto /dev/sda1 /mnt/recuperar
```

Verifica el sistema de ficheros con:

```bash
lsblk -f
```

#### 4.7 Montar el directorio `/dev` dentro del entorno recuperado

```bash
mount --bind /dev /mnt/recuperar/dev
```

#### 4.8 Montar `/proc` dentro del entorno recuperado

```bash
mount --bind /proc /mnt/recuperar/proc
```

#### 4.9 Montar `/sys` dentro del entorno recuperado

```bash
mount --bind /sys /mnt/recuperar/sys
```

#### 4.10 Crear la jaula con `chroot`

```bash
chroot /mnt/recuperar
```

> Ahora estás dentro del sistema Debian instalado, no en el sistema live de Kali. Todo lo que hagas aquí afecta directamente al sistema base.

---

### 5. Finalizar y desmontar

Una vez terminadas las acciones necesarias dentro del entorno `chroot` (como recuperar contraseñas o editar configuraciones), se debe desmontar todo correctamente.

#### 5.1 Salir del entorno chroot

```bash
exit
```

#### 5.2 Desmontar todos los sistemas de montaje

```bash
umount /mnt/recuperar/dev
umount /mnt/recuperar/proc
umount /mnt/recuperar/sys
umount /mnt/recuperar
```

---

### 6. Apagar y restaurar la VM

- Apaga la máquina virtual desde el sistema Kali.
- En la configuración de VirtualBox, elimina la imagen `.iso` de Kali Linux del almacenamiento.
- Inicia nuevamente el sistema Debian desde el disco duro.
- Comprueba que puedes acceder con el usuario recuperado o modificado.

---

## Observaciones

- Esta técnica es útil para:
  - Recuperar usuarios o contraseñas.
  - Reparar configuraciones del sistema.
  - Instalar o eliminar paquetes en sistemas que no inician correctamente.
- Asegúrate de trabajar con permisos de administrador (`root`) para evitar errores de acceso.
- `chroot` crea un entorno aislado, ideal para manipular un sistema desde otro Linux sin necesidad de arrancarlo directamente.
