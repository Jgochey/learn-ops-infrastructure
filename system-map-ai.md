# System Map (AI)

## 1. System Diagram

```
# Learning Management System - Architecture Diagram

┌─────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                     LEARNING MANAGEMENT SYSTEM                                          │
│                                    Complete Service Architecture                                        │
└─────────────────────────────────────────────────────────────────────────────────────────────────────────┘


                                         EXTERNAL SERVICES
                ┌───────────────────────────────────────────────────────────────────────────────┐
                │                                                                               │
                │       GitHub API                                      Slack API              │
                │    (OAuth, REST)                               (REST, Webhooks)             │
                │                                                                               │
                └─────────┬──────────────────────────────────────┬────────────────────────────┘
                          │                                      │
                 HTTPS/REST OAUTH                      HTTPS/Webhooks
                          │                                      │
        ┌─────────────────┴──────────────┬─────────────────────┬┴──────────────────────────┐
        │                                │                     │                           │
        │                         ┌──────▼──────────┐   ┌──────▼──────────┐              │
        │                         │   Django API    │   │  Monarch Svc    │              │
        │                         │   (Port 8000)   │   │ (Ports 8080/81) │              │
        │                         │   (Debug 5678)  │   │                 │              │
        │                         │                 │   │ Flask 3.0.3     │              │
        │                         │ Django 3.x      │   │ Pydantic        │              │
        │                         │ DRF             │   │ Valkey client   │              │
        │                         │ Pipenv          │   │                 │              │
        │                         │                 │   │                 │              │
        │                         └────┬────────┬──┘   └────────┬─────────┘              │
        │                              │        │              │                         │
        │              ┌───────────────┴─────────────────┐      │                         │
        │              │                                 │      │                         │
        │              │           HTTP/REST            │      │ Pub/Sub (SUBSCRIBE)    │
        │              │         (bidirectional)        │      │                         │
        │              │                                 │      │                         │
        │         ┌────▼────────────────────────────────▼─┐    │                         │
        │         │   React Client (Port 3000)            │    │                         │
        │         │   React 16, TailwindCSS, Radix UI     │    │                         │
        │         └──────────────────────────────────────┘    │                         │
        │                                                      │                         │
        │ HTTPS/REST     ┌──────────────────────────────────┐  │                         │
        │ (OAuth, Repos  │  Valkey Message Broker           │  │                         │
        │  Issues)       │  (Port 6379)                     │◄─┘                         │
        │                │  Valkey (Redis-compatible)      │   Pub/Sub (PUBLISH)       │
        │                └──────────┬───────────────────────┘                           │
        │                           │  ▲                                                │
        │                           │  │                                                │
        │                      Pub/Sub │  Pub/Sub                                        │
        │           (PUBLISH/SUBSCRIBE)│                                                │
        │                           │  │                                                │
        └───────────────────────────┼──┼─────────────────────────────────────────────────┘
                                    │  │
                 ┌──────────────────┘  │
                 │                     │
        ┌────────▼──────────┐  ┌───────┴────────────────────────────────┐
        │ Valkey Monitor     │  │                                        │
        │ (internal)         │  │      PostgreSQL Database              │
        │ Valkey CLI         │  │      (Port 5433)                      │
        │                    │  │      PostgreSQL 16                    │
        │ CLI monitor        │  │      learning_platform_data (volume)  │
        │ of Valkey          │  │                                        │
        │                    │  └────────┬────────────────────┬──────────┘
        └────────────────────┘           │                    │
                                         │ ▲                  │ ▲
                                    SQL Queries          SQL Queries
                                    (read/write)         (read stats)
                                         │ │                  │ │
                 ┌───────────────────────┴─┼──────────────────┴─┼──────┐
                 │                        │                    │      │
                 │                   ┌────┴────────┐    ┌──────┴──────▼┐
                 │                   │  Django API │    │ PostgreSQL   │
                 │                   │  ↕ Database │    │ Exporter     │
                 │                   │             │    │ (Port 9187)  │
                 │                   └──────────────┘    │ Prometheus   │
                 │                                       │ Exporter    │
                 │                                       └──────┬──────┘
                 │                                              │
                 │                                         HTTP Scrape
                 │                                         /metrics
                 │                                              │
                 │                                         ┌────▼──────────┐
                 │                                         │  Prometheus    │
                 │                                         │  (Port 9090)   │
                 │                                         │  Prometheus    │
                 │                                         │  Server        │
                 │                                         │  Time-series   │
                 │                                         │  Database      │
                 │                                         └────┬──────────┘
                 │                                              │
                 │                                         HTTP Scrape
                 │                                         /metrics
                 │                                              │
                 └──────────────────────────────────────────────┤
                                                                │
                                                         ┌──────▼──────────┐
                                                         │   Grafana        │
                                                         │   (Port 3001)    │
                                                         │   Grafana Server │
                                                         │   Dashboards     │
                                                         └──────────────────┘


## Connection Matrix

| From | To | Connection Type | Protocol | Direction |
|------|-----|-----------------|----------|-----------|
| React | Django API | HTTP/REST | HTTP/1.1 | Bidirectional |
| Django API | GitHub API | HTTPS/REST + OAuth | HTTPS | Bidirectional |
| Django API | Slack API | HTTPS/Webhooks | HTTPS | Bidirectional |
| Django API | PostgreSQL | SQL Queries | PostgreSQL Protocol | Bidirectional |
| Django API | Valkey | Pub/Sub (PUBLISH) | Redis Protocol | Bidirectional |
| Monarch | Valkey | Pub/Sub (SUBSCRIBE) | Redis Protocol | Unidirectional |
| Monarch | GitHub API | HTTPS/REST | HTTPS | Unidirectional |
| Monarch | Slack API | HTTPS/Webhooks | HTTPS | Unidirectional |
| Valkey Monitor | Valkey | CLI Monitoring | Redis Protocol | Bidirectional |
| PostgreSQL Exporter | PostgreSQL | SQL Queries (read stats) | PostgreSQL Protocol | Unidirectional |
| Prometheus | Django API | HTTP Scrape /metrics | HTTP | Unidirectional |
| Prometheus | PostgreSQL Exporter | HTTP Scrape /metrics | HTTP | Unidirectional |
| Grafana | Prometheus | HTTP API Queries | HTTP | Unidirectional |
```


```mermaid
graph LR
    React["React Client<br/>(3000)<br/>React 16, TailwindCSS"]
    Django["Django API<br/>(8000/5678)<br/>Django 3.x, DRF"]
    PG["PostgreSQL<br/>(5433)<br/>PostgreSQL 16"]
    Valkey["Valkey<br/>(6379)<br/>Redis-compatible"]
    ValkeyMon["Valkey Monitor<br/>(internal)<br/>Valkey CLI"]
    Monarch["Monarch Service<br/>(8080/8081)<br/>Flask, Pydantic"]
    Prometheus["Prometheus<br/>(9090)<br/>Prometheus Server"]
    Grafana["Grafana<br/>(3001)<br/>Grafana Server"]
    PGExporter["PostgreSQL Exporter<br/>(9187)<br/>Prometheus Exporter"]
    GitHub["GitHub API<br/>OAuth, REST"]
    Slack["Slack API<br/>REST, Webhooks"]

    React -->|HTTP/REST| Django
    Django -->|HTTP/REST| React
    Django -->|HTTPS/OAuth| GitHub
    GitHub -->|HTTPS/REST| Django
    Django -->|HTTPS/Webhooks| Slack
    Slack -->|HTTPS/Webhooks| Django
    Django -->|SQL Queries| PG
    PG -->|SQL Results| Django
    Django -->|Pub/Sub PUBLISH| Valkey
    Valkey -->|Pub/Sub| Django
    Monarch -->|Pub/Sub SUBSCRIBE| Valkey
    Valkey -->|Messages| Monarch
    Monarch -->|HTTPS/REST| GitHub
    GitHub -->|Issues| Monarch
    Monarch -->|HTTPS/Webhooks| Slack
    Slack -->|Status| Monarch
    ValkeyMon -->|CLI Monitor| Valkey
    Valkey -->|CLI Data| ValkeyMon
    PGExporter -->|SQL Queries| PG
    PG -->|Stats| PGExporter
    Prometheus -->|HTTP Scrape| Django
    Prometheus -->|HTTP Scrape| PGExporter
    Grafana -->|HTTP Query| Prometheus
```
