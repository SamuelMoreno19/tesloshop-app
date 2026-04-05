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

 Importante:
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

# Evidencia (Capturas)

Interfaz Principal de TesloShop
<img width="366" height="295" alt="Captura de pantalla 2026-04-04 173116" src="https://github.com/user-attachments/assets/615136c7-bf09-4c98-a699-58d23a3e3a69" />



Detalle de Producto y Categorias
<img width="408" height="851" alt="Captura de pantalla 2026-04-04 173453" src="https://github.com/user-attachments/assets/9f86aa9e-b3ab-480a-92f5-1e3adff11041" />



Panel de Administración - Gestión de Productos
<img width="1403" height="823" alt="Captura de pantalla 2026-04-04 173709" src="https://github.com/user-attachments/assets/97bec112-613b-403e-b2ff-fbaf330eca9c" />



Edición de Producto
<img width="898" height="865" alt="Captura de pantalla 2026-04-04 173746" src="https://github.com/user-attachments/assets/dc6424e4-3b76-49c4-b0b4-64854f9e8fae" />



Terminal: Docker Compose Up
<img width="1421" height="621" alt="Captura de pantalla 2026-04-04 174319" src="https://github.com/user-attachments/assets/5561edc8-bd30-4c70-964d-c731d84a7dca" />



Estado de Contenedores (Docker PS)
<img width="1288" height="828" alt="Captura de pantalla 2026-04-04 174335" src="https://github.com/user-attachments/assets/f59331b9-0094-4e99-b12a-db9cd41df4bf" />



Logs del Sistema en Tiempo Real
<img width="1284" height="352" alt="Captura de pantalla 2026-04-04 174354" src="https://github.com/user-attachments/assets/320bc674-acec-4908-a455-808dd2d561a9" />



Ejecución del Seed de Datos
<img width="1310" height="333" alt="Captura de pantalla 2026-04-04 175059" src="https://github.com/user-attachments/assets/68c7ffb6-1d04-4332-b7d6-1a1aca2d217d" />



Documentación de la API (Swagger)
<img width="1379" height="508" alt="Captura de pantalla 2026-04-04 175119" src="https://github.com/user-attachments/assets/73a61158-200f-407b-8957-dd2e864b0760" />



Estructura del Proyecto en VS Code
<img width="817" height="300" alt="Captura de pantalla 2026-04-04 180111" src="https://github.com/user-attachments/assets/d9073948-44a0-433a-aa2d-b159719161f0" />



Configuración del Dockerfile (Backend)
<img width="1401" height="365" alt="Captura de pantalla 2026-04-04 180122" src="https://github.com/user-attachments/assets/15c2a542-b963-4a25-b056-c58985ca5ba0" />



Configuración del Dockerfile (Frontend)
<img width="1386" height="421" alt="Captura de pantalla 2026-04-04 180131" src="https://github.com/user-attachments/assets/5008815c-0721-4ddc-b01a-048fccd85052" />



Definición de Servicios (Docker Compose)
<img width="1405" height="259" alt="Captura de pantalla 2026-04-04 180141" src="https://github.com/user-attachments/assets/e64854b9-0687-467f-a42e-1c52ba19f91d" />



Variables de Entorno (.env.example)
<img width="1459" height="268" alt="Captura de pantalla 2026-04-04 180150" src="https://github.com/user-attachments/assets/aab6070a-3d21-41c5-85f1-f214071bab33" />



Scripts de Automatización (Start/Stop)
<img width="1468" height="501" alt="Captura de pantalla 2026-04-04 180159" src="https://github.com/user-attachments/assets/1d4d8dc2-602e-47b1-97dc-d85f2921fc03" />



Entidades y Modelos de Datos
<img width="1379" height="570" alt="Captura de pantalla 2026-04-04 180209" src="https://github.com/user-attachments/assets/b06039c3-484b-41fd-b2f9-54cd3cbb07bf" />



Controladores y Rutas de la API
<img width="1370" height="518" alt="Captura de pantalla 2026-04-04 180218" src="https://github.com/user-attachments/assets/e0901b07-9acc-420c-b99d-8903e0a8225f" />



Servicios de Angular (Consumo de API)
<img width="1477" height="509" alt="Captura de pantalla 2026-04-04 180233" src="https://github.com/user-attachments/assets/830f2a11-ab45-4792-867a-94dc4ae3b199" />



Vista de Usuario Final (Catálogo)
<img width="1323" height="234" alt="Captura de pantalla 2026-04-04 180423" src="https://github.com/user-attachments/assets/441e1267-4419-40bd-8692-b92b55635a92" />



Configuración de Redes en Docker
<img width="1915" height="299" alt="Captura de pantalla 2026-04-04 190649" src="https://github.com/user-attachments/assets/64207033-17ed-4a30-ba04-e4185f71a6e9" />



Configuración de Redes en Docker
<img width="1919" height="1029" alt="Captura de pantalla 2026-04-04 190657" src="https://github.com/user-attachments/assets/32756f16-7b4a-4206-b431-afcf9a5962cb" />



Formulario de Registro de Usuarios
<img width="1919" height="960" alt="Captura de pantalla 2026-04-04 190708" src="https://github.com/user-attachments/assets/e8f63cd3-1bf0-45c4-be46-77ae38be2e0f" />



Pantalla de Inicio de Sesión (Login)
<img width="1919" height="1008" alt="Captura de pantalla 2026-04-04 190715" src="https://github.com/user-attachments/assets/067ab7c3-ab0e-485f-b727-b813c75abcc2" />



Validaciones de Seguridad (Frontend)
<img width="1918" height="1020" alt="Captura de pantalla 2026-04-04 190723" src="https://github.com/user-attachments/assets/07c8519b-037a-4d3f-b878-4623fd1c23be" />



Dashboard Administrativo de Usuarios
<img width="1333" height="200" alt="Captura de pantalla 2026-04-04 210802" src="https://github.com/user-attachments/assets/e43f3482-6931-4727-b555-c6443f8bf912" />



Logs de Autenticación (JWT)
<img width="1325" height="217" alt="Captura de pantalla 2026-04-04 210834" src="https://github.com/user-attachments/assets/858da8c6-908b-4536-a9bd-c82ad8ff197e" />







