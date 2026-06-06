# In our docker-compose.yml, these two images serve different purposes:

# Understanding the `mongo` and `mongo-express` Images in Docker Compose

In our `docker-compose.yml`, these two images serve different purposes:

## 1. Mongo Image

```yaml
mongodb:
  image: mongo
```

The `mongo` image starts a MongoDB database server inside a container.

### What it does

* Stores your application data.
* Listens on port **27017** (MongoDB's default port).
* Creates an admin user using:

```yaml
MONGO_INITDB_ROOT_USERNAME=admin
MONGO_INITDB_ROOT_PASSWORD=password
```

* Persists data using a Docker volume:

```yaml
volumes:
  - mongo-data:/data/db
```

Without MongoDB, your application would have nowhere to store or retrieve data.

---

## 2. Mongo Express Image

```yaml
mongo-express:
  image: mongo-express
```

The `mongo-express` image starts Mongo Express, a web-based GUI for MongoDB.

### What it does

* Provides a browser interface to manage MongoDB.
* Allows you to:

  * View databases
  * Create collections
  * Insert documents
  * Update documents
  * Delete documents

### Connection to MongoDB

Mongo Express connects to the MongoDB container using:

```yaml
ME_CONFIG_MONGODB_SERVER=mongodb
```

### Accessing Mongo Express

Open your browser and visit:

```text
http://localhost:8080
```

Docker port mapping:

```yaml
ports:
  - "8080:8081"
```

This means:

* **Host Machine Port:** 8080
* **Container Port:** 8081 (Mongo Express default port)

---

## How They Work Together

```text
+-------------------+
|   Your Browser    |
| localhost:8080    |
+---------+---------+
          |
          v
+-------------------+
|   mongo-express   |
|     (Web UI)      |
+---------+---------+
          |
          v
+-------------------+
|       mongo       |
| MongoDB Database  |
|    Port 27017     |
+-------------------+
```

---

## After Running Docker Compose

Start the containers:

```bash
docker-compose up -d
```

Check running containers:

```bash
docker ps
```

Access the services:

* **Mongo Express UI:** http://localhost:8080
* **MongoDB Database:** localhost:27017

---

## Summary

* **Mongo (`mongo`)** is the actual database that stores your application's data.
* **Mongo Express (`mongo-express`)** is a web-based interface used to view and manage the MongoDB database through a browser.
