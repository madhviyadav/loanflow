# LoanFlow — Microservices Loan Origination System

A learning project demonstrating microservices architecture, event-driven
design, and cloud-native deployment patterns for banking systems.

**Status:** Active development. Currently building service 1 of 6.

## Services

| Service | Status | Description |
|---|---|---|
| customer-service | ✅ REST API working | Customer profile management |
| loan-application-service | Planned | Loan applications and status |
| credit-check-service | Planned | Async credit scoring via Kafka |
| notification-service | Planned | Email/SMS notifications |
| api-gateway | Planned | Single entry point with JWT auth |
| discovery-server | Planned | Service registry (Eureka) |

## Tech Stack

- Java 17, Spring Boot 3, Spring Data JPA, Hibernate
- PostgreSQL 16 (containerized via Docker)
- Spring Boot Actuator for health/metrics
- Lombok, Bean Validation, JUnit 5 (planned)
- Docker Compose for orchestration (planned)
- Apache Kafka for event-driven flows (planned)
- AWS deployment (planned)

## customer-service

Working REST API with three endpoints:

- `POST /api/v1/customers` — create customer with validation, returns 201 + Location header
- `GET /api/v1/customers/{id}` — fetch customer by UUID, returns 404 if not found
- `GET /api/v1/customers?page=0&size=20&sort=createdAt,desc` — paginated list

### Architectural choices in customer-service

- **UUID primary keys** for distributed system safety (no ID collisions across services)
- **DTO/Entity separation** to prevent mass assignment vulnerabilities and decouple API contract from schema
- **`@RestControllerAdvice` global exception handler** for consistent JSON error responses without leaking stack traces
- **Defense-in-depth uniqueness**: unique constraint at DB level + application-level check for clean error messages
- **Constructor injection over field injection** for testability and immutability
- **Transactional defaults**: `readOnly = true` at class level, opt-in for writes
- **Hibernate-managed audit timestamps** (`@CreationTimestamp`, `@UpdateTimestamp`) for compliance

## Running Locally

```bash
# Start PostgreSQL
docker run --name loanflow-postgres -e POSTGRES_USER=loanflow \
  -e POSTGRES_PASSWORD=loanflow -e POSTGRES_DB=customers \
  -p 5432:5432 -d postgres:16

# Run customer-service
cd customer-service
./mvnw spring-boot:run

# App runs on http://localhost:8081
# Health check: http://localhost:8081/actuator/health
```
