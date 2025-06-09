# 📨 Telegram

<p align="center">
  <img src="img/Logotipo-Telegram.webp" alt="telegram" width="550">
</p>



## Índice

1. [¿Qué es Telegram y por qué integrarlo con n8n?](#qué-es-telegram-y-por-qué-integrarlo-con-n8n)
2. [Requisitos previos](#requisitos-previos)
3. [Creación de un Bot en Telegram](#creación-de-un-bot-en-telegram)
4. [Obtener el token del bot](#obtener-el-token-del-bot)
5. [Configurar el Trigger de Telegram en n8n](#configurar-el-trigger-de-telegram-en-n8n)
6. [Ejemplo básico de flujo](#ejemplo-básico-de-flujo)
7. [Consejos y buenas prácticas](#consejos-y-buenas-prácticas)

---

### ¿Qué es Telegram y por qué integrarlo con n8n?

**Telegram** es una aplicación de mensajería instantánea que permite la creación de bots automatizados que pueden enviar y recibir mensajes. En combinación con **n8n**, permite automatizar flujos a partir de mensajes enviados por usuarios, como por ejemplo:

* Reportar incidencias
* Solicitar información automatizada
* Consultar el estado de sistemas o servicios

Integrarlo con n8n permite activar flujos automáticamente cuando un usuario envía un mensaje al bot.

---

### Requisitos previos

* Tener una cuenta en Telegram.
* Tener acceso a una instancia funcional de n8n (local o en servidor).
* Conexión a Internet en el entorno donde corre n8n.

---

### Creación de un Bot en Telegram

1. Abre Telegram y busca el usuario `@BotFather`.

2. Inicia una conversación y escribe el comando:

   ```
   /newbot
   ```

3. Elige un nombre para tu bot (por ejemplo, `IncidenciasBot`).

4. Elige un nombre de usuario único para tu bot, que debe terminar en `bot` (ej: `IncidenciasN8nBot`).

5. El bot te devolverá un **token de acceso** como este:

   ```
   123456789:AAHkjlskjflksdfjlsdf-dfjsldfj
   ```

> **⚠️ Importante:** Guarda este token. Lo necesitarás para conectar tu bot con n8n.

---

### Obtener el token del bot

Puedes volver a consultar el token en `@BotFather` usando `/mybots` y seleccionando tu bot.

---

### Configurar el Trigger de Telegram en n8n

1. En n8n, crea un nuevo workflow.
2. Añade un nodo de tipo **Telegram Trigger**.
3. En las credenciales, crea una nueva y añade el token que te dio `@BotFather`.
4. Selecciona el **evento** que deseas escuchar, por ejemplo: `Message`.
5. Publica tu bot (es decir, inicia conversación desde tu Telegram y envía un mensaje al bot para probar).




