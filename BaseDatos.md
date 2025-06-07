# 📦 Instalación y Configuración de MariaDB para Sistema de Gestión de Incidencias

Este documento te guía paso a paso para instalar MariaDB en Debian, asegurarla, crear una base de datos y una tabla específica para gestionar incidencias reportadas desde Telegram a través de n8n.

---

## ✅ 1. Instalación de MariaDB en Debian

### 🔧 Paso 1: Instalar MariaDB

```
sudo apt update
sudo apt install mariadb-server -y
```

### 🔐 Paso 2: Asegurar la instalación

Ejecuta el script de seguridad para eliminar configuraciones inseguras por defecto:
```
sudo mysql_secure_installation
```
Recomendaciones durante el proceso:

    Establece una contraseña para el usuario root si se solicita.

    Elimina usuarios anónimos.

    Deshabilita el inicio de sesión remoto del usuario root.

    Elimina la base de datos de prueba.

    Recarga los privilegios

🗃️ 2. Crear Base de Datos y Usuario para n8n
🔑 Paso 1: Acceder al cliente MariaDB como root
```
sudo mariadb -u root -p

```
📦 Paso 2: Crear la base de datos
```
CREATE DATABASE gestion_incidencias CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
👤 Paso 3: Crear un usuario con permisos
```
CREATE USER 'usuario_n8n'@'%' IDENTIFIED BY 'tu_password_segura';
GRANT ALL PRIVILEGES ON gestion_incidencias.* TO 'usuario_n8n'@'%';
FLUSH PRIVILEGES;
```
    💡 Usa '%' para permitir conexiones remotas (útil si MariaDB está en otro servidor conectado vía ZeroTier).

🧱 3. Crear Tabla para Guardar Incidencias
🗂️ Paso 1: Seleccionar la base de datos
```
USE gestion_incidencias;
```
🧾 Paso 2: Crear la tabla incidencias
```
CREATE TABLE incidencias (
    id INT AUTO_INCREMENT PRIMARY KEY,
    descripcion TEXT NOT NULL,
    estado ENUM('pendiente', 'en_proceso', 'resuelto') DEFAULT 'pendiente',
    usuario_telegram VARCHAR(100),
    fecha_reporte DATETIME DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```
📋 Explicación de los campos

Campo	Tipo	Descripción
id	INT AUTO_INCREMENT	Identificador único (clave primaria).

numero_ticket	INT AUTO_INCREMENT	Código único generado por n8n para el seguimiento.

descripcion	TEXT	Descripción del problema proporcionado por el usuario.

estado	ENUM	Estado del ticket: pendiente, en_proceso, resuelto.

usuario_telegram	VARCHAR(100)	Usuario o ID de Telegram (opcional).

fecha_reporte	DATETIME	Fecha en la que se crea el ticket.

fecha_actualizacion	DATETIME	Fecha de la última actualización del ticket.

🌐 4. (Opcional) Permitir conexiones remotas

Edita el archivo de configuración de MariaDB:
```
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
```
sudo systemctl restart mariadb
```
   ⚠️ Asegúrate de que la conexión esté restringida por firewall o que los servidores estén conectados en una red privada como ZeroTier.

✅ ¡Listo! Ahora puedes conectar tu flujo de trabajo en n8n a esta base de datos usando el nodo MySQL para crear, leer o actualizar incidencia


