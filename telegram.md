# Telegram

<p align="center">
  <img src="img/Logotipo-Telegram.webp" alt="telegram" width="550">
</p>

## 🤖 ¿Qué es un bot de Telegram?

Un **bot de Telegram** es una cuenta especial que no requiere intervención humana directa. Funciona como una "aplicación dentro de Telegram", capaz de:

- Responder automáticamente a comandos y mensajes
- Interactuar con APIs externas
- Automatizar tareas (como enviar alertas, gestionar reservas, responder preguntas, etc.)
- Integrarse con servicios como n8n, bases de datos, IA, etc.

Los bots usan la **Bot API de Telegram** y se controlan mediante peticiones HTTP o mediante librerías de desarrollo (como `python-telegram-bot`, `node-telegram-bot-api`, etc.).

---

## 🪄 Paso 1: Crear un bot desde cero con BotFather

### 🧰 Requisitos previos

- Tener una cuenta de Telegram activa
- Usar la app oficial de Telegram (móvil o escritorio)

### 👣 Pasos

1. Abre la aplicación de **Telegram**.
2. Busca el usuario **@BotFather** (es el bot oficial de Telegram para crear otros bots).
3. Inicia un chat y escribe el comando:
   
```
/start
```

4. Luego crea un nuevo bot con:
```
/newbot
```

5. BotFather te pedirá:
- Un **nombre público** para tu bot (por ejemplo: `GestorIncidenciasBot`)
- Un **nombre de usuario único** que termine en `bot` (por ejemplo: `gestor_incidencias_bot`)

6. Si todo está correcto, recibirás un **token de autenticación** con este aspecto:

```
123456789\:ABCdefGhIJKlmNoPQRstuVWxyz
```

> 🛑 **Importante:** este token es único y sirve para controlar tu bot desde cualquier script. **Guárdalo con cuidado**.

