# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a self-contained Redis Stack deployment optimized for Portainer. The entire configuration is embedded in `docker-compose.yml` with no external files, making it ideal for GitHub URL-based deployments in Portainer.

## Architecture

**Self-Contained Design**: All Redis configuration (ACL, AOF, memory limits) is generated dynamically via command-line parameters in the docker-compose file. No external config files are needed.

**Networking**: Uses an external shared network `backend_net` that must exist before deployment. This allows other stacks to communicate with Redis.

**Security**: Redis ACL user and password are dynamically generated at startup from environment variables. The ACL file (`users.acl`) is created in the mounted volume at runtime.

**Persistence Strategy**:
- AOF (Append-Only File) with `everysec` fsync for durability (max 1s data loss)
- Auto AOF rewrite at 100% growth and 64MB minimum size
- RDB snapshots as backup
- All stored in `redis_storage` volume

**Memory Management**: 512MB limit with `allkeys-lru` eviction policy.

## Common Commands

Deploy stack:
```bash
docker compose up -d
```

Stop stack:
```bash
docker compose down
```

View logs:
```bash
docker compose logs -f redis
```

Connect to Redis:
```bash
redis-cli -h localhost --user $REDIS_USER -a $REDIS_PASSWORD
```

Check Redis health:
```bash
docker exec redis redis-cli --user $REDIS_USER -a $REDIS_PASSWORD ping
```

## Environment Variables

Required in `.env`:
- `REDIS_USER`: ACL username
- `REDIS_PASSWORD`: ACL password

These are injected into the container and used to:
1. Generate `/data/users.acl` file at startup
2. Authenticate healthcheck commands

## Deployment Notes

**Portainer**: Designed for "Deploy from Git Repository" feature. The repository URL can be used directly since all configuration is self-contained.

**Network Prerequisite**: Create the external network first:
```bash
docker network create backend_net
```

**Volume Location**: `redis_storage` volume contains all persistent data (AOF, ACL, RDB). Backup this volume for disaster recovery.
