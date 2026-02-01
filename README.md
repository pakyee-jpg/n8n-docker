# n8n-docker

A production-ready Docker setup for running n8n (workflow automation platform) with PostgreSQL database and persistent data storage.

## Overview

This Docker Compose configuration provides a complete n8n deployment with:
- **n8n**: Workflow automation platform (latest official image)
- **PostgreSQL**: Persistent relational database for n8n data
- **Named Volumes**: Data persistence for both n8n and PostgreSQL
- **Health Checks**: Automatic database readiness verification
- **Auto-restart**: Services automatically restart on failure

## Prerequisites

- Docker and Docker Compose installed
- Sufficient disk space for volumes (depends on workflows and data)
- Available ports 5678 (n8n) and 5432 (PostgreSQL, optional external access)

## Quick Start

### 1. Clone and Setup

```bash
cd /path/to/n8n-docker
cp .env.example .env
```

### 2. Configure Environment (Optional)

Edit `.env` to customize:
- Database credentials (recommended for production)
- n8n port and host
- Webhook URL

Example for production:
```bash
DB_PASSWORD=your_secure_password
N8N_HOST=your-domain.com
N8N_PROTOCOL=https
WEBHOOK_URL=https://your-domain.com/
```

### 3. Start Services

```bash
docker-compose up -d
```

The `-d` flag runs in detached mode. Omit it to see logs in real-time.

### 4. Access n8n

- **Web Interface**: http://localhost:5678 (or your configured N8N_HOST:N8N_PORT)
- **Initial Setup**: Complete the user registration on first access

## Configuration Options

All configuration is managed via the `.env` file:

| Variable | Default | Description |
|----------|---------|-------------|
| `DB_NAME` | `n8n` | PostgreSQL database name |
| `DB_USER` | `n8n` | PostgreSQL username |
| `DB_PASSWORD` | `changeme` | PostgreSQL password (⚠️ change in production) |
| `N8N_HOST` | `localhost` | n8n host address |
| `N8N_PORT` | `5678` | n8n web port |
| `N8N_PROTOCOL` | `http` | Protocol: `http` or `https` |
| `NODE_ENV` | `production` | Environment mode |
| `WEBHOOK_URL` | `http://localhost:5678/` | External webhook URL for workflows |

## Managing Services

### View Logs

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f n8n
docker-compose logs -f postgres
```

### Stop Services

```bash
docker-compose down
```

Services stop but volumes persist. Rerun `docker-compose up -d` to resume.

### Stop and Remove Volumes (⚠️ Deletes data)

```bash
docker-compose down -v
```

### Restart Services

```bash
docker-compose restart
```

### Update n8n to Latest Version

```bash
docker-compose pull n8n
docker-compose up -d n8n
```

## Volumes and Data Persistence

- **postgres_data**: PostgreSQL database storage
- **n8n_data**: n8n workflows, credentials, and settings

Volumes are stored on the host machine and persist across container restarts.

### Backup Data

```bash
# Backup PostgreSQL
docker-compose exec postgres pg_dump -U n8n n8n > backup.sql

# Backup n8n data volume
docker run --rm -v n8n-docker_n8n_data:/data -v $(pwd):/backup \
  alpine tar czf /backup/n8n_backup.tar.gz -C /data .
```

### Restore Data

```bash
# Restore PostgreSQL
cat backup.sql | docker-compose exec -T postgres psql -U n8n n8n

# Restore n8n data volume
docker run --rm -v n8n-docker_n8n_data:/data -v $(pwd):/backup \
  alpine tar xzf /backup/n8n_backup.tar.gz -C /data
```

## Database Connection Details

From within containers or the host:
- **Host**: `postgres` (from within Docker network) or `localhost`
- **Port**: `5432`
- **User**: Configured via `DB_USER` (default: `n8n`)
- **Password**: Configured via `DB_PASSWORD`
- **Database**: Configured via `DB_NAME` (default: `n8n`)

## Production Deployment Checklist

- [ ] Change `DB_PASSWORD` to a strong password
- [ ] Set `N8N_PROTOCOL` to `https` if behind a proxy
- [ ] Configure `N8N_HOST` to your domain
- [ ] Set `WEBHOOK_URL` to your external URL
- [ ] Set `NODE_ENV` to `production`
- [ ] Use a dedicated `.env` file (not `.env.example`)
- [ ] Set up SSL/TLS termination (e.g., via nginx reverse proxy)
- [ ] Configure regular backups of volumes
- [ ] Review n8n documentation for additional security settings

## Troubleshooting

### n8n won't start
- Check logs: `docker-compose logs n8n`
- Verify PostgreSQL is healthy: `docker-compose ps`
- Ensure port 5678 is not in use: `lsof -i :5678`

### PostgreSQL connection errors
- Verify `DB_*` variables match in both services
- Check database is running: `docker-compose logs postgres`
- Ensure database is healthy: `docker-compose exec postgres pg_isready -U n8n`

### Port already in use
- Change `N8N_PORT` or `5432:5432` in docker-compose.yml
- Or stop other services: `docker-compose down -p <project_name>`

### Performance issues
- Check available disk space for volumes
- Monitor resource usage: `docker stats`
- Consider using PostgreSQL tuning variables for production

## Additional Resources

- [n8n Documentation](https://docs.n8n.io)
- [n8n Docker Hub](https://hub.docker.com/r/n8nio/n8n)
- [PostgreSQL Documentation](https://www.postgresql.org/docs)
- [Docker Compose Reference](https://docs.docker.com/compose/compose-file)

## License

This configuration example is provided as-is. See the official n8n repository for licensing information.
