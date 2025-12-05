# Docker Compose Cheat Sheet

## Basics
- `docker compose up` — Start services (build if needed).
- `docker compose up -d` — Start in background.
- `docker compose down` — Stop and remove containers and networks.
- `docker compose down -v` — Also remove volumes.
- `docker compose ps` — List containers.
- `docker compose logs` — Show logs.
- `docker compose logs -f` — Follow logs.

## Build & Rebuild
- `docker compose build` — Build images.
- `docker compose build --no-cache` — Rebuild without cache.
- `docker compose up --build` — Build before starting.

## Running Commands
- `docker compose exec <service> sh` — Shell into a running container.
- `docker compose exec <service> <cmd>` — Run a command in a running container.
- `docker compose run <service> <cmd>` — Run a one-off container for a command.

## Lifecycle: up vs run
### `up`
- Starts **all services** (or selected ones).
- Full lifecycle management: start, stop, restart.
- Uses all config from `docker-compose.yml`:
  - ports  
  - volumes  
  - networks  
  - environment  
  - depends_on  
- Containers are long-lived and controlled by Compose.

### `run`
- Creates a **one-off container** for **one service only**.
- Does **not** start dependencies.
- Does **not** publish ports unless `--service-ports`.
- Ignored by Compose lifecycle (`restart`, `stop`, etc.).
- Used for temporary tasks or debugging.

## Images & Containers
- `docker compose images` — List images.
- `docker compose restart <service>` — Restart a service.
- `docker compose stop` — Stop containers.
- `docker compose start` — Start stopped containers.

## Config Helpers
- `docker compose config` — Validate and print merged config.
- `docker compose config --services` — List services.
- `docker compose config --volumes` — List volumes.

## Common docker-compose.yml Pattern
```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    environment:
      - NODE_ENV=development
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: pass
    volumes:
      - dbdata:/var/lib/postgresql/data

volumes:
  dbdata:
