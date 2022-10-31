# ![awesome-logo](assets/awesome.svg) Creacion del entorno con Awesome

## Gestor de inicio de sesion
Podemos utilizar muchos pero en este caso usare lightdm

```sh
sudo pacman -S lightdm lightdm-gtk-greeter
```
Editamos en el archivo `/etc/lightdm/lightdm.conf`
```sh
#greeter-session=example-gtk-gnome
greeter-session=lightdm-gtk-greeter
```

Activamos el servicio `systemctl enable lightdm.service`

# SETUP

## Dependencias

```shell
paru -S awesome-git picom-git alacritty rofi todo-bin acpi acpid \
wireless_tools jq inotify-tools polkit-gnome xdotool xclip maim \
brightnessctl alsa-utils alsa-tools pulseaudio lm_sensors \
mpd mpc mpdris2 ncmpcpp playerctl --needed
```

## Services

```sh
# For automatically launching mpd on login
systemctl --user enable mpd.service
systemctl --user start mpd.service
# For charger plug/unplug events (if you have a battery)
sudo systemctl enable acpid.service
sudo systemctl start acpid.service
```

## Instalación de algunas fuentes

### Fuentes Iosevka

```sh
sudo pacman -S wget
cd /usr/share/fonts
sudo su
pacman -S p7zip
# Fonts Iosevka
wget https://fontlot.com/downfile/5baeb08d06494fc84dbe36210f6f0ad5.105610
mv 5baeb08d06494fc84dbe36210f6f0ad5.105610 comprimido.zip
unzip comprimido.zip
rm comprimido.zip
find . | grep "\.ttf$" while read line; do cp $line .; done
rm -r iosevka-2.2.1/
rm -r iosevka-slab-2.2.1/
```

### Fuentes icomoon

En el navegador colocar o simplemente abrir este link [https://dropbox.com/s/hrkub2yo9iapljz/icomoon.zip?dl=0](https://dropbox.com/s/hrkub2yo9iapljz/icomoon.zip?dl=0) y te bajas el archivo .zip

```shell
mv /home/username/Downloads/icomoon.zip .
unzip icomoon.zip
mv icomoon/*.ttf .
rm -rf icomoon
```

### Fuentes adicionales

```shell
# Fuentes adicionales
paru -S nerd-fonts-jetbrains-mono ttf-font-awesome ttf-font-awesome-4 ttf-material-design-icons
```

En este punto cambian un poco las cosas. En vez de clonarse el repo por medio de git clone vamos a bajarnos este archivo zip [dotfiles](https://github.com/rxyhn/yoru/tree/c1e2eef2baa91aebd37324891cb282666beae04f), lo descomprimen y lo mueven al directorio tal cual como se muestra en el setup de github

> Importante: se instalan los paquetes del commit ya que el repo actual trae cosas innecesarias.
> Bueno en fin seguimos los pasos de s4vitar y cuando lleguen al punto 37:54 siguen esta guia y luego siguen el tutorial normal.
Espero que les haya funcionado :)



