
# 🧠 Ollama

<p align="center">
  <img src="/img/openai.png" alt="openai" width="800">
</p>


### Índice

1. [¿Qué es Ollama?](https://github.com/Nathillas/N8N/edit/main/ollama.md#-qu%C3%A9-es-ollama)
2. [Instalación de Ollama](https://github.com/Nathillas/N8N/edit/main/ollama.md#%EF%B8%8F-instalaci%C3%B3n-de-ollama)
3. [Integración de Ollama con n8n](https://github.com/Nathillas/N8N/edit/main/ollama.md#-integraci%C3%B3n-de-ollama-con-n8n)
4. [Enlaces de referencia](https://github.com/Nathillas/N8N/blob/main/ollama.md#-enlaces-de-referencia)

---
### 🧠 ¿Qué es Ollama?

**Ollama** es una herramienta que permite ejecutar **modelos de lenguaje grandes (LLMs)** localmente, sin depender de servicios externos en la nube. Utiliza modelos como **Mistral**, **LLaMA**, **Gemma** o **Phi**, permitiendo generar respuestas de texto de forma rápida, privada y totalmente controlada.

En el contexto de este proyecto, Ollama se utiliza para **responder automáticamente a los usuarios de Telegram** con información útil o instrucciones, simulando un asistente de inteligencia artificial.

### Ventajas de usar Ollama:

* ✅ No requiere conexión con APIs externas (como OpenAI).
* ✅ Ejecuta modelos en local o en servidor propio.
* ✅ Compatible con múltiples modelos.
* ✅ Ideal para entornos offline o privados.

---

## ⚙️ Instalación de Ollama

La instalación de Ollama se realizó en un servidor independiente llamado `ollama`. Los pasos seguidos fueron:

1. **Descargar e instalar Ollama**:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

2. **Ejecutar el modelo Mistral**:

```bash
ollama run mistral
```

Esto descarga y activa el modelo **Mistral**, que se usará para generar respuestas automáticas.

3. **Configurar acceso remoto (opcional)**:

Para permitir que Ollama reciba peticiones desde otros dispositivos (como el servidor `n8n`), se habilitó el servicio para escuchar en todas las interfaces:

```bash
nano ~/.ollama/config.toml
```

Añadiendo:

```toml
[server]
address = "0.0.0.0:11434"
```

Luego se inició el servicio:

```bash
ollama serve
```

4. **Abrir el puerto 11434 en el firewall**:

```bash
ufw allow 11434
```

---

## 🔗 Integración de Ollama con n8n

La comunicación entre `n8n` y `ollama` se realiza a través de la red privada **ZeroTier**.

### Paso 1: Conectar ambos servidores con ZeroTier

```bash
curl -s https://install.zerotier.com | sudo bash
zerotier-cli join <ID_DE_RED>
```

Ambos servidores (`n8n` y `ollama`) fueron autorizados en la consola de ZeroTier, obteniendo IPs privadas como:

* `n8n`: `10.x.x.1`
* `ollama`: `10.x.x.2`

---

### Paso 2: Crear flujo en n8n

1. **Telegram Trigger**: captura el mensaje del usuario.
2. **Set Node**: recoge el contenido del mensaje (`{{$json["message"]["text"]}}`).
3. **HTTP Request Node**:

   * Método: `POST`
   * URL: `http://10.x.x.2:11434/api/generate`
   * Headers:

     * `Content-Type: application/json`
   * Body (RAW - JSON):

     ```json
     {
       "model": "mistral",
       "prompt": "{{$json[\"message\"][\"text\"]}}"
     }
     ```
4. **Telegram Send Message**: devuelve la respuesta generada al usuario.

---

## 📎 Enlaces de referencia

* Sitio oficial de Ollama: [https://ollama.com](https://ollama.com)
* Documentación API: [https://github.com/jmorganca/ollama/blob/main/docs/api.md](https://github.com/jmorganca/ollama/blob/main/docs/api.md)
* ZeroTier: [https://www.zerotier.com](https://www.zerotier.com)
* n8n: [https://n8n.io](https://n8n.io)

---


