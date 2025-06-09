# 🧠 Ollama

<p align="center">
  <img src="/img/openai.png" alt="openai" width="800">
</p>

### Índice

1. [¿Qué es Ollama?](#-qué-es-ollama)
2. [Instalación de Ollama](#️-instalación-de-ollama)
3. [Integración de Ollama con n8n](#-integración-de-ollama-con-n8n)
4. [Enlaces de referencia](#-enlaces-de-referencia)

---

### 🧠 ¿Qué es Ollama?

**Ollama** es una herramienta que permite ejecutar **modelos de lenguaje grandes (LLMs)** localmente, sin depender de servicios externos en la nube. Utiliza modelos como **Mistral**, **LLaMA**, **Gemma** o **Phi**, permitiendo generar respuestas de texto de forma rápida, privada y totalmente controlada.

En el contexto de este proyecto, Ollama se utiliza para **responder automáticamente a los usuarios de Telegram** con información últil o instrucciones, simulando un asistente de inteligencia artificial.

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

3. **Configurar acceso remoto**:

Se configuró el servicio para escuchar en todas las interfaces de red, permitiendo que `n8n` acceda al puerto 11434:

```bash
nano ~/.ollama/config.toml
```

Con el siguiente contenido:

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

La comunicación entre `n8n` y `ollama` se realiza directamente mediante la configuración de credenciales HTTP internas en el propio panel de n8n.

### Paso 1: Crear credencial Ollama en n8n

Desde el panel de n8n se ha creado una credencial de tipo **Ollama** especificando la IP del servidor Ollama (por ejemplo: `http://172.29.83.185:11434`). Esta configuración ha sido probada correctamente.


<p align="center">
  <img src="/img/cuentaOllama.png" alt="Flujo Ollama modelo Mistral" />
</p>

### Paso 2: Crear flujo en n8n con el modelo Mistral

Dentro del flujo de trabajo:

1. **Telegram Trigger**: captura el mensaje del usuario.
2. **Set Node**: recoge el contenido del mensaje (`{{$json["message"]["text"]}}`).
3. **Ollama Node**:

   * Credencial: `Ollama account`
   * Modelo: `mistral:latest`
     
<p align="center">
  <img src="/img/credencial.png" alt="Credencial Ollama en n8n" />
</p>

4. **Telegram Send Message**: devuelve la respuesta generada al usuario.

Este método simplifica la integración, sin necesidad de usar nodos HTTP manuales o configuraciones adicionales. Todo queda encapsulado mediante el nodo oficial de Ollama en n8n.

---

## 📌 Enlaces de referencia

* Sitio oficial de Ollama: [https://ollama.com](https://ollama.com)
* Documentación API: [https://github.com/jmorganca/ollama/blob/main/docs/api.md](https://github.com/jmorganca/ollama/blob/main/docs/api.md)
* ZeroTier: [https://www.zerotier.com](https://www.zerotier.com)
* n8n: [https://n8n.io](https://n8n.io)
