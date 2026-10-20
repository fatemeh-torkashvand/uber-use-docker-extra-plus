# Uber Ride-Hailing Microservices (Dockerized)

A complete containerized backend for a ride-hailing application (Uber Simulator). This project demonstrates a microservices architecture orchestrated with Docker Compose, featuring event-driven messaging, caching, and a full CI/CD pipeline.

##  Architecture & Stack

The system consists of multiple microservices and infrastructure components:

*   API Service (Python): Handles user registration and ride requests.
*   Matcher Service (Python): Listens to RabbitMQ queues and finds the nearest driver using Redis.
*   Location Service (Go): Updates and retrieves real-time driver locations.
*   Driver Simulator (Python): Simulates driver movements and accepts trips.
*   PostgreSQL: Persistent relational database for users and trips.
*   Redis: In-memory cache for high-speed geospatial data (driver locations).
*   RabbitMQ: Message broker for asynchronous communication between API and Matcher.
*   Nginx: API Gateway and reverse proxy.
*   Monitoring Stack: Prometheus, Grafana, Loki, Promtail, cAdvisor, and Node Exporter for full observability.

##  CI/CD Pipeline

This repository is equipped with GitHub Actions:
1.  Build & Test: Validates the Docker Compose syntax on every push.
2.  Build & Push: Automatically builds Docker images for the microservices and pushes them to Docker Hub (uber-api, uber-matcher, uber-location, uber-simulator).

##  How to Run

1. Clone the repository:
   
```
bash
   git clone https://github.com/fatemeh-torkashvand/uber-use-docker-extra-plus.git
   cd uber-use-docker-extra-plus
```


2. Create your environment file based on the example:
```   
bash
   cp .env.example .env
```
  

3. Build and start all services in detached mode:
```
bash
   docker compose up -d --build
  ```


 API Testing (via Nginx Gateway)

1. Register a Rider:

```
bash
curl -X POST http://localhost/register \
-H "Content-Type: application/json" \
-d '{"name": "Carlos", "type": "rider"}'
```

Expected Output: {"message":"Rider registered successfully","user_id":5}

2. Request a Ride:

```
bash
curl -X POST http://localhost/request_ride \
-H "Content-Type: application/json" \
-d '{"rider_id": 5, "latitude": 34.05, "longitude": -118.24}'
```

Expected Output (Instant!): {"message":"Ride request received. Searching for a driver.","trip_id":2}

3. Monitor Matcher Logs:

```
bash
docker compose logs -f matcher
```

You will see logs like: [x] Received ride request..., [*] Found nearest driver..., [*] Successfully assigned driver...

4. Check Trip Status:

```
bash
curl http://localhost/trip/2
```

Initial Output: {"driver_id":null,"status":"requested"}
Final Output: {"driver_id":1,"status":"assigned"} (driver_id will vary)
