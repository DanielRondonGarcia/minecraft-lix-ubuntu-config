# Instalación del servidor minecraft en linux-ubuntu


### Actualizar el sistema
Como siempre, comience por actualizar su sistema a la última versión.
````
sudo apt update -y && sudo apt upgrade -y
````
### Instalar dependencias
Ahora debemos instalar algunas de las dependencias requeridas para que Minecraft funcione.
````
sudo apt install git build-essential -y
````
### Instalar Java
````
sudo apt install openjdk-17-jre-headless -y
````
Verifique que la instalación esté completa y verifique la versión usando el siguiente comando:
````
java -version
````
### Instalar el servidor de Minecraft
En primer lugar, hay que crear un usuario separada para Minecraft se ejecute.
````
sudo useradd -r -m -U -d /opt/minecraft -s /bin/bash minecraft
````
Ahora, crea una contraseña para tu nuevo usuario:
````
sudo passwd minecraft
````
Después de crear la contraseña de Minecraft, cambie al usuario de Minecraft que acabamos de crear.
````
su - minecraft
````
Ahora en nuestro perfil de Minecraft, cree los directorios requeridos a continuación:
````
mkdir ~/backups ~/tools ~/server
````

### Instalación Mcrcon
A continuación, debemos instalar **mcron** , un cliente RCON que le permite conectarse a los servidores de Minecraft.

Descargue al directorio **~/tools** con el siguiente comando:
````
git clone https://github.com/Tiiffi/mcrcon.git ~/tools/mcrcon
````
Ahora, cambie al directorio mcrcon y cree la herramienta con el siguiente comando:
````
cd ~/tools/mcrcon
gcc -std=gnu11 -pedantic -Wall -Wextra -O2 -s -o mcrcon mcrcon.c
````
Para verificar la instalación:
````
./mcrcon -v
````
### Servidor Minecraft: Descarga y Configuración
[https://www.minecraft.net/es-es/download/server](https://www.minecraft.net/es-es/download/server)  

Descargue al directorio ~/server con el siguiente comando:
````
wget https://launcher.mojang.com/v1/objects/125e5adf40c659fd3bce3e66e67a16bb49ecc1b9/server.jar -P ~/server
````

A continuación, vaya al directorio ~/server e inicie el servidor de Minecraft:
````
cd ~/server
java -Xmx1024M -Xms1024M -jar server.jar nogui
````
Recibirá este error después de ejecutar el comando:
`[07:46:12] [main/ERROR]: Failed to load properties from file:
server.properties
[07:46:12] [main/WARN]: Failed to load eula.txt
[07:46:12] [main/INFO]: You need to agree to the EULA
in order to run the server. Go to eula.txt for more info.`

Para solucionar esto, debe aceptar el CLUF de Minecraft. Para ello, edite el archivo eula.txt.


````
nano ~/server/eula.txt
````
Cambie la siguiente línea de eula=false a eula=true:
`eula=true`
Guarde y cierre el archivo. Luego, edite el archivo server.properties y la contraseña rcon para EXACTAMENTE los valores a continuación.
````
nano ~/server/server.properties
````
Edite las siguientes líneas en el archivo:
`rcon.password=your-password
enable-rcon=true`
Guarde y cierre, luego escriba exit para cerrar la sesión del usuario de Minecraft.
#
### Crear servicio Systemd para Minecraft

Ahora crearemos un archivo de servicio systemd para administrar el servicio de Minecraft. Puede crearlo con el siguiente comando, pero estos comandos deben ejecutarse como usuario sudo del sistema y no como usuario de Minecraft.
````
sudo nano /etc/systemd/system/minecraft.service
````
Ahora, copie y pegue las siguientes líneas en el archivo:

````
[Unit]
Description=Minecraft Server
After=network.target

[Service]
User=minecraft
Nice=1
KillMode=none
SuccessExitStatus=0 1
ProtectHome=true
ProtectSystem=full
PrivateDevices=true
NoNewPrivileges=true
WorkingDirectory=/opt/minecraft/server
ExecStart=/usr/bin/java -Xmx1024M -Xms1024M
````
````
-jar server.jar nogui
ExecStop=/opt/minecraft/tools/mcrcon/mcrcon
````
````
-H 127.0.0.1 -P 25575 -p your-password stop

[Install]
WantedBy=multi-user.target
````
Guarde y cierre cuando su archivo se vea como la imagen de arriba.

#
### Debemos reiniciar el demonio systemd para nuestro Shell. Para hacer esto sin la contraseña de root, se requiere reiniciar. Reinicie su Shell
````
sudo reboot
````
Vuelva a iniciar sesión como su usuario sudo, luego inicie y habilite los servicios.
````
sudo systemctl start minecraft
````
````
sudo systemctl enable minecraft
````
Para consultar y verificar el servicio de Minecraft ingresa a:
````
sudo systemctl status minecraft
````
Para verificar que el servidor de Minecraft se está ejecutando y escuchando en el puerto 25575, ingrese lo siguiente:
````
sudo netstat -pnltu | grep 25575
````
Ahora puede acceder a Minecraft con la utilidad mcrcon. Usa el siguiente comando:
````
/opt/minecraft/tools/mcrcon/mcrcon -H 127.0.0.1 -P 25575
````

````
-p your-password -t
````
A continuación, iniciaremos sesión en el juego.
