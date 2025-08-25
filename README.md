# PalStory LAMP Development Environment

Docker-based development environment for the PalStory application using PHP 8.2, Apache, and MySQL 8.0.

## Overview

This directory contains the Docker configuration for running PalStory in a local development environment. It provides a complete LAMP stack with the necessary dependencies and configurations.

## Features

- **PHP 8.2** with Apache web server
- **MySQL 8.0** database server
- **phpMyAdmin** for database management
- **Volume mounting** for live code editing

## Quick Start

```bash
# Clone the repository
git clone <repository-url>
cd palstory/palstory-lamp/

# Start the containers
docker-compose up -d

# Visit the application
# http://localhost/game
```

**Initialize the database (first-time only):**

Open the migration runner in your browser (replace token if changed):

```
http://localhost/migration_runner.php?token=change_me_secure_token
```

This will apply all SQL files in `src/html/migrations/` idempotently and seed initial data.

## Ports and URLs

- Web UI: http://localhost/game
- PHP/Apache port: `80:80` (host:container)
- MySQL port: `3306:3306` (host:container)
- phpMyAdmin: http://localhost:8080

### Migration Runner (token-gated)

Run pending DB migrations via:

```
http://localhost/migration_runner.php?token=change_me_secure_token
```

- The token value is controlled by `MIGRATE_TOKEN` in `docker-compose.yml` under the `webserver` service. Change it for your environment.
- Safe to run multiple times; only unapplied migrations will execute.

### Health and Admin Endpoints

- Health: `http://localhost/health.php` → JSON `{ status: "ok" }` when DB is reachable
- Admin players (requires token):
  - `http://localhost/admin_players.php?token=<MIGRATE_TOKEN>`
- Admin logs (requires token):
  - `http://localhost/admin_logs.php?token=<MIGRATE_TOKEN>&limit=200`
- Admin dashboard (HTML UI):
  - `http://localhost/admin.html` (enter `MIGRATE_TOKEN` to view data)

## Configuration

### Docker Services

- **webserver**: PHP 8.2 + Apache with application code
- **database**: MySQL 8.0 with persistent data storage
- **phpmyadmin**: Web interface for database management

## Database Setup

**Apply migrations (recommended)**

- Use the migration runner at `http://localhost/migration_runner.php?token=<MIGRATE_TOKEN>`.
- Safe to run multiple times; only unapplied migrations execute.

**Reset database (optional)**

```bash
docker-compose down -v   # WARNING: deletes DB data
docker-compose up -d
```

**Access phpMyAdmin**: http://localhost:8080
- Server: `database`
- Username: `docker`
- Password: `docker`

## Application Configuration

After starting the containers, configure the PalStory application:

1. **Copy configuration files**:
   ```bash
   cp ../palstory-game/src/html/config.php.example ../palstory-game/src/html/config.php
   cp ../palstory-game/src/html/config.js.example ../palstory-game/src/html/config.js
   ```

2. **Update database settings** in `config.php`:
   ```php
   $DB_SERVER = "database";  // Use Docker service name
   $DB_USERNAME = "docker";
   $DB_PASSWORD = "docker";
   $DB_NAME = "story";
   ```

## Development Workflow

### Starting Development
```bash
docker-compose up -d          # Start all services
docker-compose logs -f        # View logs
```

### Stopping Services
```bash
docker-compose down           # Stop containers
docker-compose down -v        # Stop and remove volumes
```

### Debugging
```bash
docker-compose logs webserver    # View web server logs
docker-compose logs database     # View database logs
docker-compose logs phpmyadmin   # View phpMyAdmin logs
docker exec -it palstory-webserver bash  # Access webserver shell
docker exec -it palstory-mysql mysql -u docker -pdocker story  # Access MySQL shell
```

## File Structure

```
palstory-lamp/
├── docker-compose.yml       # Main Docker Compose configuration
├── docker-compose.simple.yml # Simplified configuration (if present)
├── bin/
│   └── php8/
│       └── Dockerfile       # PHP 8.2 container definition
└── README.md               # This file
```

## Troubleshooting

### Common Issues

1. **Port conflicts**: Change ports in `docker-compose.yml` if 80, 3306, or 8080 are in use
2. **Permission issues**: Ensure Docker has access to the project directory
3. **Database connection**: Use service name `database` instead of `localhost`

### Migrations didn't apply

- Ensure you opened the migration URL with the correct token (see `MIGRATE_TOKEN` in `docker-compose.yml`).
- Check logs: `docker-compose logs webserver`.

### Linux specifics

- Ensure relative mount `../palstory-game/src/html` in `docker-compose.yml` resolves correctly from this directory.
- If SELinux/AppArmor interferes with mounts, try adding `:z` (SELinux) or review Docker permissions.

### Useful Commands

```bash
# Rebuild containers
docker-compose build --no-cache

# View running containers
docker-compose ps

# Access MySQL directly
docker exec -it palstory-mysql mysql -u docker -pdocker story

# Reset database (removes all data and re-initializes)
docker-compose down -v
docker-compose up -d
```

## Production Deployment

This setup is for development only. For production:

1. Use official images without development tools
2. Set proper security configurations
3. Use environment-specific environment variables or compose override files
4. Implement proper backup strategies
5. Configure SSL certificates

## Contributing

1. Make changes to Docker configurations
2. Test with `docker-compose up --build`
3. Update documentation as needed
4. Submit pull requests

## License

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

## Author

**Johan Litsfeldt (johlits)**  
Copyright 2025

## Support

For Docker-related issues, check:
- Docker logs: `docker-compose logs`
- Container status: `docker-compose ps`
- System resources: `docker system df`
