# 🚀 Workflow and Tech Stack - Domus.club

## 🌐 Domain
**domus.club** – Management platform for residences and commercial spaces.

---

## 🧱 Tech Stack

### 🔹 Frontend
- **Expo (React Native)** for cross-platform mobile development.
- Integration with REST APIs.
- Push notifications support (via Firebase).
- State management: Context API or Redux (based on growth).

### 🔹 Backend
- **Spring Boot 3+**
- **Spring Cloud**
- **Spring Security** with JWT.
- **Eureka Service Discovery** for microservice registration.
- **Apache Kafka** for asynchronous communication.
- **PostgreSQL** as the main relational database.
- **Redis** for caching and session management.
- **Firebase** for push notifications.
- **Contabo** as server provider and S3-compatible storage.
- **Payment gateways:** CyberSource (BAC and Lafise under evaluation).
- **Docker** for containerization.
- **Prometheus + Grafana** for monitoring and metrics.
- **Swagger/OpenAPI** for API documentation.
- **Promtail/Loki** for logs.
- **Jenkins** for CI/CD (pipeline per microservice).

---

## 📁 Project Structure

### 🔸 Repositories (Multi-repo)
Each microservice has its own dedicated repository:

```
domus-backend-auth-service
domus-backend-user-service
domus-backend-access-control-service
domus-backend-visit-history-service
domus-backend-alert-service
domus-backend-communication-service
domus-backend-survey-service
domus-backend-ownership-service
domus-backend-reservation-service
domus-backend-finance-service
domus-backend-delinquency-tracker
domus-backend-financial-reports
domus-backend-support-ticketing
domus-backend-support-chat
domus-backend-knowledge-base
domus-backend-api-gateway
domus-frontend-mobile-app (Expo)
domus-docker
domus-database
domus-infra
```

---

## 🔁 Gitflow: Phases

### 🧩 Phase 1: Initial (feature → main)

```mermaid
gitGraph
    commit id: "Repository initialized"
    branch feature/description
    commit id: "Develop feature/description"
    checkout main
    merge feature/description tag: "PR → main"
```

**Best practices:**
- Each feature in its own branch: `feature/name`.
- Mandatory code reviews (PRs).
- CI runs tests before merging.
- Manual deploy after merging to `main`.

---

### 🧩 Phase 2: Standard Gitflow (develop → main)

```mermaid
gitGraph
    commit id: "Stable main"
    branch develop
    commit id: "Start develop"
    branch feature/description
    commit id: "Developing feature"
    checkout develop
    merge feature/description tag: "PR → develop"
    commit id: "More features"
    checkout main
    merge develop tag: "Release → main"
```

**Best practices:**
- All `feature/*` branches are merged into `develop`.
- `develop` is merged into `main` for releases.
- Semantic versioning tags: `v1.0.0`, `v1.1.0`.
- Automatic testing in every PR.

---

### 🧩 Phase 3: Testing and Production Environments

```mermaid
gitGraph
    commit id: "Stable main"
    branch develop
    commit id: "Start develop"
    branch feature/description
    commit id: "Develop feature"
    checkout develop
    merge feature/description tag: "PR → develop"
    commit id: "Prepare release"
    branch release/test
    commit id: "Deploy to test"
    checkout main
    merge release/test tag: "Final release → main"
```

**Environments:**
- **test.domus.club** (Staging environment).
- **domus.club** (Production).

**Best practices:**
- `release/test` is auto-deployed to the test environment.
- Manual validation (QA).
- Logs and monitoring must be accessible for debugging.

---

## 📛 Naming Conventions

| Type               | Convention                        | Example                              |
|--------------------|------------------------------------|--------------------------------------|
| Repository         | `domus-backend-<service-name>`           | `domus-backend-alert-service`              |
| Docker Image       | `domus/<service>:<build>`        | `domus/user-service:101`           |
| Jenkins Pipeline   | `CI-<service-name>`                | `CI-domus-backend-user-service`            |
| Staging Domain     | `test.domus.club`                 | `test-api.domus.club`               |
| Production Domain  | `domus.club`                      | `api.domus.club`                    |
