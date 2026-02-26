# Kafka Multi-Broker Cluster (KRaft Mode)

## Project Overview

This repository delivers a production-grade, distributed Apache Kafka cluster orchestrated with Docker Compose, leveraging KRaft mode for modern, ZooKeeper-free operation. Designed for backend engineers and distributed systems practitioners, it demonstrates scalable, fault-tolerant event streaming infrastructure suitable for microservices, real-time analytics, and resilient message pipelines.

## Architecture

- **Multi-Broker Cluster:** Multiple Kafka brokers operate in a distributed topology, supporting horizontal scalability and high availability.
- **KRaft Mode:** Eliminates ZooKeeper, using Kafka's internal controller quorum for leader election and metadata management.
- **Replication & Leader Election:** Topics are replicated across brokers; partitions elect leaders for robust failover and data durability.
- **Docker Compose Orchestration:** Ensures reproducible, isolated environments for development, testing, and CI/CD.

**Cluster Message Flow:**

```text
┌────────────┐   Produce   ┌────────────┐   Replicate   ┌────────────┐   Consume   ┌────────────┐
│  Producer  │ ─────────▶ │  Broker 1  │ ───────────▶  │  Broker N  │ ─────────▶ │  Consumer  │
└────────────┘            │  (Leader)  │              │ (Follower) │            └────────────┘
                        <────── Leader Election & Replication ──────>
```

## Fault Tolerance & Scalability

- **Replication:** Data is replicated across brokers, ensuring durability and availability during broker failures.
- **Leader Election:** Automatic partition leader election guarantees seamless failover and consistent message delivery.
- **Horizontal Scaling:** Add brokers to increase throughput and resilience; supports large-scale distributed workloads.

## Folder Structure

```
MultiBroker/
├── docker-compose.yml
└── readme.md
```

## Prerequisites

- Docker Desktop (latest)
- Docker daemon running

## Operational Commands

Launch the cluster:

```sh
git clone <your-repo-url>
cd MultiBroker
docker compose up -d
```

Check broker status:

```sh
docker ps
# Look for multiple kafka-broker containers (e.g., kafka-broker-1, kafka-broker-2, ...)
```

Access Kafka CLI (example for broker 1):

```sh
docker exec -it kafka-broker-1 sh
cd /opt/kafka/bin
```

Create a replicated topic:

```sh
./kafka-topics.sh \
  --create \
  --topic distributed-topic \
  --bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
  --partitions 3 \
  --replication-factor 3
```

List topics:

```sh
./kafka-topics.sh --list --bootstrap-server localhost:9092
```

Start a producer:

```sh
./kafka-console-producer.sh \
  --topic distributed-topic \
  --bootstrap-server localhost:9092
# Type messages and press Enter
```

Start a consumer (any broker):

```sh
docker exec -it kafka-broker-2 sh
cd /opt/kafka/bin
./kafka-console-consumer.sh \
  --topic distributed-topic \
  --bootstrap-server localhost:9092 \
  --from-beginning
```

## Networking

| Service         | Port  |
|-----------------|-------|
| Kafka Broker 1  | 9092  |
| Kafka Broker 2  | 9093  |
| Kafka Broker 3  | 9094  |

Connect clients using all broker endpoints for full cluster awareness.

## Scaling & Recovery

- Add brokers in `docker-compose.yml` to scale horizontally.
- Replication and leader election ensure data integrity and automatic failover.
- Supports distributed microservices, stream processing, and resilient backend architectures.

## Teardown

Stop all containers:

```sh
docker compose down
```

Remove containers and volumes (purge all topics):

```sh
docker compose down -v
```


