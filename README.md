# Sistema de Gestión de Incidencias con n8n

Proyecto final del ciclo **ASIR (Administración de Sistemas Informáticos en Red)**
Desarrollado por: \[Tu Nombre]
Curso 2024/2025

---

## 📁 Descripción del Proyecto

Este proyecto implementa un sistema automatizado para la gestión de incidencias utilizando **n8n**, una herramienta de automatización de flujos de trabajo. Los usuarios pueden reportar incidencias mediante un bot de Telegram, y estas se almacenan en una base de datos, se responden automáticamente con mensajes útiles generados por IA (Ollama) y se notifican a través de diferentes canales. Todo el sistema está desplegado en servidores AWS y un servidor propio en IONOS.

---

## 🏛️ Arquitectura del Sistema

![Esquema de red](docs/esquema.jpeg)

### Componentes:

* **AWS**:

  * Servidor `n8n` (con IP estática)
  * Servidor de base de datos (MySQL)
  * Servidor de backup y Ollama
  * Comunicación interna mediante **ZeroTier** (red privada)
  * SSH configurado entre servicios

* **IONOS**:

  * Servidor web con interfaz de administración
  * Comunicación por claves SSH a servidores en AWS

---

## 📅 Flujo de Trabajo

1. El usuario contacta con `@natillas_bot` en Telegram.
2. El mensaje se recibe mediante un `Telegram Trigger` en n8n.
3. Se valida y registra la incidencia en MySQL.
4. Se genera una respuesta automática con IA (Ollama) ofreciendo consejos.
5. Se notifica al usuario y se crea el registro en el panel web.
6. El estado de la incidencia se puede actualizar, y el bot informa del cambio.

---

## 🔧 Tecnologías utilizadas

* [n8n](https://n8n.io/) (automatización de flujos)
* [Telegram Bot API](https://core.telegram.org/bots)
* [Ollama](https://ollama.com/) (IA local para generar mensajes automáticos)
* MySQL (almacenamiento de incidencias)
* ZeroTier (conectividad privada entre servidores)
* Interfaz web generada con IA Claude
* Servidores IONOS y AWS (nube y hosting privado)

---

## 🖥️ Preparar el Sistema Operativo

Lo primero es asegurarnos de que nuestro sistema esté actualizado y listo para instalar n8n.

### ✅ Actualizar el sistema

Inicia sesión como usuario root y actualiza el sistema con los siguientes comandos:

```bash
su -
apt update && apt upgrade -y
```

### 🔧 Instalar herramientas básicas

Instala las utilidades esenciales necesarias para la gestión del servidor:

```bash
apt install sudo curl wget nano ufw software-properties-common -y
```

### 🔐 Configurar el firewall (opcional)

Para mejorar la seguridad, habilita el firewall y permite el acceso SSH:

```bash
ufw allow OpenSSH
ufw enable
```

### 👤 Crear un usuario dedicado para n8n

Es recomendable utilizar un usuario exclusivo para ejecutar n8n:

```bash
adduser n8n
usermod -aG sudo n8n
su - n8n
```

### 📦 Instalar Node.js y npm

n8n requiere Node.js. Para ello, primero instalamos NVM:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
source ~/.bashrc
```

Luego instalamos Node.js 20:

```bash
nvm install 20
nvm use 20
```

Verificamos versiones:

```bash
node -v
npm -v
```

### 🧰 Configurar npm para evitar problemas de permisos

Crear directorio para paquetes globales:

```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
```

Añadir el directorio al PATH:

```bash
nano ~/.bashrc
```

Agrega al final:

```bash
export PATH="$HOME/.npm-global/bin:$PATH"
```

Aplica los cambios:

```bash
source ~/.bashrc
```

### 🚀 Instalar n8n

```bash
npm install -g n8n
```

Ejecuta n8n para comprobar:

```bash
n8n
```

### ⚙️ Configurar n8n como servicio (systemd)

Cambiar a root:

```bash
exit
su -
```

Crear archivo de servicio:

```bash
nano /etc/systemd/system/n8n.service
```

Contenido:

```ini
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

Activar el servicio:

```bash
systemctl daemon-reload
systemctl enable n8n
systemctl start n8n
systemctl status n8n
```

Accede localmente a:

```bash
http://localhost:5678
```

### 🌐 Opción: Configurar acceso remoto con Nginx y HTTPS

Instalar Nginx:

```bash
sudo apt install nginx -y
```

Crear configuración para n8n:

```bash
sudo nano /etc/nginx/sites-available/n8n
```

Contenido:

```nginx
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

Activar configuración:

```bash
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

### 🔒 (Opcional) Configurar HTTPS con Certbot

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx
```

✅ ¡Y listo! Ahora tienes n8n funcionando en tu servidor Debian 12, accesible localmente o remotamente.

## 📦 Instalación y Configuración de MariaDB para Sistema de Gestión de Incidencias

Este documento te guía paso a paso para instalar MariaDB en Debian, asegurarla, crear una base de datos y una tabla específica para gestionar incidencias reportadas desde Telegram a través de n8n.

### ✅ 1. Instalación de MariaDB en Debian

#### 🔧 Paso 1: Instalar MariaDB

```bash
sudo apt update
sudo apt install mariadb-server -y
```

#### 🔐 Paso 2: Asegurar la instalación

```bash
sudo mysql_secure_installation
```

Recomendaciones durante el proceso:

* Establece una contraseña para el usuario root si se solicita.
* Elimina usuarios anónimos.
* Deshabilita el inicio de sesión remoto del usuario root.
* Elimina la base de datos de prueba.
* Recarga los privilegios.

### 🗃️ 2. Crear Base de Datos y Usuario para n8n

#### 🔑 Paso 1: Acceder al cliente MariaDB como root

```bash
sudo mariadb -u root -p
```

#### 📦 Paso 2: Crear la base de datos

```sql
CREATE DATABASE gestion_incidencias CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

#### 👤 Paso 3: Crear un usuario con permisos

```sql
CREATE USER 'usuario_n8n'@'%' IDENTIFIED BY 'tu_password_segura';
GRANT ALL PRIVILEGES ON gestion_incidencias.* TO 'usuario_n8n'@'%';
FLUSH PRIVILEGES;
```

💡 Usa `%` para permitir conexiones remotas (útil si MariaDB está en otro servidor conectado vía ZeroTier).

### 🧱 3. Crear Tabla para Guardar Incidencias

#### 🗂️ Paso 1: Seleccionar la base de datos

```sql
USE gestion_incidencias;
```

#### 🧾 Paso 2: Crear la tabla `incidencias`

```sql
CREATE TABLE incidencias (
    id INT AUTO_INCREMENT PRIMARY KEY,
    descripcion TEXT NOT NULL,
    estado ENUM('pendiente', 'en_proceso', 'resuelto') DEFAULT 'pendiente',
    usuario_telegram VARCHAR(100),
    fecha_reporte DATETIME DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

#### 📋 Explicación de los campos

| Campo                | Tipo                | Descripción                                            |
| -------------------- | ------------------- | ------------------------------------------------------ |
| id                   | INT AUTO\_INCREMENT | Identificador único (clave primaria).                  |
| descripcion          | TEXT                | Descripción del problema proporcionado por el usuario. |
| estado               | ENUM                | Estado del ticket: pendiente, en\_proceso, resuelto.   |
| usuario\_telegram    | VARCHAR(100)        | Usuario o ID de Telegram (opcional).                   |
| fecha\_reporte       | DATETIME            | Fecha en la que se crea el ticket.                     |
| fecha\_actualizacion | DATETIME            | Fecha de la última actualización del ticket.           |

### 🌐 4. (Opcional) Permitir conexiones remotas

Edita el archivo de configuración de MariaDB:

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Busca esta línea:

```
bind-address = 127.0.0.1
```

Y cámbiala por:

```
bind-address = 0.0.0.0
```

Reinicia el servicio:

```bash
sudo systemctl restart mariadb
```

⚠️ Asegúrate de que la conexión esté restringida por firewall o que los servidores estén conectados en una red privada como ZeroTier.

✅ ¡Listo! Ahora puedes conectar tu flujo de trabajo en n8n a esta base de datos usando el nodo MySQL para crear, leer o actualizar incidencias.

---

## 📄 Capturas de funcionamiento

### Flujo en n8n

![n8n workflow](docs/n8n_workflow.png)

### Panel web personalizado

![Panel web](docs/interfaz.png)

### Registro de incidencia con IA

![Telegram registro](docs/registro_incidencia.jpeg)

### Notificación de estado actualizado

![Telegram actualización](docs/estado_actualizado.jpeg)

---

## 🔹 Funcionalidades implementadas

* [x] Registro de incidencias por Telegram
* [x] Generación de consejos automáticos con IA
* [x] Inserción en base de datos y respuesta al usuario
* [x] Panel web para ver, editar y cerrar tickets
* [x] Automatismo SSH para tareas programadas (copias de seguridad)
* [x] ZeroTier para red privada virtual

---

## 📆 Cómo desplegar el proyecto

### Requisitos

* Acceso a los servidores con n8n y MySQL funcionando
* Cuenta en Telegram y bot creado
* Claves de acceso SSH entre servidores

### Instrucciones

1. Clonar el repositorio:

```bash
git clone https://github.com/tuusuario/incidencias-n8n.git
cd incidencias-n8n
```

2. Acceder a n8n en el navegador (según IP de tu servidor)

3. Importar los flujos desde `/n8n-workflows/`

4. Configurar credenciales: Telegram API Token, acceso MySQL, Ollama LLM

---

## 📖 Referencias

* [Documentación oficial de n8n](https://docs.n8n.io/)
* [API Bots Telegram](https://core.telegram.org/bots/api)
* [Ollama](https://ollama.com/)
* [ZeroTier](https://www.zerotier.com/)

---

**Licencia:** Creative Commons BY-SA 4.0
**Autor:** \[Tu Nombre]
**Fecha de entrega:** Junio 2025
