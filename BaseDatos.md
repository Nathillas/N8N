# 📦 Instalación y Configuración de MariaDB para Sistema de Gestión de Incidencias

Este documento te guía paso a paso para instalar MariaDB en Debian, asegurarla, crear una base de datos y una tabla específica para gestionar incidencias reportadas desde Telegram a través de n8n.

---

## 📑 Índice

1. [Instalación de MariaDB en Debian](#1-instalación-de-mariadb-en-debian)
2. [Crear Base de Datos y Usuario para n8n](#2-crear-base-de-datos-y-usuario-para-n8n)
3. [Crear Tabla para Guardar Incidencias](#3-crear-tabla-para-guardar-incidencias)
4. [Permitir conexiones remotas](#4-opcional-permitir-conexiones-remotas)
5. [Integración con n8n](#integración-con-n8n)
6. [Enlaces de referencia](#enlaces-de-referencia)

---

## ✅ 1. Instalación de MariaDB en Debian

### 🔧 Paso 1: Instalar MariaDB

```bash
sudo apt update
sudo apt install mariadb-server -y
```

### 🔐 Paso 2: Asegurar la instalación

Ejecuta el script de seguridad para eliminar configuraciones inseguras por defecto:

```bash
sudo mysql_secure_installation
```

Recomendaciones durante el proceso:

* Establece una contraseña para el usuario root si se solicita.
* Elimina usuarios anónimos.
* Deshabilita el inicio de sesión remoto del usuario root.
* Elimina la base de datos de prueba.
* Recarga los privilegios.

---

## 📃 2. Crear Base de Datos y Usuario para n8n

### 🔑 Paso 1: Acceder al cliente MariaDB como root

```bash
sudo mariadb -u root -p
```

### 📦 Paso 2: Crear la base de datos

```sql
CREATE DATABASE gestion_incidencias CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 👤 Paso 3: Crear un usuario con permisos

```sql
CREATE USER 'usuario_n8n'@'%' IDENTIFIED BY 'tu_password_segura';
GRANT ALL PRIVILEGES ON gestion_incidencias.* TO 'usuario_n8n'@'%';
FLUSH PRIVILEGES;
```

> 💡 Usa `'%'` para permitir conexiones remotas (útil si MariaDB está en otro servidor conectado vía ZeroTier).

---

## 🧱 3. Crear Tabla para Guardar Incidencias

### 🗂️ Paso 1: Seleccionar la base de datos

```sql
USE gestion_incidencias;
```

### 🧾 Paso 2: Crear la tabla incidencias

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

### 📋 Explicación de los campos

| Campo                 | Tipo                | Descripción                                           |
| --------------------- | ------------------- | ----------------------------------------------------- |
| `id`                  | INT AUTO\_INCREMENT | Identificador único (clave primaria)                  |
| `descripcion`         | TEXT                | Descripción del problema proporcionado por el usuario |
| `estado`              | ENUM                | Estado del ticket: pendiente, en\_proceso, resuelto   |
| `usuario_telegram`    | VARCHAR(100)        | Usuario o ID de Telegram                              |
| `fecha_reporte`       | DATETIME            | Fecha en la que se crea el ticket                     |
| `fecha_actualizacion` | DATETIME            | Fecha de la última actualización del ticket           |

---

## 🌐 4. Permitir conexiones remotas

Edita el archivo de configuración de MariaDB:

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Busca esta línea:

```ini
bind-address = 127.0.0.1
```

Y cámbiala por:

```ini
bind-address = 0.0.0.0
```

Reinicia el servicio:

```bash
sudo systemctl restart mariadb
```

> ⚠️ Asegúrate de que la conexión esté restringida por firewall o que los servidores estén conectados en una red privada como **ZeroTier**.

---

## 🔗 Integración con n8n

Para que n8n pueda interactuar con la base de datos MariaDB:

1. Añade un nodo `MySQL` dentro de tu flujo.
2. Crea una nueva credencial con:

   * Host: IP privada del servidor MariaDB (por ejemplo, la IP ZeroTier)
   * Usuario: `usuario_n8n`
   * Contraseña: la definida al crear el usuario
   * Base de datos: `gestion_incidencias`
3. Utiliza nodos tipo `INSERT`, `SELECT` o `UPDATE` según el caso:

   * Insertar una nueva incidencia
   * Consultar incidencias por estado
   * Marcar una incidencia como resuelta

Ejemplo de consulta en un nodo `MySQL`:

```sql
SELECT * FROM incidencias WHERE estado = 'pendiente';
```

Puedes automatizar estas consultas en función de los datos recibidos por Telegram o por API.

---

## 📎 Enlaces de referencia

* [MariaDB Official Docs](https://mariadb.org/documentation/)
* [n8n Documentation](https://docs.n8n.io/)
* [ZeroTier](https://www.zerotier.com)


