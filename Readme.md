# Redis Cluster with Docker Compose

This repository provides a setup to deploy a Redis Cluster with 6 nodes (3 masters, 3 replicas) using Docker Compose. The configuration allows for easy cluster creation, management, and scaling.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Setup Instructions](#setup-instructions)
3. [Running Redis Cluster](#running-redis-cluster)
4. [Connecting to Redis Cluster](#connecting-to-redis-cluster)
5. [Managing Redis Cluster](#managing-redis-cluster)
6. [Using RedisInsight](#using-redisinsight)
7. [Stopping and Removing the Cluster](#stopping-and-removing-the-cluster)

---

## Prerequisites

Before you begin, ensure you have the following installed on your machine:
- Docker
- Docker Compose
- Redis CLI (optional)
- RedisInsight (optional)

---

## Setup Instructions

### 1. Create Redis Configuration Files

Run the following script to generate configuration files for each Redis node:

```bash
for port in $(seq 1 6); do
  mkdir -p /redis/node-${port}/conf
  touch /redis/node-${port}/conf/redis.conf
  cat << EOF >/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done
```

This will create 6 nodes with appropriate configuration files in `/redis/node-X/conf/redis.conf`.

### 2. Create Docker Compose File

Below is the `docker-compose.yaml` file to set up the Redis Cluster:

```yaml
version: "3"
services:
  redis-1:
    image: redis
    command: ["redis-server", "/etc/redis/redis.conf"]
    volumes:
      - /redis/node-1/data:/data
      - /redis/node-1/conf/redis.conf:/etc/redis/redis.conf
    ports:
      - "6371:6379"
      - "16371:16379"
    networks:
      redis_network:
        ipv4_address: 172.38.0.11

  redis-2:
    image: redis
    command: ["redis-server", "/etc/redis/redis.conf"]
    volumes:
      - /redis/node-2/data:/data
      - /redis/node-2/conf/redis.conf:/etc/redis/redis.conf
    ports:
      - "6372:6379"
      - "16372:16379"
    networks:
      redis_network:
        ipv4_address: 172.38.0.12

  redis-3:
    image: redis
    command: ["redis-server", "/etc/redis/redis.conf"]
    volumes:
      - /redis/node-3/data:/data
      - /redis/node-3/conf/redis.conf:/etc/redis/redis.conf
    ports:
      - "6373:6379"
      - "16373:16379"
    networks:
      redis_network:
        ipv4_address: 172.38.0.13

  redis-4:
    image: redis
    command: ["redis-server", "/etc/redis/redis.conf"]
    volumes:
      - /redis/node-4/data:/data
      - /redis/node-4/conf/redis.conf:/etc/redis/redis.conf
    ports:
      - "6374:6379"
      - "16374:16379"
    networks:
      redis_network:
        ipv4_address: 172.38.0.14

  redis-5:
    image: redis
    command: ["redis-server", "/etc/redis/redis.conf"]
    volumes:
      - /redis/node-5/data:/data
      - /redis/node-5/conf/redis.conf:/etc/redis/redis.conf
    ports:
      - "6375:6379"
      - "16375:16379"
    networks:
      redis_network:
        ipv4_address: 172.38.0.15

  redis-6:
    image: redis
    command: ["redis-server", "/etc/redis/redis.conf"]
    volumes:
      - /redis/node-6/data:/data
      - /redis/node-6/conf/redis.conf:/etc/redis/redis.conf
    ports:
      - "6376:6379"
      - "16376:16379"
    networks:
      redis_network:
        ipv4_address: 172.38.0.16

networks:
  redis_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.38.0.0/16
          gateway: 172.38.0.1
```

### 3. Run Docker Compose

Ensure Docker Compose is set up correctly with:

```bash
docker compose config
```

Then run the cluster:

```bash
docker compose up -d
```

---

## Running Redis Cluster

### 1. Verify Running Containers

Check if all containers are running using:

```bash
docker compose ps
```

### 2. Create Redis Cluster

After confirming that all Redis containers are up and running, use the following command to create the cluster:

```bash
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
```

This command sets up 3 master nodes with one replica each.

---

## Connecting to Redis Cluster

You can connect to the Redis Cluster using:

```bash
redis-cli -c -h 172.38.0.11 -p 6379
```

The `-c` flag enables cluster mode, allowing the client to follow redirections between nodes.

To check the cluster nodes:

```bash
127.0.0.1:6379> cluster nodes
```

---

## Managing Redis Cluster

You can manage and test your Redis Cluster using the following Redis CLI commands:

- **Set a key:**

```bash
SET key value
```

- **Get a key:**

```bash
GET key
```

- **Check cluster status:**

```bash
CLUSTER INFO
```

## Stopping and Removing the Cluster

To stop the Redis cluster, run:

```bash
docker compose down
```

To remove all containers and volumes:

```bash
docker compose down --volumes
```