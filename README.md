# Design Factory: backend

REST API for **Design Factory**, a web platform for learning software design patterns, built as my MBA capstone project (TCC) in Software Engineering at USP/ESALQ.

It stores student accounts, tracks progress through each learning track, awards experience points and issues the course certificate. The front end lives in [projeto-tcc-next](https://github.com/guerrasz/projeto-tcc-next).

## Stack

- Java 24 and Spring Boot (Web, Data JPA)
- PostgreSQL 17
- Lombok
- Docker and Docker Compose

## Architecture

```
Next.js front end  ──HTTP──▶  Spring Boot API  ──JPA──▶  PostgreSQL
     :3000                         :8080                    :5432
```

The code follows a standard layered layout:

```
src/main/java/com/lucas/designfactorybackend/
├── controllers/   StudentController: REST endpoints
├── services/      StudentService: login, progress and certificate rules
├── repositories/  StudentRepository: Spring Data JPA
├── models/        Student entity
└── CorsConfig.java
```

## API

All endpoints are under `/student`.

| Method | Path | Description |
| --- | --- | --- |
| `GET` | `/student` | List all students |
| `GET` | `/student/{id}` | Get a student by ID |
| `GET` | `/student/email/{email}` | Get a student by email |
| `POST` | `/student` | Create a student (sign-up) |
| `PUT` | `/student` | Update a student |
| `DELETE` | `/student/delete/{id}` | Delete a student |
| `POST` | `/student/login` | Log in with `{ "email", "password" }`; returns the student ID |
| `POST` | `/student/class/{category}` | Mark the next lesson of track `category` (1 to 4) as complete for `{ "id" }`; returns the new progress |
| `POST` | `/student/certificate` | Issue the certificate for `{ "id" }`; returns `true` if it was issued |

Completing a lesson also adds one experience point to the student.

## Running locally

### Everything with Docker Compose

`compose.yml` starts PostgreSQL, this API and the front end from published images:

```bash
docker compose up
```

- Front end: http://localhost:3000
- API: http://localhost:8080

### API from source

Start only the database, then run the app with Maven:

```bash
docker compose up -d db

export DB_HOST=localhost DB_USER=dev DB_PASS=example
export SPRING_DOCKER_COMPOSE_ENABLED=false   # keep Spring from starting the whole compose file
./mvnw spring-boot:run
```

The schema is created automatically on startup (`spring.jpa.hibernate.ddl-auto=create-only`).

### Configuration

| Variable | Description |
| --- | --- |
| `DB_HOST` | PostgreSQL host (the database name is `dev`, port `5432`) |
| `DB_USER` | Database user |
| `DB_PASS` | Database password |

## Building the image

```bash
./mvnw clean package
docker build -t spring-designfactory .
```
