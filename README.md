# 📝 To-Do List App con Docker Compose

## 🚀 Descripción

Aplicación completa de gestión de tareas (To-Do List) construida con una arquitectura de tres contenedores independientes: frontend en Nginx, backend en Node.js con Express y base de datos PostgreSQL. Todo gestionado mediante Docker Compose para facilitar el despliegue y desarrollo.



## 🏗️ Estructura del proyecto


trabajo_docker_Cecilia/
├── backend/           
│   ├── server.js      
│   ├── package.json
│   └── Dockerfile
├── frontend/          
│   ├── index.html     
│   ├── app.js        
│   ├── style.css      
│   └── Dockerfile
├── docker-compose.yml 
├── .env.example       
└── README.md




## 🔧 Servicios

### 🌐 Frontend (Nginx)
- **Qué hace:** Sirve la interfaz HTML/CSS/JS donde el usuario gestiona sus tareas. Realiza peticiones HTTP al backend para crear, leer, actualizar y eliminar tareas.
- **Puerto:** 8080 (mapeado al 80 del contenedor)
- **Volumen:** Bind mount `./frontend:/usr/share/nginx/html` para ver cambios en tiempo real sin reconstruir el contenedor durante el desarrollo.

### 🔌 Backend (Node.js + Express)
- **Qué hace:** Expone una API REST con endpoints para gestionar las tareas (`GET /tasks`, `POST /tasks`, `PATCH /tasks/:id`, `DELETE /tasks/:id`). Al iniciar crea automáticamente la tabla `tasks` en PostgreSQL si no existe.
- **Puerto:** 3000
- **Variables de entorno:** Usa `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD` y `DB_NAME` para conectarse a PostgreSQL de forma segura.
- **Características:** Pool de conexiones, CORS habilitado para el frontend y manejo de errores en todas las rutas.

### 🗄️ Base de datos (PostgreSQL)
- **Qué hace:** Almacena persistentemente todas las tareas del usuario con su título, estado (completada/no completada) y fecha de creación.
- **Puerto:** 5432 (solo accesible desde la red interna de Docker)
- **Volumen:** Volumen gestionado `postgres_data` para que los datos persistan aunque se detenga o elimine el contenedor.



## 🌐 Red personalizada

Los tres servicios se comunican a través de una red Docker llamada `todo-network`. Esta red permite:

- Que el frontend (puerto 8080) hable con el backend mediante `http://backend:3000`
- Que el backend se conecte a PostgreSQL usando el hostname `postgres` (nombre del servicio en el compose)
- Aislar la base de datos del exterior: PostgreSQL no es accesible desde localhost, solo desde dentro de la red Docker

Esto mejora la seguridad y simplifica la configuración (no necesito IPs, Docker resuelve los nombres automáticamente).



## 💾 Volúmenes: gestionado vs bind mount

| Tipo | Servicio | Ruta | Propósito |
|------|----------|------|-----------|
| **Bind mount** | Frontend | `./frontend:/usr/share/nginx/html` | Sincroniza la carpeta local con el contenedor. Al editar un archivo en mi PC, los cambios se ven al instante en el navegador sin reconstruir nada. Ideal para desarrollo. |
| **Volumen gestionado** | PostgreSQL | `postgres_data:/var/lib/postgresql/data` | Docker gestiona este volumen internamente. Los datos de la BD se guardan fuera del contenedor y sobreviven a `docker compose down`. Es la forma recomendada para persistencia en producción. |



## 📊 Puertos expuestos

| Servicio | Puerto host | Puerto contenedor | Acceso |
|----------|-------------|-------------------|--------|
| Frontend | 8080 | 80 | Público (localhost:8080) |
| Backend | 3000 | 3000 | Público (localhost:3000) |
| PostgreSQL | 5432 | 5432 | Solo interno (red Docker) |



## ▶️ Cómo ejecutar

# Levantar todo (con reconstrucción de imágenes)
docker compose up -d --build

# Acceder a la app: http://localhost:8080

# Ver logs en tiempo real
docker compose logs -f

# Detener los contenedores
docker compose down

# Detener y eliminar volúmenes (¡cuidado, borra los datos!)
docker compose down -v



*Proyecto realizado por Cecilia Fernández Oliveros - Curso 2025/26*