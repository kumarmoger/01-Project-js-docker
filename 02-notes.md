# Why Does Docker Compose Create a Default Network?

When you run:

```bash
docker-compose up -d
```

Docker Compose automatically creates a dedicated network for the project. This network allows all services defined in the `docker-compose.yml` file to communicate with each other using their service names.

## Example

```yaml
services:
  mongodb:
    image: mongo

  mongo-express:
    image: mongo-express
```

Docker Compose creates a network similar to:

```text
myproject_default
```

and connects both containers to it.

As a result, Mongo Express can connect to MongoDB using:

```yaml
ME_CONFIG_MONGODB_SERVER=mongodb
```

Here, `mongodb` is the service name and acts as the hostname within the Docker network.

---

# What If I Already Created My Own Network?

Suppose you create a network manually:

```bash
docker network create my-network
```

Docker Compose will still create its own default network unless you explicitly configure your services to use the existing network.

## Example

```yaml
services:
  mongodb:
    image: mongo
    networks:
      - my-network

  mongo-express:
    image: mongo-express
    networks:
      - my-network

networks:
  my-network:
    external: true
```

In this configuration:

* Docker Compose uses the existing `my-network`.
* No new `<project-name>_default` network is created.
* Both containers are attached to the external network.

---

# Verify Networks

### List all Docker networks

```bash
docker network ls
```

### Inspect a specific network

```bash
docker network inspect my-network
```

### Check which network a container is attached to

```bash
docker inspect <container-id>
```

---

# Summary

By default, Docker Compose creates a project-specific network so containers can communicate using service names. If you want to use a network that already exists, you must declare it as an external network in the `docker-compose.yml` file.

=============================================================================================
# Understanding `external: true` in Docker Compose

In Docker Compose:

```yaml
networks:
  my-network:
    external: true
```

The `external: true` option tells Docker Compose:

> Do not create this network. Instead, use an existing Docker network that has already been created.

## Without `external: true`

```yaml
networks:
  my-network:
```

When you run:

```bash
docker-compose up -d
```

Docker Compose automatically creates the network if it does not already exist.

## With `external: true`

```yaml
networks:
  my-network:
    external: true
```

Docker Compose expects the network to already exist before starting the containers.

Create the network manually:

```bash
docker network create my-network
```

If the network does not exist, Docker Compose displays an error similar to:

```text
network my-network declared as external, but could not be found
```

## Why Use `external: true`?

Using an external network is useful when:

* Multiple Docker Compose projects need to share the same network.
* You want to manage network creation manually.
* Containers from different Compose files need to communicate with each other.
* You want greater control over network configuration and lifecycle.

## Example

```yaml
services:
  mongodb:
    image: mongo
    networks:
      - my-network

  mongo-express:
    image: mongo-express
    networks:
      - my-network

networks:
  my-network:
    external: true
```

In this example:

1. Docker Compose does not create a new network.
2. Both containers join the existing `my-network`.
3. Containers can communicate with other containers connected to the same external network.

## Summary

* `external: false` (default) → Docker Compose creates and manages the network.
* `external: true` → Docker Compose uses an existing network and does not create or delete it.
