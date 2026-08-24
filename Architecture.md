# System Architecture & Code Blueprint

## 1. High-Level Architecture
The system transitions from the legacy Python/FastAPI ML stack to an enterprise-grade **Java Spring Boot + Spring AI** backend, complemented by a modern **Next.js (React/TypeScript)** frontend.

### Workflow:
1. **Data Source:** Mock transaction payload generated and streamed from the frontend.
2. **Ingestion & Persistence:** Handled via Spring Boot REST controllers and persisted via Spring Data JPA.
3. **Processing (Feature Engineering):** Java backend processes the transaction history.
4. **Analytics (Spring AI & Agents):** Intelligent agents evaluate the gap using probabilistic scoring (Probability Score x Entitlement Value = Unclaimed $ Gap).
5. **Action:** Backend triggers personalized JSON payloads back to the Next.js React Dashboard via WebSockets or SSE for live UI updates.

---

## 2. Code Blueprint & Project Structure

### 2.1 Backend (Java / Spring Boot / Maven)
**Tech Stack:** Java 17+, Spring Boot 3, Spring Web, Spring Data JPA, Spring AI, PostgreSQL, Maven.

#### Directory Structure:
```text
benefitiq-backend/
├── pom.xml
├── Dockerfile
└── src/main/java/com/benefitiq/
    ├── BenefitIqApplication.java
    ├── config/           # App configuration, Spring AI Config, Security, WebSockets
    ├── controller/       # REST APIs (TransactionController, DashboardController)
    ├── service/          # Business logic (ClassificationService, NotificationService)
    ├── agent/            # Spring AI Agent implementations (PredictiveGapAgent, NudgeAgent)
    ├── dao/ (or repo/)   # Spring Data JPA Repositories (TransactionRepository, BenefitRepository)
    ├── pojo/ (or model/) # Entities and DTOs (Transaction, BenefitRule, GapScore)
    └── util/             # Helpers, Constants
```

#### Component Details:
- **POJOs / Entities:** 
  - `Transaction.java`: Represents an incoming swipe.
  - `Benefit.java`: Represents a cardholder's available benefit.
  - `GapScore.java`: Represents the computed unclaimed value.
- **DAOs / Repositories:** Interfaces extending `JpaRepository` for database interactions.
- **Spring AI Agents:** 
  - `ScoringAgent`: Uses Spring AI to interact with ML models (or LLMs) to predict category frequency and spend recency.
  - `ContextTriggerAgent`: Evaluates geospatial and temporal rules (Location, Expiry) to fire nudges.
- **JUnit 5:** Located in `src/test/java/`. Contains unit tests for Services and integration tests for AI Agents.

### 2.2 Frontend (Next.js / React / TypeScript)
**Tech Stack:** Next.js (App Router), React, TypeScript, Tailwind CSS, Recharts (for Heatmaps/Dashboards).

#### Directory Structure:
```text
benefitiq-frontend/
├── package.json
├── tsconfig.json
├── Dockerfile
├── next.config.js
└── src/
    ├── app/              # Next.js App Router (Dashboard, Login, Settings pages)
    ├── components/       # Reusable UI (HeatmapChart, MetricCard, NudgeAlert)
    ├── hooks/            # Custom React hooks (useTransactions, useGapScore)
    ├── services/         # API clients to communicate with Spring Boot backend
    └── types/            # TypeScript interfaces matching Backend DTOs
```

### 2.3 DevOps & Containerization
- **Backend Dockerfile:** Multi-stage build using a Maven image to build, and a lightweight OpenJDK image to run the `.jar`.
- **Frontend Dockerfile:** Multi-stage build using Node.js to build the Next.js static files and run the Next server.
- **docker-compose.yml:** Orchestrates the Backend, Frontend, and a PostgreSQL database container for seamless local development.

---

## 3. Demo Execution Flow
1. **Step 1:** Next.js dashboard sends a mock transaction payload to the Spring Boot `/api/transactions/ingest` endpoint.
2. **Step 2:** Spring AI Agent (`ScoringAgent`) analyzes the payload and identifies a $120 dining gap.
3. **Step 3:** Spring Boot backend evaluates context (location, expiry) via `ContextTriggerAgent` and generates a personalized push payload.
4. **Step 4:** The React UI dashboard receives the gap score and trigger data via SSE/WebSocket, updating the heatmaps and ROI tracking live.
