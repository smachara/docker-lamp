# Containerized PHP Application


## What is this?

Quickly get up and running with Docker to develop a PHP application. 

## How to use

### 1. Get the files and spin up containers

```bash
# Get shipping-docker files
git clone https://github.com/shipping-docker/php-app.git
cd php-app

# Start the app, run containers
#   in the background
# This will download and build the images
#   the first time you run this
docker-compose up -d
```

At this point, we've created containers and have them up and running. However, we didn't create a Laravel application to serve yet. We waited because we wanted a PHP image to get created so we can re-use it and run `composer` commands.

### 2. Create a new Synfony application

```bash
# From directory "php-app"
# Create a Synfony application
docker run -it --rm \
    -v ? \
    -w ? \
    --network=phpapp_appnet \
    smachara2/apache-php7 \
    composer create-project symfony/framework-standard-edition my_project_name "3.3.*"

# Restart required to ensure
# app files shares correctly
docker-compose restart
```

Edit the `application/.env` file to have correct settings for our containers. Adjust the following as necessary:

```
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret

BROADCAST_DRIVER=log
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_DRIVER=sync

REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379
```

> If you already have an application, you can move it to the `application` directory here. Else, you can adjust the shared volume file paths within the `docker-compose.yml` file.
> 
> If you edit the `docker-compose.yml` file, run `docker-compose down; docker-compose up -d` to suck in the new Volume settings.

**NOTE**: If you're not running Docker Mac/Windows (which run Docker in a small virtualized layer), you may need to set permissions on the shared directories that Laravel needs to write to. The following will let Laravel write the storage and bootstrap directories:

```bash
# From directory php-app
chmod -R o+rw application/bootstrap application/storage
```

### 3. (Optionally) Add Auth Scaffolding:

If you'd like, we can add Laravel's Auth scaffolding as well. To do that, we need to run some Artisan commands:

```bash
# Scaffold authentication views/routes
docker run -it --rm \
    -v $(pwd)/application:/opt \
    -w /opt \
    --network=phpapp_appnet \
    shippingdocker/php \
    php artisan make:auth

# Run migrations for auth scaffolding
docker run -it --rm \
    -v $(pwd)/application:/opt \
    -w /opt \
    --network=phpapp_appnet \
    shippingdocker/php \
    php artisan migrate
```

Now we can start using our application! Head to `http://localhost/` to see your Synfony application.
