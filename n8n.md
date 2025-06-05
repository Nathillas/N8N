# N8N

<p align="center">
  <img src="/img/titulon8n.png" alt="n8n" width="600">
</p>


## 🧩 Índice  

- [🔧 ¿Qué es n8n?](#qué-es-n8n)
- [📌 Funcionalidad de n8n en el Proyecto](#funcionalidad-de-n8n-en-el-proyecto)
- [🚀 Instalación de n8n](#instalación-de-n8n)

---

## 🔧 ¿Qué es n8n?



**n8n** (pronunciado _"n-eight-n"_) es una herramienta de automatización de flujos de trabajo basada en nodos, de código abierto y autoalojable. Permite conectar múltiples servicios (APIs, bases de datos, archivos, etc.) para automatizar procesos sin necesidad de escribir código.

Características principales:

- Automatización sin código o con poco código.
- Más de 300 integraciones preconfiguradas.
- Soporte para flujos condicionales y lógica compleja.
- Interfaz visual muy intuitiva.
- Capacidad de ejecutar scripts personalizados en JavaScript.

---

## 📌 Funcionalidad de n8n en el Proyecto

En este proyecto, n8n se utiliza como el **motor de automatización principal** para:

- Procesar datos que llegan desde formularios web.
- Integrar servicios externos (como APIs, emails o bases de datos).
- Generar notificaciones automáticas.
- Ejecutar tareas programadas (cronjobs).
- Registrar logs y errores de forma centralizada.

Esta herramienta permite mejorar la eficiencia del sistema reduciendo la intervención manual y facilitando la escalabilidad de las integraciones.

---

## 🚀 Instalación de n8n

## Preparar el sistema operativo

Lo primero es asegurarnos de que nuestro sistema esté actualizado y listo para instalar n8n.

### Actualizar el sistema

Inicia sesión como usuario root y actualiza el sistema con los siguientes comandos:

```bash
su -
apt update && apt upgrade -y
```

Instalar herramientas básicas

Luego, instala las utilidades esenciales necesarias para la gestión del servidor:

```

apt install sudo curl wget nano ufw software-properties-common -y
```

Configurar el firewall (opcional)

Para mejorar la seguridad, habilita el firewall y permite el acceso SSH:
```

ufw allow OpenSSH
ufw enable
```

Crear un usuario dedicado para n8n

Es recomendable utilizar un usuario exclusivo para ejecutar n8n, lo que mejora la seguridad y organización.

Crea el usuario:
```

adduser n8n
```

Cuando se te solicite, configura la contraseña para el usuario. Luego, añade el usuario al grupo sudo para que tenga permisos administrativos:
```

usermod -aG sudo n8n
```

Finalmente, cambia al nuevo usuario:
```

su - n8n
```

Instalar Node.js y npm

n8n requiere Node.js para funcionar, así que procederemos a instalarlo.
Instalar Node Version Manager (nvm)

Descarga e instala nvm para gestionar fácilmente versiones de Node.js:
```

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
source ~/.bashrc
```

Instalar la versión 20 de Node.js

Instala la versión recomendada (compatible con n8n):
```

nvm install 20
nvm use 20
```

Verificar las versiones instaladas

Asegúrate que Node.js y npm estén correctamente instalados:
```

node -v
npm -v
```

Configurar npm para evitar problemas de permisos

Para evitar problemas al instalar paquetes globales, configuraremos un directorio personalizado.
Crear un directorio para paquetes globales
```

mkdir -p ~/.npm-global
```

Configurar npm para usar este directorio
```

npm config set prefix '~/.npm-global'
```

Añadir la ruta de npm al PATH

Edita el archivo .bashrc para incluir el nuevo directorio en la variable PATH:
```

nano ~/.bashrc
```

Añade al final del archivo la siguiente línea:
```

export PATH="$HOME/.npm-global/bin:$PATH"
```

Guarda y cierra el archivo, luego aplica los cambios:
```

source ~/.bashrc
```

Instalar n8n

Ahora sí, instalamos n8n de manera global utilizando npm:
```

npm install -g n8n
```

Verificar la instalación

Ejecuta n8n para comprobar que funciona correctamente:
```
n8n
```

Esto debería iniciar el servidor en http://localhost:5678. Puedes pulsar Ctrl + C para detenerlo. Deberías ver una interfaz similar en el navegador.
Configurar n8n como un servicio

Para que n8n se ejecute automáticamente y en segundo plano, configuraremos un servicio systemd.
Cambiar a usuario root
```

exit
su -
```

Crear archivo de servicio
```

nano /etc/systemd/system/n8n.service
```

Añadir la siguiente configuración al archivo
```

[Unit]
Description=n8n Automation Tool
After=network.target

[Service]
Type=simple
User=n8n
ExecStart=/home/n8n/.npm-global/bin/n8n
Restart=on-failure
Environment=NODE_ENV=production
Environment=GENERIC_TIMEZONE=Europe/Madrid
Environment=N8N_SECURE_COOKIE=false

[Install]
WantedBy=multi-user.target
```

Guarda y cierra el archivo.
Recargar configuración de systemd
```

systemctl daemon-reload
```

Habilitar y arrancar el servicio
```

systemctl enable n8n
systemctl start n8n
```

Verificar que el servicio esté funcionando
```

systemctl status n8n
```

Acceso a n8n
Acceder localmente

Si estás en el mismo servidor, abre un navegador y visita:
```

http://localhost:5678
```

### Opcion1: Configurar acceso remoto con Nginx y HTTPS 

Si quieres acceder a n8n desde otro dispositivo, es recomendable configurar un proxy inverso seguro con Nginx.
Instalar Nginx
```

sudo apt install nginx -y
```

Crear configuración para n8n
```

sudo nano /etc/nginx/sites-available/n8n
```

Añadir esta configuración
```

server {
    server_name tu-dominio.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Guarda y cierra el archivo.
Activar la configuración y reiniciar Nginx
```

sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

### (Opcional) Configurar HTTPS con Certbot

Para habilitar HTTPS gratis con Let’s Encrypt:
```

sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx
```
