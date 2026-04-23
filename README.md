# Kunzoick
**Java Backend Developer · Systems Thinker · Production-Minded Builder**

> I love writing code that I can explain — every decision, every tradeoff, every boundary.

---

## How I Build

I always start with the architecture before the code. I try to document what I rejected and possibly why. I treat failure as a feature, not an edge case. I also write on the system limitations.

Every project I build has a design document. Every design decision has a reason.

---

## Projects

### Student Management System
**Java 17 · Swing · JDBC · MySQL**

A layered desktop application managing the full academic lifecycle of students across a multi-admin institution. No Spring. No ORM. Every dependency is wired manually — intentionally.

Four strict layers: UI → Service → DAO → Database. SwingWorker threading contract enforced across 20 frames. Multi-tenant data isolation enforced at the SQL level on every query.

Went through a full code review: 15 critical bugs and 8 high-severity issues identified and fixed. Every issue is documented with the root cause and the fix applied.

**What makes it worth reading:**
- Written justification for why no Spring, no Hibernate, no ORM
- SwingWorker contract enforced — the EDT never blocks
- adminId sentinel pattern eliminates an entire class of security bugs
- Design tradeoffs documented honestly, including what is not yet solved

→ [View Repository](https://github.com/Kunzoick/student-management-system)

---

### Calculus API
**Java 21 · Spring Boot 3.4 · Railway**

A REST API that exposes a symbolic mathematics engine as HTTP endpoints. Differentiation, integration, and implicit differentiation — five endpoints, one deployed JAR, no math libraries.

The engine was written from scratch: tokenizer, parser, AST, differentiator, integrator, simplifier, printer. The engine has zero Spring dependencies — the REST layer is a thin wrapper around it.

**What makes it worth reading:**
- Hand-coded recursive descent parser and AST — no external library
- Engine is pure Java, testable in isolation without starting Spring
- Variable exponents, inverse trig, and integration by parts all implemented
- Live at: [calculus-api-production.up.railway.app](https://calculus-api-production.up.railway.app)

→ [View Repository](https://github.com/Kunzoick/calculus-api)

---

### Trust-Aware Incident Intelligence API
**Java 23 · Spring Boot 3.5 · MariaDB · Redis · JWT . RabbitMQ**

A RESTful backend API where every request is evaluated not just by who the user is (role) but by how trustworthy their behavior has been (trust score). The two axes never mix — that boundary is the architectural core of the system.

Built security-first: stateless JWT auth with reuse detection, Redis-backed behavioral rate limiting that fails open, correlation ID tracing on every request, and a trust score engine that is the single source of truth for all mutations.
Outbox pattern implemented for reliable event publishing to RabbitMQ

**What makes it worth reading:**
- The Iron Rule — role and trust score are architecturally separated and never mixed
- Token reuse detection that revokes entire token families atomically
- Redis failure never denies a legitimate user — fail-open by design
- Transactional outbox pattern ensures no event are lost on failure

→ [View Repository](https://github.com/Kunzoick/zoick-incident-api)

---

### Async Incident Processing Pipeline
**Java 21 · Spring Boot · RabbitMQ · MySQL**
An async event-driven pipeline that consumes incident events published by the Trust API. Implements the full distributed systems failure handling contract: retry logic with exponential backoff, dead letter queue, idempotency guards, and a watchdog for stuck processing records.

**What makes it worth reading:**
-Transactional outbox pattern on the producer side, idempotency on the consumer side
-Dead letter queue with structured failure tracking
-ProcessingWatchdog detects and recovers stuck records
-Architecture Decision Records documenting every infrastructure choice

→ [View Repository](https://github.com/Kunzoick/zoick-pipeline)

---

### Multi-Tenant Secure API
**Java 21 · Spring Boot 3.5 · MariaDB · Redis · JWT · GitHub Actions**
A production-grade multi-tenant REST API built contract-first. Every architectural decision is documented before implementation. Every phase has a completion gate. Every bug has a root cause and a fix on record.
The core is structural security — tenant isolation enforced at the repository layer, not by convention. A single missed WHERE clause cannot leak data because there is no path to the database that bypasses the tenant scope predicate.

**What makes it worth reading:**
9-layer security stack — rate limiting through ORM immutability
BaseRepository enforces tenant scope on every query structurally
Pure domain workflow engine with zero Spring dependencies
Immutable audit log — state change and audit entry are atomic or neither exists
JWT revocation via Redis JTI blacklist with refresh token reuse detection
39 automated tests against real MariaDB and Redis via Testcontainers
GitHub Actions CI pipeline — all 39 tests run on every push to main
10 Architecture Decision Records documenting every non-obvious choice
16 bugs documented with root cause and fix applied

→ [View Repository](https://github.com/Kunzoick/multitenant-api)

---

## Tech Stack

| Layer | Technologies |
|---|---|
| Language | Java 23, Java 21, Java 17 |
| Framework | Spring Boot 3.5, Spring Boot 3.4 |
| Database | MariaDB, MySQL |
| Cache | Redis |
| Messaging | RabbitMQ |
| Auth | Spring Security, JWT |
| Build | Maven |
| Deploy | Railway |
| Desktop | Swing, JDBC |
| Tools | Flyway, Lombok, Spring Actuator |
| Testing | JUnits 5, Testcontainers |
| CI | GitHub Actions |

---


*Every repository has a README that explains the decisions. Every decision has a reason.*
