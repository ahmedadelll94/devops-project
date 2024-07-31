# devops-project 1
# Introduction
This project demonstrates a multi-service web application setup using Docker Compose. The application consists of several microservices working together to provide a voting system where users can vote and see the results. The services include:

*Vote Service:* A web application where users can cast their votes.
*Result Service:* A web application that displays the voting results.
*Worker Service:* A background processing service that handles the business logic related to voting.
*Redis:* An in-memory data structure store used as a message broker and cache.
*PostgreSQL:* A relational database to store voting data.
*Seed Service:* A one-time service that seeds the database with initial voting data.

# Services Description

1. Vote Service
    *Purpose:* Allows users to vote.
    *Build Context:* ./vote
    *Depend on:* redis
    *Health Check:* Ensures the service is healthy by pinging http://localhost.
    *Ports:* Exposes port 5000.
    *Networks:* Connected to both front-tier and back-tier.
2. Result Service
    *Purpose:* Displays the voting results.
    *Build Context:* ./result
    *Entrypoint:* Uses nodemon for automatic restart during development. Command: nodemon --inspect=0.0.0.0 server.js
    *Depend on:* db
    *Ports:* Exposes ports 5001 and 9229.
    *Networks:* Connected to both front-tier and back-tier.
3. Worker Service
    *Purpose:* Handles background processing tasks.
    *Build Context:* ./worker
    *Depend on:* redis, db
    *Networks:* Connected to back-tier.
4. Redis
    *Purpose:* In-memory data store and cache.
    *Image:* redis:alpine
    *Health Check:* Custom script located in ./healthchecks/redis.sh.
    *Networks:* Connected to back-tier.
5. PostgreSQL
    *Purpose:* Stores voting data.
    *Image:* postgres:15-alpine
    *Environment:* Configured with default user and password (POSTGRES_USER, POSTGRES_PASSWORD).
    *Health Check:* Custom script located in ./healthchecks/postgres.sh.
    *DB Data Path:* /var/lib/postgresql/data
    *Networks:* Connected to back-tier.
6. Seed Service
    *Purpose:* Seeds the database with initial data.
    *Build Context:* ./seed-data
    *Depend on:* vote
    *Profile:* Runs only with the seed profile.
    *Networks:* Connected to front-tier.

# Volumes and Networks
## Volumes
    *db-data:* Persistent storage for PostgreSQL data.

##Networks
    *front-tier:* Network for front-facing services.
    *back-tier:* Network for backend services.

# Expected Result for User Validation
Upon successfully running the Docker Compose setup, the user should be able to:

1. Vote Service
Access the vote application via http://localhost:5000.
Cast votes without errors.
2. Result Service
Access the results application via http://localhost:5001.
View updated voting results reflecting the votes cast in the Vote Service.
3. Worker Service
Function without direct user interaction but should handle backend tasks correctly.
4. Redis
Operate as an in-memory data store and message broker for the Vote and Worker services.
5. PostgreSQL
Store and persist voting data correctly.
Pass the health check scripts indicating the database is running properly.
6. Seed Service
Successfully seed the database with initial data when run with the seed profile.