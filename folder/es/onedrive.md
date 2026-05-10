# Tener onedrive 
Tener onedrive corriendo en segundo plano con descarga automatica.

## Paquete necesario
App de la terminal para conectar con onedrive
``` 
sudo pacman -S --needed rclone 
```
Ya descargado ejecutarlo primero crear la carpeta donde se va alojar los archivos
```
mkdir ~/OneDrive
mkdir -p ~/.cache/rclone
``` 
Y ya con ello ejecutar el rclone
``` 
rclone config
``` 

### Paso importante
Hay 2 formas de tener los archivos, si online o de forma local, ud como usuario elige una de las 2 y para tenerlo en segundo plano va ser diferente el funcionamiento.

Ver el funcionamiento online
```
rclone mount OneDrive: ~/OneDrive --vfs-cache-mode full --vfs-cache-max-age 48h --vfs-cache-max-size 4G --dir-cache-time 12h --use-mmap --umask 022 --progress
```

Sincronizar archivos y guardarlo en el ordenador
``` 
rclone bisync OneDrive: ~/OneDrive --resync --progress --exclude "Personal Vault/**" --exclude "PersonalVault/**" --exclude "*{.~,tmp,bak}*"
```

## Tenerlo en segundo plano

### Tener los archivos en la nube
Crear el archivo en ``.config/systemd/user/``

Archivo del servicio ``rclone-onedrive.service``

```
[Unit]
Description=Rclone OneDrive Online Mount
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount OneDrive: %h/OneDrive \
    --config=%h/.config/rclone/rclone.conf \
    --vfs-cache-mode full \
    --vfs-cache-max-age 48h \
    --vfs-cache-max-size 4G \
    --dir-cache-time 12h \
    --use-mmap \
    --umask 022 \
    --log-level INFO
ExecStop=/bin/fusermount -u %h/OneDrive
Restart=on-failure

[Install]
WantedBy=default.target
```
Por último, activar los servicios:
```
systemctl --user daemon-reload
systemctl --user enable --now rclone-onedrive.service
```

Ver si esta corriendo el servicio
``` 
systemctl --user status rclone-onedrive.service
```

### Tener los archivos localmente y actualización automatica
Crear los archivo en ``.config/systemd/user/``

Archivo del servicio ``rclone-onedrive.service``
```
[Unit]
Description=Rclone OneDrive Bisync Service
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/usr/bin/rclone bisync OneDrive: %h/OneDrive \
    --config=%h/.config/rclone/rclone.conf \
    --compare size,modtime \
    --slow-hash-sync-only \
    --resilient \
    --exclude "Personal Vault/**" \
    --exclude "PersonalVault/**" \
    --log-file=%h/.cache/rclone/bisync.log \
    --log-level INFO
```

Archivo que va actualizar el serivico ``rclone-onedrive.timer``
```
[Unit]
Description=Timer para sincronizar OneDrive cada 5 minutos

[Timer]
OnBootSec=2min
OnUnitActiveSec=5min
Unit=rclone-onedrive.service

[Install]
WantedBy=timers.target
```

Por último, activar los servicios:
```
systemctl --user daemon-reload
systemctl --user enable --now rclone-onedrive.timer
```

Verificar el funcionamiento
```
systemctl --user list-timers
```
Si los archivos se movieron
```
tail -n 20 ~/.cache/rclone/bisync.log
```

## Fallas onedrive
si tienes fallas utilice 
```
rclone bisync OneDrive: /home/mapachuelo/OneDrive/ --resync --resync-mode path2 --exclude "Personal Vault/**" --exclude "Almacén personal/**" -v
```
Sincroniza con la nube y local para no tener conflictos.
Reiniciar el servicio para aplicar los cambios 
```
systemctl --user enable --now rclone-onedrive.timer
```