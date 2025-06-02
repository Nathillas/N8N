Claro, aquí tienes el **documento completo en Markdown** listo para copiar, con el índice corto enlazado y sin los apartados de *Ejemplo de uso con API* y *Ventajas de la integración*:

---

````markdown
# 🧠 Ollama en el Proyecto

## 📋 Índice de Contenidos - Ollama

- [¿Qué es Ollama?](#qué-es-ollama)
- [Instalación en AWS EC2](#instalación-de-ollama-en-una-instancia-aws-ec2)
- [Integración en el proyecto](#integración-de-ollama-en-el-proyecto)
- [Documentación oficial](#🔗-enlace-a-documentación-oficial)

---

## ¿Qué es Ollama?

[Ollama](https://ollama.com/) es una herramienta diseñada para ejecutar modelos de lenguaje grande (LLMs) de manera local y sencilla. Permite ejecutar modelos como LLaMA, Mistral, Gemma, entre otros, a través de una interfaz de línea de comandos y una API REST. Ollama es ideal para desarrolladores que buscan privacidad, bajo coste y latencia mínima, sin depender de servicios en la nube de terceros.

---

## Instalación de Ollama en una instancia AWS EC2

### Requisitos Previos

- Instancia EC2 con Ubuntu 20.04 o 22.04 (x86_64).
- Clave `.pem` para conexión SSH.
- Acceso al puerto `11434` si se desea exponer el servicio.
- Usuario `ubuntu` por defecto en EC2.

### 1. Conexión a la instancia EC2

```bash
ssh -i /ruta/a/tu/llave.pem ubuntu@IP_DE_TU_INSTANCIA
````

### 2. Actualizar el sistema

```bash
sudo apt update && sudo apt upgrade -y
```

### 3. Instalar herramientas básicas

```bash
sudo apt install curl -y
```

### 4. Instalar Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 5. Iniciar el servicio de Ollama

```bash
ollama serve
```

Para ejecutarlo en segundo plano:

```bash
nohup ollama serve > /dev/null 2>&1 &
```

### 6. Verificar funcionamiento

```bash
curl http://localhost:11434
```

Debe responder con algo como:

```json
{"models":[]}
```

### 7. Acceso externo (opcional)

Asegúrate de abrir el puerto `11434` en el grupo de seguridad de EC2. Luego puedes acceder a la API desde fuera:

```bash
curl http://TU_IP_PUBLICA:11434
```

---

## Integración de Ollama en el Proyecto

Ollama se ha integrado en este proyecto como motor local para ejecutar modelos de lenguaje natural, eliminando la necesidad de depender de APIs externas como OpenAI o Hugging Face.

### Arquitectura de la integración

* **Infraestructura:** Ollama se ejecuta en una instancia EC2 dedicada con Ubuntu.
* **API REST:** Las aplicaciones del proyecto (como n8n o scripts personalizados) se comunican con Ollama mediante peticiones HTTP a `http://IP_SERVIDOR:11434`.
* **Modelo utilizado:** Se ha desplegado el modelo `mistral` con:

  ```bash
  ollama run mistral
  ```

### Casos de uso integrados

* Generación automática de respuestas.
* Resumen de textos y correos.
* Automatización con [n8n](https://n8n.io/).
* Clasificación o análisis de entradas de formularios.

---

## 🔗 Enlace a documentación oficial

👉 [https://ollama.com](https://ollama.com)

```


