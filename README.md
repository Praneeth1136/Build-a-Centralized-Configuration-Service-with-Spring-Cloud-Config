# Centralized Configuration Service

This repository implements a centralized, Git-backed configuration server using Spring Cloud Config and a client microservice (`inventory-service`) that dynamically consumes this configuration.

## Project Structure
- `config-repo`: The Git repository containing profile-specific Yaml settings.
- `config-server`: The Spring Boot Configuration Server.
- `inventory-service`: The inventory service client.
- `docker-compose.yml`: Orchestration file.

## Requirements
- Docker
- Docker Compose

## Quick Start
1. Ensure your Docker engine is running.
2. At the root of the project, execute:
   ```bash
   docker-compose up --build
   ```
3. The Config Server and the Inventory Service should start up. The proxy expects `config-server` to be healthy before `inventory-service` begins startup.

## Testing Configuration Endpoints
Once both services are running and healthy:
- Test the properties served by Config Server:
  `curl http://localhost:8888/inventory-service/dev`
- Test the current config inside Inventory Service:
  `curl http://localhost:8081/api/inventory/config`
- Check health:
  `curl http://localhost:8081/api/inventory/health`

## Testing Refresh Scope
To dynamically update configurations:
1. Modify `config-repo/inventory-service-dev.yml` (e.g., change `maxStock` to `250`).
2. Commit the changes:
   ```bash
   cd config-repo
   git add .
   git commit -m "Update maxStock"
   ```
3. Trigger the refresh endpoint:
   ```bash
   curl -X POST http://localhost:8081/actuator/refresh
   ```
4. Verify by calling the Inventory Service again:
   `curl http://localhost:8081/api/inventory/config`

## Switching Profiles
To test the `prod` profile, adjust your environment variables (e.g. creating a `.env` based on `.env.example` or exporting directly):
```bash
export SPRING_PROFILES_ACTIVE=prod
export INVENTORY_HOST_PORT=8082
export INVENTORY_CONTAINER_PORT=8082
docker-compose up --build
```
Then verify on `http://localhost:8082/api/inventory/config`
