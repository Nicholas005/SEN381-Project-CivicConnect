# CivicConnect

Community service request management platform - built for SEN381 (Software Engineering 381).

CivicConnect replaces the organisation's fragmented email/phone/WhatsApp/spreadsheet process for logging and tracking community service requests (facility faults, maintenance issues, IT support, lost property, and similar) with a single controlled platform. Requesters submit and track requests, staff triage and resolve them, and management gets visibility into open/overdue/resolved work.

This repository contains both the backend (Java/Spring Boot REST API) and the frontend (React) as a single controlled monorepo. See `docs/PED/` for the full Project Engineering Document - requirements, architecture reasoning, risks, and decisions - this README documents the application as it stands, not the engineering rationale behind it.

## Current Implementation Status

**Backend**
- [x] Project bootstrapped with Spring Boot, Maven build configured
- [ ] PostgreSQL datasource connected, application context loads successfully
- [ ] Domain entities (User, ServiceRequest, Category, StatusHistory)
- [ ] Repository / service layers
- [ ] Controlled status-transition logic (State pattern - see ADR-ED-007)
- [ ] REST endpoints
- [ ] Spring Security / RBAC
- [ ] Automated tests beyond the default `contextLoads` smoke test

**Frontend**
- [ ] Project bootstrapped
- [ ] Routing / page structure
- [ ] API service layer
- [ ] Auth context
- [ ] Requester / staff / management pages

## Project Structure

```
civicconnect/
├── backend/
│   ├── pom.xml
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/civicconnect/
│   │   │   │   ├── CivicConnectApplication.java
│   │   │   │   ├── controller/
│   │   │   │   │   ├── ServiceRequestController.java
│   │   │   │   │   ├── UserController.java
│   │   │   │   │   └── CategoryController.java
│   │   │   │   ├── service/
│   │   │   │   │   ├── ServiceRequestService.java
│   │   │   │   │   ├── UserService.java
│   │   │   │   │   └── StatusTransitionService.java
│   │   │   │   ├── repository/
│   │   │   │   │   ├── ServiceRequestRepository.java
│   │   │   │   │   ├── UserRepository.java
│   │   │   │   │   ├── CategoryRepository.java
│   │   │   │   │   └── StatusHistoryRepository.java
│   │   │   │   ├── model/
│   │   │   │   │   ├── User.java
│   │   │   │   │   ├── ServiceRequest.java
│   │   │   │   │   ├── Category.java
│   │   │   │   │   └── StatusHistory.java
│   │   │   │   ├── dto/
│   │   │   │   │   ├── ServiceRequestDto.java
│   │   │   │   │   └── CreateRequestDto.java
│   │   │   │   ├── status/                  
│   │   │   │   │   ├── RequestState.java
│   │   │   │   │   ├── SubmittedState.java
│   │   │   │   │   ├── AssignedState.java
│   │   │   │   │   ├── InProgressState.java
│   │   │   │   │   ├── ResolvedState.java
│   │   │   │   │   └── ClosedState.java
│   │   │   │   ├── security/
│   │   │   │   │   ├── SecurityConfig.java
│   │   │   │   │   └── JwtAuthFilter.java
│   │   │   │   ├── exception/
│   │   │   │   │   ├── GlobalExceptionHandler.java
│   │   │   │   │   └── InvalidStatusTransitionException.java
│   │   │   │   └── config/
│   │   │   │       └── AppConfig.java
│   │   │   └── resources/
│   │   │       ├── application.properties
│   │   │       ├── application-dev.properties
│   │   └── test/
│   │       └── java/com/civicconnect/
│   │           ├── service/
│   │           │   └── StatusTransitionServiceTest.java
│   │           └── controller/
│   │               └── ServiceRequestControllerTest.java
│   └── README.md
├── frontend/
│   └── (React app, structure below)
├── docs/
│   └── milestone2_doc/
├── .gitignore
└── README.md

frontend/
├── package.json
├── vite.config.js 
├── public/
│   └── index.html
├── src/
│   ├── main.jsx
│   ├── App.jsx
│   ├── pages/
│   │   ├── requester/
│   │   │   ├── SubmitRequestPage.jsx
│   │   │   ├── MyRequestsPage.jsx
│   │   │   └── RequestDetailPage.jsx
│   │   ├── staff/
│   │   │   ├── RequestQueuePage.jsx
│   │   │   └── RequestDetailPage.jsx
│   │   ├── management/
│   │   │   └── DashboardPage.jsx
│   │   └── auth/
│   │       ├── LoginPage.jsx
│   │       └── RegisterPage.jsx
│   ├── components/
│   │   ├── common/
│   │   │   ├── Button.jsx
│   │   │   ├── StatusBadge.jsx
│   │   │   └── LoadingSpinner.jsx
│   │   ├── requests/
│   │   │   ├── RequestCard.jsx
│   │   │   ├── RequestForm.jsx
│   │   │   ├── RequestFilterBar.jsx
│   │   │   └── StatusHistoryTimeline.jsx
│   │   └── layout/
│   │       ├── Navbar.jsx
│   │       └── Sidebar.jsx
│   ├── services/
│   │   ├── api.js                  
│   │   ├── requestService.js       
│   │   ├── userService.js
│   │   └── authService.js
│   ├── hooks/
│   │   ├── useAuth.js
│   │   └── useRequests.js
│   ├── context/
│   │   └── AuthContext.jsx
│   ├── utils/
│   │   ├── statusLabels.js         
│   │   └── dateFormat.js
│   └── styles/
│       └── index.css 
```

Backend and frontend each have their own README with setup detail specific to that side - this top-level README is the entry point that ties both together.

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java 21, Spring Boot 4.1.1, Spring Data JPA, Spring Security |
| Database | PostgreSQL 15+ |
| Frontend | React |
| Build tools | Maven (backend), npm/yarn (frontend) |

Full rationale and alternatives considered: ADR-M2-01 (`docs/decisions/`).

## Getting Started

You'll need both the backend and frontend running for the full application to work.

### 1. Start PostgreSQL

```bash
docker run --name civicconnect-db \
  -e POSTGRES_PASSWORD=yourpassword \
  -e POSTGRES_DB=civicconnect \
  -p 5432:5432 \
  -d postgres
```

Or install PostgreSQL locally and create a `civicconnect` database.

### 2. Run the backend

```bash
cd backend
cp src/main/resources/application-example.properties src/main/resources/application-dev.properties
# edit application-dev.properties with your local DB credentials - this file is gitignored
mvn clean install
mvn spring-boot:run
```

Backend runs at `http://localhost:8080` by default.

See `backend/README.md` for full backend-specific detail.

### 3. Run the frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend runs at `http://localhost:5173` (or whatever port your dev server uses) and talks to the backend API.

See `frontend/README.md` for full frontend-specific detail.

## API

The frontend consumes the backend as a REST API. Endpoints are documented in `backend/README.md` and updated as they're implemented - none are built yet, so there's nothing to list here.

## Environment & Configuration

- Local database credentials live in `backend/src/main/resources/application-dev.properties`, which is gitignored and must never be committed.
- Frontend environment variables (e.g. the backend API base URL) belong in `frontend/.env.local`, also gitignored.
- No passwords, API keys, or connection strings are committed anywhere in this repository - see the Master Project Brief's GitHub Governance standard.

## Design Decisions Worth Knowing

- **Status transitions** (`backend/.../status/`): implemented using the State design pattern so each request status knows its own valid next transitions, rather than scattering validation logic across service methods. See ADR-ED-007.
- Further design-pattern documentation will be added here as each is implemented, per the project's second required design decision.

## Known Limitations / TODOs

- No authentication/authorization implemented yet
- No API endpoints implemented yet - backend domain/repository layers are the current priority
- `ddl-auto=update` is a development convenience only and must be replaced with proper migrations before any real deployment
- Frontend project not yet bootstrapped
- Test coverage is currently limited to the default Spring Boot context-load smoke test

## Related Documentation

- Project Engineering Document (PED): `docs/PED/`
- Requirements Traceability Matrix (RTM): `docs/requirements/`
- Architecture Decision Records: `docs/decisions/`
- Risk Register: `docs/risk/`
- GitHub governance standards: see the SEN381 Master Project Brief

## Team

CivicConnect is built as a 3-person team project for SEN381. See the PED's sign-off page for team member names and roles.
