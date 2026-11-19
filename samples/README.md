# Code samples

Unmodified production code from the Java services, except that comments pointing at internal documents were removed. Package names are kept so the layout is visible.

## `hexagonal/`: ports, use case and wiring (observation-service)

| File | Layer | What it shows |
|---|---|---|
| `CreateObservationUseCase.java` | `domain/port/in` | An inbound port: a plain interface with its own command record |
| `EventPublisherPort.java` | `domain/port/out` | An outbound port: the core says *what* it needs, not *how* |
| `GpsCoordinates.java` | `domain/model` | A self-validating value object |
| `ObservationService.java` | `application` | The use case: no Spring, no JPA, no AMQP imports, so it's unit-tested with plain fakes |
| `AppConfig.java`, `TransactionalCreateObservationUseCase.java` | `config` | Wiring. The transaction boundary is a decorator applied here, so the application layer stays framework-free while the save and the event still commit atomically |

## `outbox/`: transactional outbox

| File | What it shows |
|---|---|
| `OutboxEventPublisher.java` | The adapter behind `EventPublisherPort`. It writes the event as a row in the same transaction as the aggregate instead of calling the broker. |
| `OutboxPublisherJob.java` | The relay. It polls unpublished rows, sends them to RabbitMQ and marks them published. The Javadoc states its delivery guarantee (at-least-once) and its scaling limit (single instance). |

## `messaging/`: idempotent consumer (notification-service)

| File | What it shows |
|---|---|
| `RabbitMQConfig.java` | Topic exchanges, durable **quorum** queues and bindings, declared as code |
| `ObservationCreatedConsumer.java` | The consumer side of at-least-once delivery: it checks the `eventId` before acting, so a redelivered event is a no-op |

Read together, the three folders trace one catch from the REST call to the user's notification inbox. The sequence diagram is in [`ARCHITECTURE.md` §4](../ARCHITECTURE.md#4-event-flow).
