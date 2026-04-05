# tesloshop-app
TesloShop – Implementación con Docker
Descripción del proyecto

Este proyecto consiste en la implementación de una aplicación web completa utilizando Docker. La aplicación está dividida en varias partes (frontend, backend y base de datos), y cada una se ejecuta en su propio contenedor.

El objetivo de usar Docker es facilitar:

La ejecución del proyecto en cualquier máquina
Evitar problemas de dependencias
Mantener todo organizado y aislado
Poder levantar todo el sistema con un solo comando

Arquitectura del sistema

# La aplicación sigue una arquitectura de 3 capas:

Frontend (Angular) → lo que ve el usuario
Backend (NestJS) → lógica del sistema
Base de datos (PostgreSQL) → almacenamiento
"Usuario → Frontend → Backend → Base de Datos"

Explicación paso a paso:
- El usuario entra a la aplicación desde el navegador
- El frontend muestra la interfaz
- Cuando el usuario hace una acción, el frontend envía una petición al backend
- El backend procesa la información
- Si necesita datos, consulta la base de datos
- La base de datos responde
- El backend envía la respuesta al frontend
- El frontend actualiza la vista

# Arquitectura en Docker

Cada parte del sistema es un contenedor:

Servicio - Tecnología - Función
frontend - Angular + Nginx - Interfaz
backend	- NestJS - API
db - PostgreSQL - Base de datos

Todos se conectan por una red interna de Docker, lo que permite que se comuniquen usando nombres como db en vez de localhost.

# Estructura del proyecto

El proyecto está organizado así:
tesloshop/
│
├── docker-compose.yml
├── .env
├── .env.example
├── start.sh
├── stop.sh
│
├── teslo-shop/ (backend)
│   ├── Dockerfile
│   └── src/
│
├── angular-tesloshop/ (frontend)
│   ├── Dockerfile
│   ├── nginx.conf
│   └── src/

# Pasos de ejecución (explicados bien)
 1. Verificar Docker
 docker --version
docker compose version
Si falla, significa que no está instalado o no está corriendo.

2. Crear archivo de entorno
cp .env.example .env
Luego editar:

POSTGRES_PASSWORD
DB_PASSWORD
JWT_SECRET

👉 Importante:
El .env NO se sube al repositorio.

3. Dar permisos a scripts
chmod +x start.sh stop.sh

4. Levantar el proyecto
Opción fácil:
./start.sh
Opción manual:
docker compose up --build -d

¿Qué hace este comando?
Construye imágenes Docker
Descarga imágenes base (Node, Postgres, Nginx)
Crea contenedores
Configura red interna
Inicia todos los servicios

5. Verificar estado
docker compose ps

Debe mostrar:

db → healthy
backend → running
frontend → running

6. Acceder a la app
Frontend → http://localhost
Backend → http://localhost:3000/api

7. Ejecutar seed (MUY importante)

Primera vez la DB está vacía.

Ejecutar: http://localhost:3000/api/seed
Esto: Borra datos previos, Inserta datos de prueba

8. Ver logs (si algo falla)
docker compose logs -f

O por servicio:

docker compose logs -f backend

9. Detener el sistema
docker compose down

10. Resetear todo
docker compose down -v
docker compose up --build -d

Esto borra la base de datos completamente.

# Explicación de servicios
- Base de datos (db)
Usa PostgreSQL
Guarda datos de usuarios, productos, etc.
Usa volumen para persistencia

Sin volumen, los datos se perderían al apagar el contenedor.

- Backend (NestJS)
API REST
Maneja lógica del sistema
Conecta con la base de datos

Cosas importantes:

Usa variables de entorno
Depende de la DB (depends_on)
Tiene hot-reload en desarrollo
Frontend (Angular + Nginx)
Angular se compila
Nginx sirve los archivos

Funciones:

Mostrar interfaz
Manejar rutas (SPA)
Enviar peticiones al backend

Comunicación entre servicios

No se usa localhost entre contenedores.

Ejemplo correcto:

DB_HOST=db

Porque db es el nombre del servicio en Docker.

# Dockerfile (idea general)
- Backend
Usa Node
Tiene múltiples etapas (dev y prod)
Permite hot-reload en desarrollo

 Ventaja:
Una sola configuración sirve para desarrollo y producción.

- Frontend
Primera etapa → build Angular
Segunda etapa → Nginx

Ventaja:

Imagen más ligera
No incluye Node en producción
Nginx (muy importante)

Se encarga de:

Servir la aplicación Angular
Redirigir /api al backend
Evitar problemas de CORS

Gracias al proxy:
El navegador cree que todo viene del mismo servidor.

# Conclusión

El uso de Docker permite ejecutar una aplicación completa de forma organizada, evitando problemas de entorno y facilitando su despliegue.

Además, con Docker Compose se logra levantar todo el sistema con un solo comando, lo cual simplifica mucho el proceso de desarrollo.

