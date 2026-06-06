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
