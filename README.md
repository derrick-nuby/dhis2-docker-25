# DHIS2 Docker Setup

## What is DHIS2?

DHIS2 (District Health Information Software 2) is an open-source, web-based platform used for reporting, analysis, and dissemination of data for all health programs. It is widely used by governments and organizations around the world to manage their health information systems.

## Method Used: Docker

In this guide, we will use Docker to install and set up DHIS2 on a Windows machine. Docker allows us to create isolated environments called containers, which can run applications and their dependencies. This makes it easier to manage and deploy applications consistently across different environments.

## Setup Steps

### Prerequisites

1. Install Docker Desktop for Windows from [Docker's official website](https://www.docker.com/products/docker-desktop).
2. Ensure Docker is running and you have administrative privileges.

### Step 1: Clone the Repository

Clone this repository to your local machine:

```sh
...
```

### Step 2: Create Configuration Files

Create the necessary configuration files as shown below:

#### .env

The `.env` file contains environment variables used by Docker Compose to configure the services.

```properties
POSTGRES_DB=dhis2
POSTGRES_USER=dhis
POSTGRES_PASSWORD=dhis
DHIS2_DATABASE_HOST=dhis2-db
DHIS2_DATABASE_PORT=5432
DHIS2_DATABASE_NAME=dhis2
DHIS2_DATABASE_USERNAME=dhis
DHIS2_DATABASE_PASSWORD=dhis
DHIS2_AUDIT_DATABASE_HOST=dhis2-db
DHIS2_AUDIT_DATABASE_PORT=5432
DHIS2_AUDIT_DATABASE_NAME=dhis2
DHIS2_AUDIT_DATABASE_USERNAME=dhis
DHIS2_AUDIT_DATABASE_PASSWORD=dhis
```

#### config/dhis.conf

The `dhis.conf` file contains the DHIS2 configuration settings.

```properties
# DHIS2 configuration file
connection.dialect = org.hisp.dhis.hibernate.dialect.DhisPostgresDialect
connection.driver_class = org.postgresql.Driver
connection.url = jdbc:postgresql://dhis2-db:5432/dhis2
connection.username = dhis
connection.password = dhis
# Additional configuration properties can be added here
```

### Step 3: Docker Compose File

The `compose.yml` file defines the services and their configurations.

```yaml
services:
  dhis2-db:
    image: postgis/postgis:13-3.3
    container_name: dhis2-db
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - dhis2_db_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - dhis2-network

  dhis2-core:
    image: dhis2/core:2.40.7
    container_name: dhis2-core
    environment:
      DHIS2_DATABASE_HOST: ${DHIS2_DATABASE_HOST}
      DHIS2_DATABASE_PORT: ${DHIS2_DATABASE_PORT}
      DHIS2_DATABASE_NAME: ${DHIS2_DATABASE_NAME}
      DHIS2_DATABASE_USERNAME: ${DHIS2_DATABASE_USERNAME}
      DHIS2_DATABASE_PASSWORD: ${DHIS2_DATABASE_PASSWORD}
      DHIS2_AUDIT_DATABASE_HOST: ${DHIS2_AUDIT_DATABASE_HOST}
      DHIS2_AUDIT_DATABASE_PORT: ${DHIS2_AUDIT_DATABASE_PORT}
      DHIS2_AUDIT_DATABASE_NAME: ${DHIS2_AUDIT_DATABASE_NAME}
      DHIS2_AUDIT_DATABASE_USERNAME: ${DHIS2_AUDIT_DATABASE_USERNAME}
      DHIS2_AUDIT_DATABASE_PASSWORD: ${DHIS2_AUDIT_DATABASE_PASSWORD}
    ports:
      - "9831:8080"
    depends_on:
      - dhis2-db
    networks:
      - dhis2-network
    volumes:
      - ./config/dhis.conf:/opt/dhis2/dhis.conf

volumes:
  dhis2_db_data:

networks:
  dhis2-network:
    driver: bridge
```

### Step 4: Run Docker Compose

Start the services using Docker Compose:

```sh
docker-compose up -d
```

This command will download the necessary images and start the containers in detached mode.

### Step 5: Access DHIS2

Once the containers are running, you can access the DHIS2 application by navigating to `http://localhost:9831` in your web browser.

## Docker Images and Containers

### Docker Images

- **dhis2/core:2.40.7**

  - **IN USE**: 9dbbda6f03aa
  - **CREATED**: 13 days ago
  - **SIZE**: 1.39 GB

- **postgis/postgis:13-3.3**
  - **IN USE**: c0106c3dc30e
  - **CREATED**: 2 years ago
  - **SIZE**: 816.7 MB

### Container Resource Usage

- **Container CPU usage**: 1.22% / 1200% (12 CPUs available)
- **Container memory usage**: 1.18GB / 15.14GB

## Explanation of Configuration Files

### .env

The `.env` file contains environment variables that are used to configure the services in the `compose.yml` file. This allows for easy management and modification of configuration settings without changing the Docker Compose file directly.

### config/dhis.conf

The `dhis.conf` file contains the configuration settings for DHIS2, such as database connection details. This file is mounted into the `dhis2-core` container to provide the necessary configuration for DHIS2 to connect to the PostgreSQL database.

### compose.yml

The `compose.yml` file defines the services, their configurations, and how they interact with each other. It includes the following services:

- **dhis2-db**: A PostgreSQL database with PostGIS extensions.
- **dhis2-core**: The DHIS2 core application.

The file also defines the volumes and networks used by the services.

## Running Docker

To start the services, use the following command:

```sh
docker-compose up -d
```

To stop the services, use the following command:

```sh
docker-compose down
```

To view the logs of a specific service, use the following command:

```sh
docker-compose logs <service-name>
```

For example, to view the logs of the `dhis2-core` service:

```sh
docker-compose logs dhis2-core
```

## Conclusion

By following this guide, you have successfully set up DHIS2 using Docker on a Windows machine. Docker simplifies the process of managing and deploying applications by providing isolated environments for each service. This setup ensures that DHIS2 and its dependencies are consistently configured and easy to manage.
