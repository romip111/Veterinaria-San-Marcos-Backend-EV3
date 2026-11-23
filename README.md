# Veterinaria San Marcos – Backend (Spring Boot)

Backend de microservicios con API Gateway, una base de datos por servicio, autenticación JWT con roles y Swagger.

| Módulo | Puerto | Responsabilidad | Base de datos |
|---|---:|---|---|
| gateway | 8080 | Entrada única, rutas, CORS, Swagger agregado | — |
| usuarios | 8081 | Registro, login, JWT, refresh, CRUD de usuarios | sanmarcos_usuarios |
| catalogo | 8082 | CRUD de productos, precios y stock | sanmarcos_catalogo |
| pedidos | 8083 | Compras, historial y estados | sanmarcos_pedidos |
| citas | 8084 | Citas veterinarias y mensajes de contacto | sanmarcos_citas |
| common | — | Biblioteca compartida (errores, seguridad, OpenAPI) | — |

Cada servicio: `controller`, `service`, `repository`, `model`, `dto`, `config`, `exception`.

## Requisitos

JDK 17 o superior (probado con JDK 21). `JAVA_HOME` debe apuntar a ese JDK. Maven no se instala: se usa el wrapper.

## Compilar y probar

```powershell
.\mvnw.cmd verify        # compila y ejecuta las 37 pruebas de integración
```

## Ejecutar (perfil h2, sin instalar base de datos)

Cada servicio en una terminal, en este orden:

```powershell
.\mvnw.cmd -pl usuarios spring-boot:run
.\mvnw.cmd -pl catalogo spring-boot:run
.\mvnw.cmd -pl pedidos  spring-boot:run
.\mvnw.cmd -pl citas    spring-boot:run
.\mvnw.cmd -pl gateway  spring-boot:run
```

(Antes, una vez: `.\mvnw.cmd -DskipTests install` para instalar `common`.)

Swagger de todos los servicios: <http://localhost:8080/swagger-ui.html>

## Ejecutar con MySQL

1. Crear las bases: `mysql -u root -p < sql/01_crear_bases_mysql.sql`
2. Definir `SPRING_PROFILES_ACTIVE=mysql` y `DB_PASSWORD` (y si hace falta `DB_URL`, `DB_USER`) al iniciar cada servicio.

## Ejecutar con Docker (MySQL + todos los servicios)

```powershell
Copy-Item .env.example .env
docker compose up --build
```

API y Swagger en <http://localhost:8080/swagger-ui.html>. El frontend está en su propio repositorio y se conecta al Gateway (`VITE_API_URL=http://localhost:8080/api/v1`).

## Cuentas iniciales

| Correo | Contraseña | Rol |
|---|---|---|
| admin@sanmarcos.cl | Admin1234 | ADMIN |
| vendedor@sanmarcos.cl | Vende1234 | VENDEDOR |
| cliente@gmail.com | Cliente123 | CLIENTE |

Se crean solo la primera vez; las contraseñas se pueden cambiar con las variables
`ADMIN_PASSWORD`, `VENDEDOR_PASSWORD` y `CLIENTE_PASSWORD`.

## Variables de entorno principales

| Variable | Uso | Valor por defecto |
|---|---|---|
| `SPRING_PROFILES_ACTIVE` | `h2` o `mysql` | `h2` |
| `DB_URL`, `DB_USER`, `DB_PASSWORD` | Conexión MySQL de cada servicio | ver `application-mysql.yml` |
| `AUTH_JWKS_URL` | Dónde se obtiene la clave pública JWT | `http://localhost:8081/api/v1/auth/jwks` |
| `INTERNAL_API_KEY` | Clave entre pedidos y catálogo | valor local de ejemplo |
| `FRONTEND_ORIGINS` | Orígenes permitidos por CORS (gateway) | `http://localhost:5173,http://127.0.0.1:5173` |
| `KEYS_DIRECTORY` | Carpeta de las claves RSA (usuarios) | `./data/keys` |

No se suben a Git: `target/`, `data/` (bases H2 y claves RSA) ni `.env`.

# Veterinaria San Marcos — Backend (Microservicios)

Backend desarrollado con **Spring Boot** bajo una arquitectura de microservicios para la plataforma web **Veterinaria San Marcos**, desplegado en **AWS EC2** mediante contenedores Docker.

---

## 🌐 Enlaces de Producción (AWS)

* **API Gateway (Base URL):** `http://13.221.202.90:8080/api/v1`
* **Documentación Interactiva (Swagger UI):** `http://13.221.202.90:8080/swagger-ui/index.html`
* **Frontend Conectado (AWS S3):** `http://veterinaria-sanmarcos-front-ev3.s3-website-us-east-1.amazonaws.com`

---

## 🏗️ Arquitectura Desplegada

* **Infraestructura:** Instancia **AWS EC2** (`m7i-flex.large`, Ubuntu) en la región `us-east-1` con grupo de seguridad habilitado en el puerto `8080`.
* **Orquestación:** **Docker** y **Docker Compose** para el despliegue unificado de los servicios.
* **Servicios:**
  * **API Gateway:** Enrutamiento centralizado y control de políticas **CORS** hacia el bucket S3 del frontend.
  * **Microservicios Spring Boot:** Gestión de autenticación/usuarios, catálogo de productos/servicios, citas médicas, pedidos y mensajes.
  * **Base de Datos:** **MySQL** contenedorizado con persistencia en volúmenes Docker.

---

## ⚙️ Variables de Entorno (`.env`)

Para ejecutar el proyecto en un entorno local o en un nuevo servidor, crea un archivo `.env` en la raíz del backend (basado en `.env.example`) con las siguientes variables principales:

```env
MYSQL_ROOT_PASSWORD=<clave_root_mysql>
MYSQL_DATABASE=veterinaria_sanmarcos
JWT_SECRET=<clave_secreta_jwt>
FRONTEND_ORIGINS=[http://veterinaria-sanmarcos-front-ev3.s3-website-us-east-1.amazonaws.com](http://veterinaria-sanmarcos-front-ev3.s3-website-us-east-1.amazonaws.com)
GATEWAY_URL=[http://13.221.202.90:8080](http://13.221.202.90:8080)

---

## **🚀 Despliegue y Ejecución con Docker**
1. Clonar el repositorio e ingresar al directorio del backend.

2. Configurar el archivo .env con las credenciales correspondientes.

3. Construir y levantar los contenedores en segundo plano:

docker compose up -d --build

1. Verificar el estado de los contenedores:

docker compose ps



