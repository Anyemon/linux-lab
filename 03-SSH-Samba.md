# Guía de Configuración: Servidor de Archivos Samba y Acceso SSH en Debian

Este documento detalla el procedimiento para configurar un recurso compartido accesible desde Windows y habilitar el control remoto mediante SSH bajo un usuario específico.

---

## Parte 1: Configuración del Servidor Samba

### 1. Preparación de Directorios y Permisos
Creación de la carpeta física en Debian y asignación de permisos globales para permitir el acceso cruzado entre usuarios locales.

```bash
# Crear la carpeta compartida
sudo mkdir -p /home/juanma/compartido

# Dar permisos totales a la carpeta compartida
sudo chmod 777 /home/juanma/compartido

# Permitir el paso (ejecución) a través de la carpeta raíz del usuario
sudo chmod o+x /home/juanma
```

### 2. Creación del Usuario en el Sistema y Samba
El usuario debe existir tanto en la base de datos de Linux como en la de Samba con sus respectivas contraseñas.

```bash
# Crear la contraseña de Samba para el usuario
sudo smbpasswd -a juanssh
```

### 3. Configuración de Samba (`/etc/samba/smb.conf`)
Editar el archivo de configuración de Samba para exponer el recurso en la red de forma segura.

```bash
sudo nano /etc/samba/smb.conf
```

Añadir el siguiente bloque al final del archivo:

```ini
[test]
comment = test bascico de comparticion de recursos
path = /home/juanma/compartido
read only = no
valid users = juanssh
force user = juanma
```

Reiniciar el servicio para aplicar los cambios:
```bash
sudo systemctl restart smbd
```

### 4. Conexión y Verificación desde Windows
Para montar la carpeta en Windows como una unidad de red utilizando la consola de comandos (CMD):

```cmd
:: Comando general para mapear la unidad Z:
net use Z: \\192.168.1.5\test /user:juanssh *

:: Navegar al recurso desde CMD
Z:
dir
```
*Nota: También se puede acceder visualmente desde el Explorador de Archivos introduciendo `\\192.168.1.5\test` en la barra de direcciones o buscando la unidad **Z:** en "Este Equipo".*

---

## Parte 2: Configuración y Resolución de Problemas en SSH

### 1. Habilitación del Entorno del Usuario
Asegurar que el usuario tenga una terminal válida asignada en Debian.

```bash
sudo usermod -s /bin/bash juanssh
```

### 2. Resolución de Bloqueos de Seguridad (Módulo PAM)
En caso de recibir el error `Access denied for user juanssh by PAM account configuration`, aplicar las siguientes directivas para desbloquear la cuenta y remover restricciones de caducidad:

```bash
# Desbloquear la cuenta en el sistema
sudo usermod -U juanssh

# Eliminar restricciones de expiración de contraseña
sudo chage -I -1 -m 0 -M 99999 -E -1 juanssh

# Actualizar/Sincronizar la contraseña del usuario en Linux
sudo passwd juanssh
```

### 3. Verificación de la Configuración de SSH (`/etc/ssh/sshd_config`)
Asegurar que la autenticación por contraseña esté explícitamente permitida.

```bash
sudo nano /etc/ssh/sshd_config
```

Confirmar que la siguiente línea esté activa y sin el símbolo `#`:
```text
PasswordAuthentication yes
```

Reiniciar el servicio SSH:
```bash
sudo systemctl restart ssh
```

### 4. Acceso Remoto desde Windows
Para tomar el control de la terminal de Debian de forma remota, ejecutar desde el CMD de Windows:

```cmd
ssh juanssh@192.168.1.5
```
