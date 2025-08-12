# PalStory LAMP Development Environment

Docker-based development environment for the PalStory application using PHP 8.2, Apache, and MySQL 8.0.

## Overview

This directory contains the Docker configuration for running PalStory in a local development environment. It provides a complete LAMP stack with the necessary dependencies and configurations.

## Features

- **PHP 8.2** with Apache web server
- **MySQL 8.0** database server
- **phpMyAdmin** for database management
- **Redis** for caching (optional)
- **Xdebug 3.3.1** for PHP debugging
- **Volume mounting** for live code editing

## Quick Start

```bash
# Clone the repository
git clone <repository-url>
cd palstory/palstory-lamp/

# Copy environment configuration (optional - has sensible defaults)
cp .env.example .env

# Start the containers (database will auto-initialize)
docker-compose up -d

# Visit the application
# http://localhost/story
```

**That's it!** The database will automatically initialize with all required tables and data on first startup.

## Configuration

### Environment Variables (.env)

Create a `.env` file with the following variables:

```env
# HTTP Ports
HTTP_PORT=80
HTTPS_PORT=443

# MySQL Configuration
MYSQL_ROOT_PASSWORD=tiger
MYSQL_USER=docker
MYSQL_PASSWORD=docker
MYSQL_DATABASE=story

# Additional Services
MYSQL_PORT=3306
REDIS_PORT=6379
PHPMYADMIN_PORT=8080
```

### Docker Services

- **webserver**: PHP 8.2 + Apache with application code
- **database**: MySQL 8.0 with persistent data storage
- **phpmyadmin**: Web interface for database management
- **redis**: Redis cache server (optional)

## Database Setup

**Automatic Initialization**: The database automatically initializes with all required tables and data on first startup. No manual setup required!

**Access phpMyAdmin**: http://localhost:8080
- Server: `database`
- Username: `docker`
- Password: `docker`

## Application Configuration

After starting the containers, configure the PalStory application:

1. **Copy configuration files**:
   ```bash
   cp ../palstory-game/src/html/config.php.example ../palstory-game/src/html/config.php
   cp ../palstory-game/src/html/story/config.js.example ../palstory-game/src/html/story/config.js
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
docker-compose logs webserver # View web server logs
docker-compose logs database  # View database logs
docker exec -it palstory-lamp_webserver_1 bash  # Access container shell
```

## File Structure

```
palstory-lamp/
├── docker-compose.yml       # Main Docker Compose configuration
├── docker-compose.simple.yml # Simplified configuration
├── .env                     # Environment variables
├── bin/
│   └── php8/
│       └── Dockerfile       # PHP 8.2 container definition
└── README.md               # This file
```

## Troubleshooting

### Common Issues

1. **Port conflicts**: Change ports in `.env` if 80, 3306, or 8080 are in use
2. **Permission issues**: Ensure Docker has access to the project directory
3. **Database connection**: Use service name `database` instead of `localhost`
4. **Xdebug**: Configure your IDE to connect to port 9003

### Useful Commands

```bash
# Rebuild containers
docker-compose build --no-cache

# View running containers
docker-compose ps

# Access MySQL directly
docker exec -it palstory-lamp_database_1 mysql -u docker -pdocker story

# Reset database (removes all data and re-initializes)
docker-compose down -v
docker-compose up -d
```

## Production Deployment

This setup is for development only. For production:

1. Use official images without development tools
2. Set proper security configurations
3. Use environment-specific `.env` files
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
