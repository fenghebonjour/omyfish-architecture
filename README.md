# OMyFish — Architecture Showcase

**OMyFish** is an AI fishing companion for anglers in Quebec: *when* to fish (an explainable 6-factor Bite Score forecast), *what* you caught (photo species identification), and *what the rules are* where you're standing (zone catch limits and mercury consumption advisories).

> Production source code is private (upcoming iOS App Store product). This repository publishes the architecture, diagrams, API contracts, deployment design and selected code samples. Full source access is available on request.

**Live demo:** <https://huggingface.co/spaces/fenghebonjour/omyfish>

## What's here

| Path | Contents |
|---|---|
| [`ARCHITECTURE.md`](ARCHITECTURE.md) | System design: C4 context, container and component diagrams, event flows, data model, security, key decisions and trade-offs |
| [`openapi/`](openapi/) | OpenAPI 3 specs exported from the running services |
| [`deploy/`](deploy/) | Production Docker Compose topology, reverse proxy config and environment template |
| [`samples/`](samples/) | Code excerpts: hexagonal ports and adapters, transactional outbox, idempotent RabbitMQ consumer |

## Stack at a glance

- **Backend:** Java 21 / Spring Boot 3 microservices (hexagonal architecture), Spring Cloud Gateway
- **AI service:** Python / FastAPI, PyTorch (EfficientNet-B3 classifier with a CLIP zero-shot fallback), weather and solunar forecasting, a Groq-backed retrieval Q&A over Quebec regulations
- **Data:** PostgreSQL + PostGIS, MongoDB (species catalog), MinIO/S3 (images), Redis (catalog cache)
- **Messaging:** RabbitMQ quorum queues for domain events, Kafka for the ordered shop-order ledger
- **Clients:** Next.js web app, native SwiftUI iOS app
- **Quality:** JUnit 5 unit tests plus Testcontainers integration tests against real Postgres and RabbitMQ in CI

The same domain was also built as a .NET 10 Clean Architecture/CQRS system and a Django monolith, all against one API contract. This repo documents the Java system, which is the production engine.

## Notice

© OMyFish. All rights reserved. This repository is published for review purposes only. No license is granted to use, copy or redistribute its contents.
