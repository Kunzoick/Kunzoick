# Kunzoick

Java Backend Developer · Systems Thinker · Production-Minded Builder

I like writing code I can explain later — the decision, the tradeoff, the thing I chose not to do. Most of what's here reflects that more than anything else.

## How I Build

Architecture before code, mostly. I try to write down what I rejected, not just what I kept. Failure paths get treated like a feature that needs designing, not something to patch later. If a system has a limit, I'd rather it be written down somewhere than discovered by someone else.

Every project here has a design document. Every decision in it has a reason attached, even the boring ones.

## Projects

### BarnCart
Spring Boot 3.5 · React · MySQL · Stripe · Docker

A farm-to-customer e-commerce platform, built and deployed end to end — auth, cart, checkout, delivery scheduling, disputes, an admin panel, the works.

Two-phase checkout keeps the Stripe call outside the database transaction. Slot booking happens at reservation, not payment, so two people can't claim the same delivery window. WebSocket events only fire after commit, so the frontend never shows a state that didn't actually happen in the database.

Found and fixed a real one during deployment: a `clearAutomatically` cache-clear on an atomic stock query was silently dropping order items mid-checkout — only the last item in a multi-item cart ever made it to the database, no exception, nothing in the logs to suggest it. Root-caused with a direct DB query rather than trusting the application logs, then traced the fix into six other places that had quietly depended on the same wrong assumption.

- Two-phase checkout, atomic stock deduction, ID-passing pattern for schedulers
- httpOnly refresh cookies, in-memory access tokens, real logout
- 15 ADRs, a bug log with root causes, both written after the fact from what actually shipped
- Live at barncart-frontend.vercel.app · backend on Render, DB on Aiven

→ Frontend: https://github.com/Kunzoick/barncart-frontend
→ Backend: https://github.com/Kunzoick/barncart-backend

### Multi-Tenant Secure API
Java 21 · Spring Boot 3.5 · MariaDB · Redis · JWT · GitHub Actions

Tenant isolation enforced at the repository layer, not by convention — there's no code path to the database that skips the tenant scope predicate, so a missed `WHERE` clause can't leak data by accident.

- 9-layer security stack, rate limiting through ORM immutability
- JWT revocation via Redis JTI blacklist, refresh token reuse detection
- 39 tests against real MariaDB and Redis via Testcontainers, run on every push
- 10 ADRs, 16 bugs logged with root cause and fix

→ View Repository: https://github.com/Kunzoick/multitenant-api

### Trust-Aware Incident Intelligence API
Java 23 · Spring Boot 3.5 · MariaDB · Redis · JWT · RabbitMQ

Every request gets evaluated on two separate axes — who the user is, and how trustworthy their behavior has been. Those two things are kept structurally apart on purpose; that boundary is most of the point of the system.

- Stateless JWT with reuse detection, entire token families revoked atomically
- Redis-backed rate limiting that fails open — a Redis outage doesn't lock out legitimate users
- Transactional outbox pattern for RabbitMQ, so nothing gets lost mid-publish

→ View Repository: https://github.com/Kunzoick/zoick-incident-api

### Async Incident Processing Pipeline
Java 21 · Spring Boot · RabbitMQ · MySQL

Consumes the events the Trust API publishes. Handles the usual distributed-systems list — retries with backoff, a dead letter queue, idempotency, a watchdog for anything that gets stuck.

- Outbox on the producer side, idempotency on the consumer side
- Watchdog process that finds and recovers stuck records
- ADRs for the infrastructure choices, not just the code

→ View Repository: https://github.com/Kunzoick/zoick-pipeline

### Calculus API
Java 21 · Spring Boot 3.4 · Railway

A symbolic math engine exposed over five HTTP endpoints — differentiation, integration, implicit differentiation. No math library underneath any of it.

Tokenizer, parser, AST, differentiator, integrator, simplifier, printer — all written from scratch. The engine doesn't know Spring exists; the REST layer is a thin wrapper around something that's fully testable on its own.

- Hand-written recursive descent parser and AST
- Handles variable exponents, inverse trig, integration by parts
- Live at calculus-api-production.up.railway.app

→ View Repository: https://github.com/Kunzoick/calculus-api

### Student Management System
Java 17 · Swing · JDBC · MySQL

A layered desktop app for managing students across a multi-admin institution. No Spring, no ORM — everything wired by hand, on purpose, mostly to understand what those frameworks are usually doing for you.

Four layers, strictly kept apart: UI → Service → DAO → Database. SwingWorker contract enforced across 20 frames so the EDT never blocks. Went through a full review afterward — 15 critical and 8 high-severity issues found, each one written up with root cause and fix.

- Written justification for skipping Spring/Hibernate/ORM entirely
- `adminId` sentinel pattern closes off a whole category of security bugs
- Multi-tenant isolation enforced at the SQL level on every query
- What's not solved yet is written down too, not just what is

→ View Repository: https://github.com/Kunzoick/student-management-system

## Tech Stack

| Layer | Technologies |
|---|---|
| Language | Java 23, Java 21, Java 17 |
| Framework | Spring Boot 3.5, Spring Boot 3.4 |
| Frontend | React, Tailwind CSS |
| Database | MySQL, MariaDB |
| Cache | Redis |
| Messaging | RabbitMQ |
| Auth | Spring Security, JWT |
| Payments | Stripe |
| Build | Maven |
| Deploy | Render, Vercel, Railway |
| Desktop | Swing, JDBC |
| Tools | Flyway, Lombok, Spring Actuator, Docker |
| Testing | JUnit 5, Testcontainers |
| CI | GitHub Actions |

Every repository has a README that explains the decisions. Every decision has a reason, even if the reason is "I didn't have time and here's what that costs."
