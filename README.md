# Redis Cluster Setup with Docker Compose

This repository contains a Docker Compose configuration for setting up a Redis cluster with three master nodes and three slave nodes. Each Redis instance is configured to support data persistence using the append-only file (AOF) method.

## Prerequisites

- Docker
- Docker Compose

## Directory Structure

```
.
├── docker-compose.yml
└── data
    ├── master1
    ├── master2
    ├── master3
    ├── slave1
    ├── slave2
    └── slave3
```

## Getting Started

### 1. Clone the repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Start the Redis Cluster

Run the following command to start the Redis cluster:

```bash
docker-compose up -d
```

### 3. Create the Redis Cluster

Once all the containers are up and running, you can create the Redis cluster using the following command:

```bash
docker exec -it <container_name> redis-cli --cluster create \
172.39.0.11:6379 172.39.0.12:6379 172.39.0.13:6379 \
172.39.0.14:6379 172.39.0.15:6379 172.39.0.16:6379 \
--cluster-replicas 1
```

Replace `<container_name>` with the name of one of the master containers (e.g., `redis-master-1`).

### 4. Accessing Redis

The Redis instances are exposed on the following ports:

- Master 1: `16379`
- Master 2: `16380`
- Master 3: `16381`
- Slave 1: `16382`
- Slave 2: `16383`
- Slave 3: `16384`

You can connect to any of these ports using a Redis client, such as `redis-cli`.

### 5. Stopping the Cluster

To stop the cluster, run:

```bash
docker-compose down
```

## Configuration Details

- Each Redis instance is configured with:
  - AOF persistence enabled (`--appendonly yes`).
  - Cluster mode enabled (`--cluster-enabled yes`).
  - Custom cluster configuration files located in the `data` directory.

## Customization

Feel free to modify the configuration options in the `docker-compose.yml` file according to your requirements. You can change volumes, ports, and Redis settings as needed.