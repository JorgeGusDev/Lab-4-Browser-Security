# Roadmap de instalación | Lab 4 Browser Security | MIT

## 1. Descripción general

El presente roadmap tiene como finalidad orientar la instalación del entorno de laboratorio y el desarrollo controlado correspondiente al **Lab 4: Browser Security** del MIT. https://css.csail.mit.edu/6.566/2026/labs/lab4.html

El laboratorio será trabajado únicamente en un entorno local, usando la aplicación vulnerable **Zoobar**, con fines académicos, demostrativos y defensivos.

## 2. Alcance del laboratorio

Para el presente trabajo solo se desarrollará el siguiente ejercicio:

**Exercise 1: Print cookie**

Este ejercicio permite evidenciar cómo una cookie de sesión puede ser leída desde el navegador mediante JavaScript cuando la aplicación no cuenta con controles adecuados para protegerla.

El objetivo no es desarrollar un ataque real, sino comprender el riesgo asociado a la exposición de cookies de sesión y usar dicha evidencia como punto de partida para proponer una arquitectura cloud segura en Microsoft Azure.

## 3. Requisitos previos

Antes de iniciar, se debe contar con:

- Oracle VirtualBox instalado.
- VM del curso MIT descargada y descomprimida. https://web.mit.edu/6.858/2026/6.566-standalone-v26.zip
- Acceso a Internet desde la VM.
- Navegador web en el equipo anfitrión.

## 4. Instalación de la VM en Oracle VirtualBox

### 4.1 Crear la máquina virtual

Abrir Oracle VirtualBox y seleccionar:

```txt
New / Nueva
```

Configurar la máquina virtual con valores similares a:

```txt
Name: 6566-v26
Type: Linux
Version: Ubuntu 64-bit o Debian 64-bit
RAM: 2048 MB mínimo, recomendado 4096 MB
CPU: 2 cores recomendado
Hard disk: Use an existing virtual hard disk file
```

Seleccionar el disco de la VM descargada:

```txt
6.566-standalone-v26.vmdk
```

### 4.2 Configurar red

Ingresar a:

```txt
Settings > Network > Adapter 1
```

Configurar:

```txt
Attached to: NAT
```

Luego ingresar a:

```txt
Advanced > Port Forwarding
```

Agregar la siguiente regla para Zoobar:

```txt
Name: Zoobar
Protocol: TCP
Host IP: 127.0.0.1
Host Port: 8080
Guest IP:
Guest Port: 8080
```

Opcionalmente, agregar una regla para SSH:

```txt
Name: SSH
Protocol: TCP
Host IP: 127.0.0.1
Host Port: 2222
Guest IP:
Guest Port: 22
```

### 4.3 Iniciar la VM

Iniciar la VM desde VirtualBox.

Ingresar con:

```txt
Usuario: student
Password: student
```

## 5. Preparación del repositorio del laboratorio

Abrir una terminal dentro de la VM.

Ejecutar:

```bash
cd ~
git clone https://github.com/mit-pdos/6.566-lab-2026 lab
cd lab
```

Verificar las ramas disponibles:

```bash
git fetch --all
git branch -a
```

Crear la rama local del Lab 4:

```bash
git checkout -b lab4 origin/lab4
```

Compilar el laboratorio:

```bash
make
```

## 6. Ejecución de Zoobar

Desde la carpeta del laboratorio, ejecutar:

```bash
cd ~/lab
./zookd 8080
```

La terminal quedará ocupada mientras el servidor esté corriendo. Esto es normal.

Abrir el navegador en el sistema anfitrión y acceder a:

```txt
http://localhost:8080/
```

Debe visualizarse la aplicación Zoobar.

Si se requiere ejecutar más comandos mientras el servidor está corriendo, abrir otra terminal dentro de la VM con:

```txt
CTRL + ALT + T
```

## 7. Verificación inicial de Zoobar

En el navegador:

1. Ingresar a:

```txt
http://localhost:8080/
```

2. Crear un usuario de prueba.

3. Iniciar sesión.

4. Ir a la sección **Users**.

5. Abrir DevTools del navegador:

```txt
F12
```

6. Revisar:

```txt
Application > Cookies > localhost
```

Si el usuario está autenticado, debería observarse una cookie de sesión similar a:

```txt
PyZoobarLogin=usuario#valor_de_sesion
```

## 8. Desarrollo del Exercise 1: Print cookie

El objetivo del **Exercise 1: Print cookie** es mostrar la cookie del usuario autenticado mediante JavaScript cuando el usuario visita la página **Users**.

Este ejercicio evidencia que la cookie de sesión puede ser accesible desde el navegador mediante:

```javascript
document.cookie
```

## 9. Ubicar el archivo 

Desde la VM, en una nueva terminal, ejecutar:

```bash
cd ~/lab
find . -name "users.html"
```

El archivo esperado es:

```txt
zoobar/templates/users.html
```

## 10. Crear una copia de seguridad

Antes de modificar el archivo, crear un backup:

```bash
cp zoobar/templates/users.html zoobar/templates/users.html.bak
```

## 11. Editar la plantilla Users

Abrir el archivo:

```bash
nano zoobar/templates/users.html
```

Ubicar el final del archivo.

Agregar:

```html
<script>
alert(document.cookie);
</script>
```

Guardar cambios en nano:

```txt
CTRL + O
ENTER
CTRL + X
```

## 12. Crear el archivo de respuesta del ejercicio

El Lab 4 solicita que el archivo `answer-1.js` contenga solo JavaScript, sin etiquetas `<script>`.

Crear el archivo:

```bash
echo 'alert(document.cookie);' > answer-1.js
```

Verificar el contenido:

```bash
cat answer-1.js
```

Debe mostrar:

```javascript
alert(document.cookie);
```

## 13. Reiniciar correctamente Zoobar

Si el servidor está corriendo, detenerlo con:

```txt
CTRL + C
```

Si el puerto 8080 queda ocupado, ejecutar:

```bash
sudo fuser -k 8080/tcp
```

Levantar nuevamente el servidor:

```bash
cd ~/lab
./zookd 8080
```

## 14. Validar el ejercicio en el navegador

Abrir:

```txt
http://localhost:8080/
```

Iniciar sesión con el usuario de prueba.

Ingresar a:

```txt
http://localhost:8080/zoobar/index.cgi/users
```

Debe aparecer una alerta del navegador mostrando la cookie de sesión.

Ejemplo esperado:

```txt
PyZoobarLogin=usuario#valor_de_sesion
```

Si no aparece la alerta, realizar una recarga fuerte:

```txt
CTRL + SHIFT + R
```

También se puede verificar desde la consola del navegador:

```javascript
document.cookie
```
