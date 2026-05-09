# Guía de maquina virtual
Instarlar y tener configurado para crear maquinas virtuales en arch linux

## Compatibilidad
Ver la compatibilidad si el procesador puede utilizar virtualización
``` 
lscpu | grep -i virt
``` 
Debe dar un mensaje parecido 
```
Tamaños de las direcciones:               39 bits physical, 48 bits virtual
Virtualización:                           VT-x
```
en caso si tiene procesador intel

## Instalación 
### Paquetes necesarios
Instalar dependencias para la maquina virtual
```
sudo pacman -S qemu-full virt-manager libvirt virt-viewer dnsmasq vde2 openbsd-netcat iptables-nft swtpm --needed
```
Habilitar servicio de libvirtd
```
sudo systemctl enable --now libvirtd
```
## Tuned
```
sudo pacman -S tuned
```
Habilitar servicio
```
sudo systemctl enable --now tuned
```
Ver si esta activo y que tipo de ejecución esta
```
tuned-adm active
```
Por defecto esta balanceado

Para tomar cuantos tipos de ejecución esta es con
```
tuned-adm list
```
Y cambia por preferencia el tipo de ejecución tuned, virtual-host es para tener acceso a todo pero utiliza más energía, solo en caso para servidores o computadoras de mesa.
```
sudo tuned-adm profile virtual-host
```
## Red en qemu/kvm
Ver si esta encendido
```
sudo virsh net-list --all
```
Activarlo
```
sudo virsh net-start default
```
Verificar
```
sudo virsh net-list --all
```
Y inicio automatico
```
sudo virsh net-autostart default
```

