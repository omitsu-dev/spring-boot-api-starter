![CI](https://github.com/omitsu-dev/spring-boot-api-starter/actions/workflows/ci.yml/badge.svg)

# spring-boot-api-starter

Spring Boot 3 + JWT authentication + PostgreSQL REST API starter. Register, login, and access protected endpoints with Bearer tokens.

Built by the developer behind [32blog.com](https://32blog.com).

## Features

- **Spring Boot 3.4** — Latest stable with Java 21
- **JWT authentication** — Stateless auth with jjwt 0.12
- **BCrypt password hashing** — Secure password storage
- **PostgreSQL + JPA** — Hibernate ORM with auto-schema generation
- **Bean Validation** — Request DTO validation with Jakarta Validation
- **Role-based access** — USER/ADMIN roles with method-level security
- **Global error handling** — Consistent JSON error responses
- **H2 for tests** — In-memory database for integration testing

## Quick Start

### Prerequisites

- Java 21+
- Maven 3.9+
- PostgreSQL (or Docker)

### 1. Start PostgreSQL

```bash
docker run -d --name postgres \
  -e POSTGRES_DB=mydb \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -p 5432:5432 \
  postgres:16
```

### 2. Run

```bash
./mvnw spring-boot:run
```

The API starts at `http://localhost:8080`.

## API Endpoints

### Public

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/auth/register` | Register a new user |
| `POST` | `/api/auth/login` | Login and receive JWT |

### Protected (requires `Authorization: Bearer <token>`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/me` | Get current user info |
| `GET` | `/api/health` | Health check |

## Usage Examples

### Register

```bash
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name": "John", "email": "john@example.com", "password": "password123"}'
```

Response:
```json
{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "email": "john@example.com",
  "name": "John"
}
```

### Login

```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "john@example.com", "password": "password123"}'
```

### Access Protected Endpoint

```bash
curl http://localhost:8080/api/me \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9..."
```

## Project Structure

```
src/main/java/com/example/api/
├── ApiApplication.java          # Entry point
├── config/
│   ├── SecurityConfig.java      # Spring Security + JWT filter chain
│   └── GlobalExceptionHandler.java  # Error response handler
├── controller/
│   ├── AuthController.java      # POST /register, /login
│   └── UserController.java      # GET /me, /health
├── dto/
│   ├── RegisterRequest.java     # Registration payload (validated)
│   ├── LoginRequest.java        # Login payload (validated)
│   ├── AuthResponse.java        # JWT + user info response
│   └── ErrorResponse.java       # Standardized error format
├── entity/
│   └── User.java                # JPA entity with USER/ADMIN roles
├── repository/
│   └── UserRepository.java      # Spring Data JPA repository
├── security/
│   ├── JwtProvider.java         # Token generation + validation
│   ├── JwtAuthFilter.java       # OncePerRequestFilter for JWT
│   └── CustomUserDetailsService.java  # UserDetailsService impl
└── service/
    └── AuthService.java         # Registration + login logic
```

## Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `DATABASE_URL` | `jdbc:postgresql://localhost:5432/mydb` | PostgreSQL connection URL |
| `DATABASE_USERNAME` | `postgres` | Database username |
| `DATABASE_PASSWORD` | `postgres` | Database password |
| `JWT_SECRET` | (dev default) | HMAC-SHA256 signing key (min 256 bits) |

### Security Notes

- Change `JWT_SECRET` in production (min 32 characters)
- Set `ddl-auto: validate` in production (use Flyway/Liquibase for migrations)
- Enable CORS if serving a frontend from a different origin

## Testing

```bash
./mvnw test
```

Tests use H2 in-memory database (see `application-test.yml`).

## Tech Stack

| Component | Version |
|-----------|---------|
| Spring Boot | 3.4.3 |
| Java | 21 |
| jjwt | 0.12.6 |
| PostgreSQL | 16 |
| H2 (test) | latest |

## License

[MIT](LICENSE)
