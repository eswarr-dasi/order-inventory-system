# order-inventory-system

Event-driven order and inventory management system built with Kafka, CQRS, and event sourcing. Designed to demonstrate scalable, auditable, eventually-consistent processing across distributed services.

## Architecture Overview

The system follows a CQRS plus Event Sourcing pattern with two core bounded contexts: Order Service and Inventory Service. Each service owns its own data store and communicates with others exclusively through domain events published to Kafka.

Client to Order Service to Kafka Event Bus to Inventory Service, and back through Kafka to Order Service. Read models for the query side are built as projections from the event stream and stored in PostgreSQL for fast queries. Redis is used for caching hot inventory counts.

## Services

order-service handles order placement, cancellation, and order status queries, covering both the command and read model. inventory-service handles stock reservation, release, and restocking, also covering both the command and read model.

## Event Flow

Happy path summary: a client places an order via order-service. order-service persists an OrderCreated event to its event store and publishes it to the order.events Kafka topic. inventory-service consumes OrderCreated, attempts to reserve stock, and publishes either StockReserved or StockReservationFailed. order-service consumes the inventory result and updates the order's status accordingly, Confirmed or Cancelled, publishing OrderConfirmed or OrderCancelled. All events are persisted in an append-only event store, enabling full replay and audit trails.

## Tech Stack

Java, Spring Boot, Apache Kafka, PostgreSQL, Redis, Docker, Kubernetes.

## Local Development

See docker-compose.yml for spinning up local infrastructure: Kafka, Zookeeper, PostgreSQL, and Redis. Start it with `docker compose up -d`.

## Project Status

This project is under active development. See the GitHub Project board for progress on individual milestones.
