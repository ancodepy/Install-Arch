# Instalación

```
sudo pacman -S gnome gnome-extra
```

# Instalar gestor de inicio de sesion

```
sudo pacman -S gdm
```

## Habilitar gdm

`$ systemctl enable gdm.service`

# Instalaciones previas

- Instalar navegador
- Instalar firewall: `sudo pacman -S ufw`
    
    Inciarlo: `systemctl start ufw.service`
    
    Habilitarlo: `systemctl enable ufw.service`
    
    Verificar: `sudo ufw status` 
    

# Personalizacion

Gnome look: [https://www.gnome-look.org/browse/](https://www.gnome-look.org/browse/)

Instalar `gnome-tweak-tool`

Ruta de temas: `/usr/share/themes`

Ruta de iconos: `/usr/share/icons`