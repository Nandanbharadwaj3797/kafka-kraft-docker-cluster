


# Kafka Infrastructure: Dockerized Event Streaming

## Project Overview

This repository provides a production-grade, containerized Apache Kafka stack orchestrated with Docker Compose. It is engineered for backend engineers and distributed systems practitioners seeking a scalable, reproducible foundation for event-driven microservices, real-time data pipelines, and message-based architectures.

## Architecture & Scalability

- **Kafka Broker (KRaft Mode):** Modern, ZooKeeper-free deployment for simplified management and improved reliability.
- **Docker Compose Orchestration:** Enables isolated, reproducible environments for local development and CI/CD pipelines.
- **Single Broker Topology (default):** Easily extensible to multi-broker clusters for horizontal scalability and fault tolerance.

**Message Flow:**

```text
┌────────────┐   Produce   ┌────────────┐   Consume   ┌────────────┐
│  Producer  │ ─────────▶ │   Kafka    │ ─────────▶ │  Consumer  │
└────────────┘            │  Broker    │            └────────────┘
                                     (KRaft Mode)
```

## Folder Structure

```
Kafka-Docker-Setup/
├── docker-compose.yml
└── README.md
```

## Prerequisites

- Docker Desktop (latest)
- Docker daemon running

## Usage: Launch & Operate

Start the Kafka stack:

```sh
git clone https://github.com/Nandanbharadwaj3797/kafka-kraft-docker-cluster.git
cd Kafka-Docker-Setup
docker compose up -d
```

Verify broker status:

```sh
docker ps
# Expect: kafka-broker   Up   0.0.0.0:9092->9092/tcp
```

## Core Kafka Operations

Access Kafka CLI:

```sh
docker exec -it kafka-broker sh
cd /opt/kafka/bin
```

Create a topic:

```sh
./kafka-topics.sh \
   --create \
   --topic test-topic \
   --bootstrap-server localhost:9092 \
   --partitions 1 \
   --replication-factor 1
```

List topics:

```sh
./kafka-topics.sh --list --bootstrap-server localhost:9092
```

Start a producer:

```sh
./kafka-console-producer.sh \
   --topic test-topic \
   --bootstrap-server localhost:9092
# Type messages and press Enter
```

Start a consumer (separate terminal):

```sh
docker exec -it kafka-broker sh
cd /opt/kafka/bin
./kafka-console-consumer.sh \
   --topic test-topic \
   --bootstrap-server localhost:9092 \
   --from-beginning
```

## Networking

| Service      | Port |
|--------------|------|
| Kafka Broker | 9092 |

Connect clients at: `localhost:9092`

## Scaling Up

- To scale to a multi-broker cluster, extend `docker-compose.yml` with additional broker services and adjust networking/replication configs.
- Supports seamless integration with distributed microservices, stream processing frameworks, and cloud-native deployments.

## Teardown

Stop all containers:

```sh
docker compose down
```

Remove containers and volumes (purge all topics):

```sh
docker compose down -v
```

---

