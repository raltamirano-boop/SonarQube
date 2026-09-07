# 🛡️ SonarQube - Plataforma de Calidad y Seguridad Continua

Este repositorio contiene la configuración local mediante **Docker Compose** para desplegar una instancia de **SonarQube** (Community Edition), su base de datos en PostgreSQL, y los pasos necesarios para conectarlo a un flujo CI/CD (GitHub Actions).

Además, se incluye una **Presentación Ejecutiva** interactiva lista para usarse con directivos (archivo HTML).

---

## 📋 Requisitos Previos

1. **Docker Desktop** instalado y corriendo en tu máquina.
2. Opcional: **Ngrok** (si deseas exponer tu instancia local a la nube de manera temporal para que GitHub Actions pueda acceder a ella).

---

## 🚀 Instalación y Arranque Rápido

1. Abre una terminal en esta carpeta.
2. Levanta los servicios ejecutando:
   ```bash
   docker-compose up -d
   ```
3. Espera un par de minutos a que el servidor de SonarQube termine de inicializar.
4. Accede al panel de SonarQube desde tu navegador en:
   👉 **http://localhost:9000**

### Credenciales por defecto:
- **Usuario:** `admin`
- **Contraseña:** `admin`
*(El sistema te pedirá cambiar la contraseña en el primer inicio de sesión).*

---

## 🗄️ Estructura de Servicios (Docker Compose)

El archivo `docker-compose.yml` despliega dos contenedores orquestados:

- `sonarqube_prueba`: Servidor principal de análisis (Puerto 9000).
- `postgres_sonar`: Base de datos PostgreSQL v15 para almacenar los análisis, usuarios y reglas (Puerto 5432 expuesto internamente).

> **Nota de persistencia:** Se creó un volumen llamado `sonar_db_data` para evitar la pérdida de los análisis e historial cuando se apague el contenedor.

---

## ☁️ Exposición Local hacia GitHub Actions (Uso de Ngrok)

Si estás trabajando en local y necesitas que GitHub Actions mande sus reportes a tu máquina, necesitas exponer el puerto `9000`:

```bash
ngrok http 9000
```
Copia el enlace `https://xxxx.ngrok-free.app` que te arroje la consola; este será tu `SONAR_HOST_URL` para GitHub.

---

## 🔗 Integración con GitHub Actions (CI/CD)

Para que tus repositorios analicen el código automáticamente en cada commit:

1. **Generar Token en SonarQube:**
   - Ve a `My Account > Security`.
   - Genera un nuevo Token de tipo `User Token` (o `Global Analysis Token`). Cópialo.

2. **Configurar Secrets en GitHub:**
   En tu repositorio de código en GitHub, ve a `Settings > Secrets and variables > Actions` y crea:
   - `SONAR_TOKEN`: Pega aquí el token generado en SonarQube.
   - `SONAR_HOST_URL`: Pega la URL de ngrok (ej. `https://xxxx.ngrok-free.app`).

3. **Ejemplo básico de Workflow (`.github/workflows/build.yml`):**

```yaml
name: SonarQube Scan
on:
  push:
    branches:
      - main
jobs:
  build:
    name: Analizar Código
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # SonarQube necesita todo el historial
      - name: SonarQube Scan
        uses: SonarSource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
```

---

## 📊 Presentación Ejecutiva (Para Directivos)

En esta misma carpeta encontrarás el archivo:
📄 **`Presentacion_Directivos_SonarQube.html`**

Este archivo es una presentación animada e interactiva (creada con HTML/Reveal.js) diseñada para convencer y explicar a la Dirección/Gerencia el por qué de la implementación de esta herramienta.

- **Cómo abrirlo:** Solo dale doble clic para abrirlo en Chrome/Edge.
- **Contenido:** Aborda la disminución de deuda técnica, seguridad desde el diseño, Retorno de Inversión (ROI), el flujo del Quality Gate, e integra métricas y capturas directas del sistema.

---
*Mantenimiento: ODM (Infraestructura y Calidad de Código)*
