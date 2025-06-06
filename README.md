<div align="center">
  <h1>Sistema de Gestión de Incidencias con n8n</h1>
</div>

<p align="center">
  <img src="img/n8n.jpg" alt="n8n" />
</p>


Proyecto final del ciclo **ASIR (Administración de Sistemas Informáticos en Red)**

Desarrollado por: Natalia Barrera Aguilera

Curso 2024/2025


---

## 📁 Descripción del Proyecto

Este proyecto implementa un sistema automatizado para la gestión de incidencias utilizando **n8n**, una herramienta de automatización de flujos de trabajo. Los usuarios pueden reportar incidencias mediante un bot de Telegram, y estas se almacenan en una base de datos, se responden automáticamente con mensajes útiles generados por IA (Ollama) y se notifican a través de diferentes canales. Todo el sistema está desplegado en servidores AWS y un servidor propio en IONOS.

---

## 🏛️ Arquitectura del Sistema

<p align="center">
  <img src="img/estructuraRed.png" alt="Esquema de red">
</p>


---

## 📅 Flujo de Trabajo

- **n8n** actúa como motor de flujo para las automatizaciones.
- Se reciben mensajes desde Telegram a través del bot [@natillas_bot](http://t.me/natillas_bot).
- Se gestionan incidencias: alta, modificación de estado, y cierre.
- Respuestas enriquecidas mediante IA (Ollama).
- Actualización y consulta de incidencias mediante una interfaz CLI conectada al servidor IONOS.

---

## 📄 Funcionamiento

### Flujo en n8n

El sistema también incluye mejoras clave como:
- **Autorización de IDs de Telegram**: Cuando una persona contacta al bot, el sistema genera una petición en el servidor para validar si se trata de un usuario legítimo antes de continuar. Una vez autorizado, el usuario puede crear incidencias libremente.
- **Notas opcionales al cerrar incidencia**: Al finalizar una incidencia, se puede incluir una nota adicional que:
  - Se guarda en la base de datos.
  - Se envía automáticamente al usuario afectado vía Telegram.

1. **Creación de la incidencia**:
   - El usuario inicia una conversación con el bot de Telegram.
   - Se le solicita que describa la incidencia.
   - Una IA integrada mediante Ollama hace preguntas adicionales sobre el problema (formulario interactivo).
   - Una vez completado, se registra en la base de datos.
   - Se confirma la creación del ticket y se ofrece una respuesta automática con consejos iniciales gracias a la IA.

   ![Ejemplo respuesta IA y confirmación de ticket](WhatsApp%20Image%202025-06-02%20at%2013.44.24%20(2).jpeg)

2. **Visualización en el panel de administración**:
   - El panel muestra todos los tickets, permitiendo filtrar por estado y buscar por usuario o descripción.
   - Cada incidencia se puede modificar, actualizar de estado o eliminar.

   ![Interfaz web](interfaz%20(1).PNG)

3. **Actualización del estado**:
   - Cuando un técnico actualiza el estado de una incidencia, se ejecuta un flujo en n8n.
   - Se notifica al usuario por Telegram con el nuevo estado y una posible nota.

   ![Notificación Telegram](WhatsApp%20Image%202025-06-02%20at%2013.48.13%20(1).jpeg)

4. **Ejecución automática de tareas**:
   - Algunos tickets generan flujos automatizados como backups, escaneos, etc.
   - Estos se ejecutan desde n8n por SSH usando comandos remotos.

5. **Registro completo**:
   - Todas las acciones quedan registradas en la base de datos.
   - El usuario puede recibir mensajes adicionales generados por Ollama según la categoría del problema.



### Interfaz de Gestión (IONOS)

Se ha implementado una interfaz sencilla en la terminal (CLI) del servidor IONOS donde:
- Se listan incidencias.
- Se pueden modificar.
- Se pueden añadir nuevas manualmente.
- Todo esto controlado por una IA local (Claude) con acceso restringido mediante MCP Filesystem.



### Conclusión

Este sistema ofrece una plataforma automatizada, segura y enriquecida con inteligencia artificial para gestionar incidencias técnicas, ofreciendo una experiencia ágil tanto para usuarios como para administradores.


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


## 📖 Referencias

* [Documentación oficial de n8n](https://docs.n8n.io/)
* [API Bots Telegram](https://core.telegram.org/bots/api)
* [Ollama](https://ollama.com/)
* [ZeroTier](https://www.zerotier.com/)

---

**Autor:** Natalia Barrera Aguilera
