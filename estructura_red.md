
# 🔗 Estructura de Red y Comunicación en el Proyecto

Este documento detalla la estructura de red utilizada para conectar de forma segura los distintos componentes del sistema de gestión de incidencias desarrollado con n8n, desplegado en servidores AWS e IONOS.

---

## 📑 Índice

1. [ZeroTier](#zerotier)
   - ¿Qué es?
   - ¿Cómo lo he implementado?
   - ¿Por qué lo he elegido?
   - Pasos para configurarlo

2. [Claves SSH](#claves-ssh)
   - ¿Qué es?
   - ¿Cómo lo he implementado?
   - ¿Por qué lo he elegido?
   - Pasos para configurarlas

---

## ZeroTier

![img](/img/zerotier.jpeg)

### ¿Qué es?
ZeroTier es una herramienta de red virtual que permite interconectar equipos de manera segura a través de Internet como si estuvieran en una red LAN local. Crea redes privadas virtuales sin necesidad de configurar túneles complejos ni abrir puertos públicos.

### ¿Cómo lo he implementado?
He utilizado ZeroTier para conectar los servidores desplegados en AWS (n8n, base de datos, backup, IA) con el servidor web ubicado en IONOS. Esto ha permitido una red privada con IPs fijas virtuales, sin tener que exponer servicios sensibles a Internet.

### ¿Por qué lo he elegido?
- Seguridad: evita exponer puertos o IPs públicas.
- Sencillez: configuración rápida y escalable.
- Eficiencia: permite tráfico interno sin pasar por NAT o firewalls complejos.

### Pasos para configurarlo

1. Crear una cuenta y una red en [ZeroTier](https://my.zerotier.com)
2. Instalar el cliente en cada servidor:
```bash
curl -s https://install.zerotier.com | bash
```
3. Unirse a la red:
```bash
sudo zerotier-cli join [NETWORK_ID]
```
4. Autorizar los nodos en el panel web.


![interfazZerotier](/img/interfazZerotier.png)

5. Verificar IP interna asignada con:
```bash
ip a
```

---

## Claves SSH

![img](/img/ssh.jpeg)


### ¿Qué es?
Las claves SSH son un método de autenticación basado en criptografía que permite establecer conexiones seguras sin contraseñas. Se utilizan para acceder a otros equipos, automatizar procesos o realizar tareas administrativas.

### ¿Cómo lo he implementado?
Generé claves SSH en el servidor de n8n para conectar automáticamente con los otros servidores (base de datos, backup, interfaz web). Esto me ha permitido ejecutar comandos remotos desde flujos en n8n de forma segura y sin intervención manual.

### ¿Por qué lo he elegido?
- Automatización: permite ejecutar scripts o tareas sin introducir credenciales.
- Seguridad: autenticación basada en clave pública/privada.
- Integración: ideal para usar junto con n8n en flujos programados.

### Pasos para configurarlas

1. Generar clave pública/privada:
```bash
ssh-keygen -t rsa -b 4096
```
2. Copiar la clave al servidor destino:
```bash
ssh-copy-id usuario@ip_privada
```
3. Verificar conexión sin contraseña:
```bash
ssh usuario@ip_privada
```

Con estas herramientas, todos los servicios del proyecto se comunican de forma privada, segura y eficiente.
