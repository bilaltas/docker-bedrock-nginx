# Bedrock with NGINX and WPCLI on Docker
This is a tiny extension for Roots.io Bedrock to make it easily work on Docker.

## What are included
1. WordPress on PHP-FPM (extend/docker-compose.yml, extend/config/php/uploads.ini)
2. NGINX (extend/Dockerfile, extend/config/nginx/default.conf)
3. WPCLI (extend/Dockerfile)
4. MariaDB (extend/docker-compose.yml)
5. PHPMyAdmin (Use on only for development environment: extend/docker-compose.override.yml)

## Requirements
1. Docker
2. Composer (Only for first Bedrock pull and development environment)

## Build
```sh
# Create native Bedrock Project
composer create-project roots/bedrock my-bedrock

# Copy the Docker related files
cp -r extend/ my-bedrock

# Open the project directory
cd my-bedrock

# Run the Docker Services
docker compose up -d

# Open http://localhost on your browser
```


## WP CLI Usage
```sh
docker-compose exec wordpress wp db check
```

## WP CLI Usage
```sh
docker-compose exec wordpress wp db check
```