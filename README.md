# TV-Controler — Plataforma de Señalización Digital y Control de Pantallas

Sistema integral, libre y autohospedado para la **gestión centralizada de cartelería digital, pantallas informativas y televisores publicitarios**. Diseñado para operar en redes locales o en la nube, ofreciendo control remoto en tiempo real, programación de listas de reproducción (playlists), widgets interactivos y compatibilidad con cualquier Smart TV o navegador web.

**Repositorio oficial:** [https://github.com/Chenteneitor/TV-Controler](https://github.com/Chenteneitor/TV-Controler)

---

## 1. Características Principales

- **Gestión Centralizada de Pantallas:** Supervisa y administra múltiples televisores, quioscos y monitores desde un único panel web interactivo.
- **Playlists y Contenidos Multimedia:** Reproducción fluida de videos en alta definición (MP4, WebM, secuencias HLS y streams RTSP), imágenes, diapositivas y documentos.
- **Widgets Web Interactivos:** Integración transparente de páginas web externas o locales, tableros de control (dashboards), feeds RSS, reloj y estado del tiempo.
- **Modo Pantalla Completa y Ampliación Interactiva:**
  - Al pulsar o hacer clic sobre cualquier video publicitario o widget interactivo embebido, el encabezado del reproductor se oculta automáticamente y el contenido se expande a pantalla completa.
  - Comunicación bidireccional por `postMessage` (`request-fullscreen` y `exit`) para permitir que aplicaciones web alojadas en iframes o widgets soliciten pantalla completa sin fricción.
  - Atajos de teclado universales (`f` / `F11`) y conmutación por doble clic para pantallas y quioscos táctiles.
- **Soporte Multi-Plataforma Sin Licencias por Dispositivo:**
  - **Reproductor Web Universal (`/player`):** Compatible con navegadores modernos en Smart TVs (LG webOS, Samsung Tizen, Android TV, Fire TV Stick, Raspberry Pi, mini PCs, Windows, Linux).
  - **Aplicación Android TV Nativa:** Soporta arranque automático desatendido, apagado programado de pantalla y almacenamiento en caché local (*ContentCache*) para seguir reproduciendo contenido aun si la red se interrumpe.
- **Seguridad Robusta:** Autenticación por tokens JWT, contraseñas con hash `bcrypt`, protección contra SSRF en peticiones de red privada, base de datos SQLite embebida de alto rendimiento (`better-sqlite3`) y control de acceso basado en roles (RBAC).

---

## 2. Requisitos del Sistema

- **Opción A (Recomendada):** Servidor Linux con **Docker** y **Docker Compose**.
- **Opción B (Instalación Nativa):** **Node.js 20.6+** y **npm**.
  - Opcional: `ffmpeg` instalado en el sistema operativo para generación automática de miniaturas y detección de duración de videos. (El contenedor Docker ya lo incluye).

---

## 3. Instalación Rápida

### Opción A: Despliegue con Docker (Producción)

1. Clonar el repositorio en el servidor:
   ```bash
   git clone https://github.com/Chenteneitor/TV-Controler.git
   cd TV-Controler
   ```

2. Iniciar el servicio con Docker Compose:
   ```bash
   docker compose up -d --build
   ```

3. Verificar que el contenedor esté corriendo y saludable:
   ```bash
   docker compose ps
   ```

4. Abrir en el navegador:
   ```
   http://<IP_SERVIDOR>:3001
   ```
   El primer usuario registrado en la base de datos se convertirá automáticamente en el **Administrador de la Plataforma**.

---

### Opción B: Instalación Tradicional con Node.js

1. Clonar y acceder a la carpeta del servidor:
   ```bash
   git clone https://github.com/Chenteneitor/TV-Controler.git
   cd TV-Controler/server
   ```

2. Instalar dependencias:
   ```bash
   npm install
   ```

3. Iniciar el servidor:
   ```bash
   SELF_HOSTED=true npm start
   ```

4. El servidor se iniciará en el puerto `3001` (HTTP) o `3443` (si existen certificados SSL en `server/certs/`).

---

## 4. Configuración del Entorno (`.env`)

Las principales variables de configuración pueden definirse en un archivo `.env` o en el bloque `environment` de `docker-compose.yml`:

| Variable | Descripción | Valor por Defecto |
| :--- | :--- | :--- |
| `PORT` | Puerto HTTP donde atiende TV-Controler | `3001` |
| `HTTPS_PORT` | Puerto HTTPS si se habilitan certificados SSL locales | `3443` |
| `NODE_ENV` | Modo de ejecución (`production` para entornos productivos) | `production` |
| `SELF_HOSTED` | Habilita el modo autohospedado con todas las funciones desbloqueadas | `true` |
| `HIDE_BILLING` | Oculta las vistas de suscripción y planes comerciales SaaS | `true` |
| `DISABLE_HOMEPAGE` | Redirige `/` directamente a la aplicación web (`/app`) | `true` |
| `DATA_DIR` | Ruta donde se almacenan la base de datos SQLite y los archivos subidos | `/data` |
| `JWT_SECRET` | Clave secreta para la firma de tokens JWT | *(auto-generada)* |
| `MAX_FILE_SIZE` | Tamaño máximo de subida para videos y archivos multimedia | `500MB` |
| `HEARTBEAT_TIMEOUT`| Tiempo sin reporte antes de marcar una pantalla como desconectada (ms) | `45000` (45 seg) |

---

## 5. Guía de Operación y Uso

### 5.1. Vincular un Televisor o Pantalla
1. Encienda el televisor o Smart TV y abra su navegador web.
2. Navegue a la dirección del reproductor:
   ```
   http://<IP_SERVIDOR>:3001/player
   ```
3. La pantalla mostrará un **código de emparejamiento de 6 dígitos** (ejemplo: `AB12CD`).
4. En su computadora, ingrese al panel de administración (`http://<IP_SERVIDOR>:3001`), vaya a la sección **Pantallas (Displays)**, pulse **Vincular Pantalla (+)** e ingrese el código.
5. Asigne un nombre descriptivo a la pantalla (por ejemplo: `PANTALLA PRINCIPAL` o `TELEVISOR LOBBY`).

### 5.2. Crear y Asignar Listas de Reproducción (Playlists)
1. En el menú lateral, diríjase a **Listas de Reproducción (Playlists)** y pulse **Crear Lista**.
2. Añada elementos arrastrando archivos multimedia (videos publicitarios, imágenes, avisos institucionales).
3. Establezca la duración de cada elemento y el orden de reproducción.
4. Asigne la lista de reproducción a la pantalla correspondiente o a un grupo completo de pantallas.

### 5.3. Configurar Widgets Web Interactivos (Webpages)
Para proyectar aplicaciones web, paneles o páginas dinámicas en un televisor:
1. En el panel de TV-Controler, vaya a **Widgets** y seleccione **Añadir Widget** → **Página Web (Webpage)**.
2. Ingrese la dirección URL del widget o servicio web a proyectar (por ejemplo: `http://192.168.1.50:8080/dashboard` o una URL pública).
3. Configure el intervalo de recarga deseado (o `0` si la aplicación web se actualiza por sí misma en tiempo real).
4. Guarde el widget e incorpórelo a la lista de reproducción (playlist) de la pantalla deseada.

---

## 6. Mantenimiento y Actualizaciones

### Actualizar la Instalación con Docker
Para actualizar a la última versión disponible en el repositorio de GitHub:

```bash
cd /opt/tv-controler  # o la ruta donde clonó el proyecto
git pull origin main
docker compose build --no-cache
docker compose up -d
```

### Respaldos de la Base de Datos y Medios
Toda la información operativa (usuarios, pantallas registradas, listas de reproducción y archivos multimedia) se conserva en el volumen Docker `st-data`:

- **Base de datos SQLite:** `/data/remote_display.db`
- **Archivos subidos:** `/data/uploads/`

Para crear una copia de seguridad rápida en caliente:
```bash
docker run --rm -v st-data:/data -v $(pwd):/backup alpine tar -czf /backup/backup_tv_controler_$(date +%Y%m%d).tar.gz /data
```

---

## 7. Estructura del Proyecto

```
TV-Controler/
├── server/                     # Backend Node.js / Express / Socket.IO
│   ├── db/                     # Base de datos SQLite y migraciones automáticas
│   ├── routes/                 # Rutas de API REST (/api/devices, /api/playlists, etc.)
│   ├── player/                 # Archivos estáticos del reproductor web (/player)
│   ├── certs/                  # Directorio para certificados SSL opcionales
│   └── server.js               # Punto de entrada principal del servidor
├── frontend/                   # Interfaz de usuario del panel administrativo (/app)
├── android/                    # Código fuente del reproductor nativo para Android TV
├── docker-compose.yml          # Orquestación de contenedores Docker
├── Dockerfile                  # Receta de compilación de la imagen de producción
└── README.md                   # Documentación oficial del sistema
```

---

## 8. Licencia

Proyecto de código abierto bajo licencia **MIT**. Puede ser utilizado, adaptado y distribuido libremente en entornos comerciales, corporativos o institucionales.
