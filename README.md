Example Ergo setup.

It is somewhat similar to [Ergo Bootstrap](https://github.com/ergoplatform/ergo-bootstrap) except it offers much less options and is not NixOS-based.

## Node

```
# Volume and network expected by node compose file.
docker volume create ergo-node
docker network create ergo-node

# Starting the service
cd node
docker-compose up -d

# Stopping the service (still from within the node directory)
# Using stop before/instead of down seems to cause less db corruption issues
docker-compose stop node
docker-compose down
```

## Explorer

Edit `build.sh` (or `build.bat` if using Windows) and set the `EXPLORER_VERSION` variable to the desired Explorer version. You can use any tag from the explorer repository: https://github.com/ergoplatform/explorer-backend.

```
# Run the build script
cd explorer
./build.sh

# Create a named volume for Redis
docker volume create ergo-redis

# Start all services in one go...
docker-compose up -d --build

# ...or only the ones you need
docker-compose up --no-start
docker-compose start db grabber
docker-compose start api
```

### Database volume

Unlike the node, the database volume is mapped, not named. Mapped volumes make it easier to handle  Postresql upgrades.

This means you may have to edit the path in `explorer/docker-compose.yml` and ensure the specified path exists on your system:

```
services:
  db:
...
    volumes:
      # Mapped volume for easier Postgres upgrades.
      # Make sure the path exists or edit it here.
      # See also readme_pg_upgrade.md
      - /var/lib/explorer_pg/14/data:/var/lib/postgresql/data
...
```

### Initial sync

Syncing from scratch will take a long time (weeks). Two solutions:

1. Drop indexes and constraints from the database and restore them once initial sync is done.
2. Get a snapshot from someone

