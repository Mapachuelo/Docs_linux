La función principal de scrcpy es tomar video en tiempo real al celular conectado y poder verlo en el ordenador

## Mediante USB 

Encontrar el puerto del celular
```
adb devices
```
```
scrcpy --video-bit-rate 4M --max-size 1080
```
## Mediante Wifi

Habilitar modo desarrollador en su celular
Habilitar depuración inalámbrica
Entrar a depuración inalambrica
Tomar en cuenta la dirección de la ip y puerto

```
1XX.XXX.XXX.0:12345 -> Un ejemplo de la ip 
```
mediante la terminal tener instalado scrcpy y colocar 

```
adb devices
```
```
adb connect 1XX.XXX.XXX.0:12345
```
Así habilita la depuración inalambrica

```
scrcpy --video-bit-rate 4M --max-size 1080
```
