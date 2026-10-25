# Laboratorio S09 - Autenticacion JWT con Spring Boot

API REST con registro e inicio de sesion, tokens JWT y autorizacion por roles (Spring Boot 3.5, Spring Security, Spring Data JPA, MySQL y JJWT 0.11.5).

## Requisitos

- Java 21
- MySQL en `localhost:3306` con usuario `root` sin contrasena (por ejemplo, el de XAMPP)

## 1. Crear la base de datos

```sql
CREATE DATABASE IF NOT EXISTS security_db;
```

## 2. Ejecutar la aplicacion

```
./mvnw spring-boot:run
```

En el primer arranque Hibernate crea las tablas `roles`, `users` y `user_roles`.

## 3. Insertar los roles

Con la aplicacion ya ejecutada (las tablas deben existir):

```sql
INSERT INTO security_db.roles (name) VALUES ('USER'), ('ADMIN'), ('MODERATOR');
```

## 4. Endpoints

| Metodo | Ruta | Acceso |
|--------|------|--------|
| POST | `/api/auth/signup` | Publico |
| POST | `/api/auth/signin` | Publico |
| GET | `/api/test/all` | Publico |
| GET | `/api/test/user` | Rol USER o ADMIN |
| GET | `/api/test/admin` | Rol ADMIN |

### Registro

```
curl -X POST http://localhost:8080/api/auth/signup -H "Content-Type: application/json" \
  -d '{"username":"admin","email":"admin@test.com","password":"123456","roles":["ADMIN"]}'

curl -X POST http://localhost:8080/api/auth/signup -H "Content-Type: application/json" \
  -d '{"username":"user1","email":"user@test.com","password":"123456","roles":["USER"]}'
```

Respuesta: `{"message":"Usuario registrado correctamente"}`

### Inicio de sesion

```
curl -X POST http://localhost:8080/api/auth/signin -H "Content-Type: application/json" \
  -d '{"email":"admin@test.com","password":"123456"}'
```

La respuesta incluye `accessToken`, `tokenType`, `id`, `username`, `email` y `roles`.

### Rutas protegidas

Enviar el token en la cabecera `Authorization`:

```
curl http://localhost:8080/api/test/user  -H "Authorization: Bearer <accessToken>"
curl http://localhost:8080/api/test/admin -H "Authorization: Bearer <accessToken>"
```

Sin token, con un token alterado o con un rol insuficiente la API responde `403`.
