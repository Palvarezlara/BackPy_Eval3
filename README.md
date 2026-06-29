# Backend Products Service — Python Flask

Servicio REST para la gestión de productos, desarrollado con Python y Flask. Forma parte del proyecto **Innovatech Chile** desplegado en AWS ECS.

## Tecnologías

- **Runtime:** Python 3.11
- **Framework:** Flask 3.0
- **Base de datos:** MySQL 8.0
- **Contenedor:** Docker (imagen base `python:3.11-slim`)
- **CI/CD:** GitHub Actions → Amazon ECR → Amazon ECS

## Endpoints

| Método | Ruta | Descripción |
|---|---|---|
| GET | `/api/products` | Obtener todos los productos |
| GET | `/api/products/:id` | Obtener producto por ID |
| POST | `/api/products` | Crear nuevo producto |
| PUT | `/api/products/:id` | Actualizar producto |
| DELETE | `/api/products/:id` | Eliminar producto |
| GET | `/api/products/search` | Buscar por nombre, precio o stock |

## Variables de entorno

```env
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=tu_password
DB_NAME=products_db
PORT=8082
```

## Ejecución local (sin Docker)

```bash
pip install -r requirements.txt
cp .env.example .env
# edita .env con tus credenciales
python app.py
```

## Ejecución con Docker

```bash
docker build -t backend-products .
docker run -p 8082:8082 --env-file .env backend-products
```

## Ejecución completa con Docker Compose

Desde la carpeta raíz del proyecto:

```bash
docker-compose up --build
```

El servicio estará disponible en `http://localhost:8082`

## Pipeline CI/CD

Cada `push` a la rama `main` dispara el workflow `.github/workflows/deploy.yml` que:

1. **Test** — ejecuta las pruebas disponibles
2. **Build** — construye la imagen Docker
3. **Push** — publica la imagen en Amazon ECR con tag del commit
4. **Deploy** — actualiza el servicio en Amazon ECS Fargate

Los secretos de AWS y base de datos se gestionan mediante **GitHub Secrets**, sin exposición en el código.

## Arquitectura en la nube

El servicio corre en **Amazon ECS Fargate** dentro de una VPC privada, detrás de un **Application Load Balancer** en el puerto 8082. Los logs se envían automáticamente a **Amazon CloudWatch** en el grupo `/ecs/backend-products`.
