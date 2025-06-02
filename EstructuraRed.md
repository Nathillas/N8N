
Estructura de red del proyecto

ZEROTIER
CLAVES SSH

🔗 Estructura de Red y Comunicación entre Aplicaciones

Para permitir la integración segura y eficiente entre los distintos componentes desplegados en AWS e IONOS, se han utilizado dos tecnologías clave: ZeroTier y claves SSH.

🛡️ ¿Qué es ZeroTier?

ZeroTier es una solución de red virtual que permite conectar varios dispositivos en diferentes ubicaciones geográficas como si estuvieran en una red LAN local. Esta tecnología crea una red privada y segura sobre Internet, ideal para evitar exponer servicios de forma pública.

🔑 ¿Qué son las claves SSH?

SSH (Secure Shell) permite acceso remoto seguro a otros equipos. Mediante el uso de claves SSH, se garantiza autenticación sin necesidad de contraseñas, lo cual mejora la automatización de tareas entre servidores, como la ejecución de scripts o accesos a base de datos.

🔧 ¿Cómo se integran en el proyecto?

ZeroTier se ha utilizado para conectar los servidores de AWS (n8n, base de datos, backup) con el servidor de IONOS (interfaz web), sin exponer los servicios a Internet. Esto permite que todos los servicios interactúen de forma segura a través de IPs privadas.

Claves SSH se han configurado entre los servidores para que desde n8n se puedan ejecutar acciones automatizadas (como copias de seguridad, actualizaciones o consultas).

🔍 Pasos para implementar ZeroTier

Crear una cuenta en https://my.zerotier.com y generar una red.

Instalar ZeroTier en cada servidor:

curl -s https://install.zerotier.com | bash

Unirse a la red creada:

sudo zerotier-cli join [NETWORK_ID]

Autorizar cada nodo desde el panel web de ZeroTier.

Verificar la IP asignada (suele ser 10.x.x.x o 192.168.x.x):

ip a

🔍 Pasos para configurar claves SSH

En el servidor que iniciará conexión (por ejemplo, n8n):

ssh-keygen -t rsa -b 4096

Copiar la clave pública al servidor destino (base de datos, backup, interfaz web, etc):

ssh-copy-id usuario@ip_destino

Probar conexión:

ssh usuario@ip_destino

Con estas dos herramientas, todo el sistema funciona de forma segura, privada y automatizada.

