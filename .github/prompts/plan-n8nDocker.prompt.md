# Plan: Set up n8n Docker container infrastructure

Create a complete Docker setup to run n8n (workflow automation platform) with PostgreSQL database and persistent data storage. This plan establishes a production-ready foundation using Docker Compose, allowing n8n to run with automatic restarts and data persistence.

## Steps

1. Create a `docker-compose.yml` file with n8n service, PostgreSQL database, and volume configurations for data persistence.
2. Create a `.env.example` file documenting required environment variables (database credentials, n8n host/port).
3. Create a `.dockerignore` file to exclude unnecessary files from Docker builds.
4. Update the README.md with setup instructions, configuration options, and how to run the containers.
5. Test the Docker setup by running `docker-compose up` and verifying n8n is accessible on its web port.

## Further Considerations

1. **n8n version and database choice** — Use the official n8n Docker image (latest) with PostgreSQL for persistence, or use SQLite for simpler single-machine setups? PostgreSQL is recommended for production.
2. **Port configuration** — Default n8n web interface runs on port 5678. Should this be configurable via environment variables?
3. **Additional services** — Do you need Redis caching, webhook support, or other integrations beyond n8n and the database?
4. **Backup and recovery** — Should the plan include volume strategies for backing up n8n data and database state?
