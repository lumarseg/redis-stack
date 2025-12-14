# Redis Stack

Stack de Redis 7 optimizado para despliegue en Portainer con configuración self-contained.

## Requisitos Previos

Crear la red externa compartida:

```bash
docker network create backend_net
```

## Variables de Entorno

Crear archivo `.env` con:

```env
REDIS_USER=your_user
REDIS_PASSWORD=your_password
```

## Despliegue

### Portainer

1. Stacks > Add stack
2. Repository: Usar URL de este repositorio
3. Compose path: `docker-compose.yml`
4. Configurar variables de entorno
5. Deploy

### Docker Compose

```bash
docker compose up -d
```

## Características

- Redis 7 Alpine
- ACL: Usuario personalizado con autenticación
- Persistencia: AOF (everysec) + RDB
- Memoria: Límite 512MB con política LRU
- Healthcheck: Verificación automática de salud
- Red: backend_net (compartida)

## Conexión

```bash
redis-cli -h localhost --user <REDIS_USER> -a <REDIS_PASSWORD>
```

## Persistencia

Volumen `redis_storage`:
- Archivos AOF
- ACL users.acl
- Snapshots RDB
