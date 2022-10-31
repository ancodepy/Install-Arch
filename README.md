# ![Logo-Arch](assets/logo.png) Guia de Instalación

## ![Teclado-logo](assets/keyboard.png) Configurar teclado

```sh
# Teclado en español
loadkeys es

# Teclado en español latinoamericano
loadkeys la-latin1

# Teclado en ingles
loadkeys es # Bien por defecto en iso live
```

---

## Configurar internet
Comprobar si hay internet

```sh
ping -c 1 google.com
```

---

## ![wifi-logo](assets/wifi.png) Configurar wifi
En el caso que no presenten conexión por cable. Sigan los siguiente:
```sh
# Despliega los adaptadores wifi con información
ip link
# Levantar el adapatador | wlan0 es el adaptador que se encontro con el comando anterior
ip link set wlan0 up
# Escanear -> Esto detectara las redes cercanas con su nombre ESSID y mas información
iwlist wlan0 scan | more
```

### Conexion

- Esta es en el caso que no haya un clave, es decir, la red es publica
```sh
iwconfig wlan0 essid name
# Example : iwconfig wlan0 essid linux
```

- Cuando la seguridad de la red es **wep**
```sh
iwconfig wlan0 essid name key s:clave
# Ejemplo: iwconfig wlan0 essid familia key s:miclave
```

- Cuando la seguridad de la red es **wpa/wpa2**
```sh
wpa_passphrase name password > /etc/wifi
# Ejemplo: wpa_passphrase familia miclave > /etc/wifi
cat /etc/wifi # Muestra la configuracion de nuestra red wifi guardada
wpa_supplicant -B -i wlan0 -D wext -c /etc/wifi # Conexion
dhclient
```

---

## ![disco-logo](assets/disco-duro.png) Particionamiento

Listar discos: `lsblk`

Comprobar si nuestro equipo maneja *UEFI* o *BIOS* con `ls /sys/firmware/efi/efivars`

- Si el comando da una salida sin ningun error es *UEFI*
- Si da un error es *BIOS*

<br>

### Crear particines - UEFI

Abrir la consola interactiva para crear la tabla de particiones: *cfdisk*

- Selecciona gpt como tipo de etiqueta

> Si solo tendras Arch Linux en tu maquina crearas el root, boot, swap
>
> Si instalas arch dual-boot crearas el root, swap
>
> La particion home no es necesaria pero si quieres crearla adelante

<br>

- Particion boot
```txt
Partition size: 512M
Type: EFI System
```
- Particion root
```txt
Partition size: 15-20G
Type: Linux filesystem
```
Cambiara según tus requerimientos
- Particion swap
```txt
Partitition size: 2:3:4..GB
Type: Linux swap
```
- Particion home
```txt
Partition size: 80GB
Type: Linux filesystem
```

Ya creadas las particiones no se les olvide escribirlas. Opcion: `write`

<br>

### Formatear particiones UEFI

Con `lsblk` podras ver tus particones y guiandote

- boot
```sh
mkfs.vfat -F32 part_boot # Si tiras dual-boot no la formateas
# Esto es muy importante ya que te puedes tirar el arranque de windows
```
- root y home
```sh
mkfs.ext4 part_home
mkfs.ext4 part_root
```
- swap
```sh
mkswap part_swap # Formateas el swap
swapon # Activas el swap
```

<br>

### Montar particiones - UEFI

Solamente montaras las particiones boot, root y home

- root
```sh
mount part_root /mnt
```
- boot
```sh
mkdir /mnt/boot
mkdir /mnt/boot/efi

mount part_boot /mnt/boot/efi
```
- home
```sh
mkdir /mnt/home

mount /dev/sda3 /mnt/home
```
<br>

### Crear particiones BIOS
Aqui solamente cambiara que al entrar al `csdisk` escogeras *dos* como type label y el formate de las particiones home, root y boot van a ser `83 Linux`. En boot escogeras que sea booteable y tipo swap sera `83 Linux swap / Solarios`

Por ulitmo escribir las particiones

### Formatear particiones BIOS
El procedimiento va ser igual no cambiara en nada

### Montar particiones
Cambia en el modelo de montar el boot
```
mkdir /mnt/boot

mount part_boot /mnt/boot
```
El resto de particiones va ser igual

---

## Instalacion de paquetes base

```sh
pacstrap /mnt linux linux-firmware base nano grub networkmanager dhcpcd efibootmgr # Este ultimo solamente si tienes uefi
```

## Instalar paquetes para wifi

```sh
pacstrap /mnt netctl wpa_supplicant dialog base-devel
```

---

## Generar fstab

```sh
genfstab /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

---

## ![settings-logo](assets/settings.png) Configuracion

Ya tenemos instalado arch linux podemos acceder a el con: `arch-chroot /mnt`


### ![ordenador-logo](assets/ordenador-personal.png) Nombre del equipo

- Nombre del equipo: `echo ArchLinux > /etc/hostname`
- Abrimos el archivo `/etc/hosts`
- Dentro de este archivo agregamos
```
127.0.0.1        localhost.localdomain        localhost
::1              localhost.localdomain        localhost
127.0.1.1        hostname.localdomain	      hostname
```

### ![clock-logo](assets/tiempo.png) Zona horaria

configurar: `ln -sf /usr/share/zoneinfo/America/Bogota /etc/localtime`


### ![idioma-logo](assets/idiomas.png) Idioma

- Abrir el archivo locale.gen: `nano /etc/locale.gen`
- Descomentar el idioma en mi caso es:  `#es_MX.UTF-8 UTF-8` Guardar y salir
- Para que nuestro OS sepa nuestro idioma: `locale-gen`

### ![teclado-logo](assets/keyboard.png) Distribucion de teclado

`echo KEYMAP=la-latin1 > /etc/vconsole.conf`

`echo LANG=es_MX.UTF-8 > /etc/locale.conf`

---

## GRUB

Descomentar en `/etc/default/grub`

```
#GRUB_DISABLE_OS_PROBER=false
```

### UEFI
```sh
grub-install --efi-directory=/boot/efi --bootloader -id='Arch Linux' --target=x86_64-efi
```
### BIOS
```sh
grub-install /dev/sda # o vda
```

## Configurar GRUB
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

## Creacion de usuario

- Asignarle una contraseña al usuario root: `passwd`
- Crear usuario: `useradd -m username`
- Asignarle una contraseña al usuario: `passwd username`

### Asignarle privilegios de super usuario al usuario normal

Asignar al usuario al grupo wheel e instalar el paquete sudo:

- `$ usermod -aG wheel username` Agregar el usuario al grupo wheel

- `$ groups username` Ver en que grupo se encuentra

- `$ pacman -S sudo` Instalar el paquete sudo

- Abrir el archivo sudoers: `nano /etc/sudoers`

Descomentar la siguiente linea:
```
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL:ALL) ALL
```

---

## Reiniciar

>Antes de reiniciar nos salimos del chroot con `exit` y desmontamos las particiones `umount -R /mnt`

Listo, reinciamos: `reboot now`

---

## Activando y configurando internet

### ![cable-logo](assets/cable.png) Red cableada

Para comprobar si nuestra red funciona: `ping 8.8.8.8` en el caso que nuestra red sea inaccesible. Ejecutamos

Iniciando servicio de red: `systemctl start NetworkManager.service` 

Actviar servicio de red: `systemctl enable NetworkManager.service`

### ![wifi-logo](assets/wifi.png) Red wifi

Iniciando servicio wpa: `systemctl start wpa_supplicant.service`

activar servicio wpa: `systemctl enable wpa_supplicant.service`

activar servicio dhcpcd: `systemctl enable dhcpcd.service`

```bash
ip link # Mostrar adaptadores wifi
ip link set adaptador up # Ejemplo: ip link set wlp0s6u2 up
nmcli dev wifi connect SSID password clave # Ejemplo: nmcli dev wifi connect familia password miclave
```

---

## Habilitar los AUR Repositorios

Instalar git: `sudo pacman -S git`

Bajarnos el repositorio con git

```
mkdir -p Documents/repos
cd !$
git clone https://aur.archlinux.org/paru-bin.git
cd paru-bin
```

Instalarlo

```
makepkg -si
```

Si muestra algun error de: `ERROR: You do not have write permission for the directory $BUILDDIR,` simplemente ejecuten este comando

```bash
sudo chown -R username /home/username/paru-bin # Remplace username por su usuario
```

Vuelva a ejecutar el comando de `makepkg -si`

--- 

## Drivers graficos

```
lspci | grep VGA
```

Instalar un driver generico: `pacman -S xf86-video-vesa`

Instalar driver para nvidia: `pacman -S xf86-video-nouveau`

Instalar driver para amd: `pacman -S xf86-video-ati`

Instalar driver para intel: `pacman -S xf86-video-intel intel-ucode`

---

## Instalar xorg y mesa

```
pacman -S xorg xorg-server xorg-xinit mesa mesa-demos
```

Ya terminamos de instalar arch linux. El siguiente paso es instalar y configurar el entorno de nuestra preferencia.

- [awesome wm](awesome.md)
- [gnome](gnome.md)

---

# Configuraciones opcionales

## Configurar pacman

Abrir el archivo `/etc/pacman.conf` y deshabilitar las siguientes opciones

```
#color
#ParallelDownloads = 5
```

## Instalar vmware-tools

Para correguir las proporciones de pantalla instalamos esto y ya estaria solucionado.

```bash
pacman -S gtkmm
pacman -S open-vm-tools
pacman -S xf86-video-vmware xf86-input-vmmouse
systemctl enable vmtoolsd # Habilitar un daemon
```

## Instalar BlackArch

En el caso que seas un hacker y uses herramientas de hacking el repositorio de blackarch es mas que suficiente para que te bajes todas tus herramientas de hacking.

```bash
cd blackarch # Creas un directorio para bajarte el archivo
curl -O https://blackarch.org/strap.sh # Te bajas el archivo de la web
chmod +x strap.sh # Le das permisos de ejecución al archivo
./strap.sh # Importante ejecutar el archivo como usuario root
```

Con `pacman -Sgg | grep blackarch` podemos ver todas los paquetes que puede contener este repo