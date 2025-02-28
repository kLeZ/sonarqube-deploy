# SonarQube installation with Docker Compose

This repository contains the installation method for SonarQube using Docker Compose.

> [!NOTE]
> Looking for the Kubernetes installation method?
> Please use the [SonarQube helm chart](https://SonarSource.github.io/helm-chart-sonarqube) to install SonarQube on kubernetes.

## Quick start

First, you must clone the [sonarqube-deploy](https://github.com/kLeZ/sonarqube-deploy/tree/main) repository:

```shell
git clone https://github.com/kLeZ/sonarqube-deploy --depth=1 --branch=main sonarqube
```

Copy the example `.env` file and edit any values you want to change:

```shell
cp .env.example .env
emacs .env
```

If you are using the default value of SONAR_DATA, SONAR_EXTS and SONAR_LOGS that are used in the ```.env.example``` you need to make sure that the folders exist, and you have the right permissions:

```shell
sudo mkdir -p /var/sonarqube/{data,extensions,logs}
sudo chown 1000:1000 -R /var/sonarqube
```

Next you start up the containers in the background while making sure to pull the latest versions of all used images.

```shell
docker compose up -d --build --pull always
```

After a while, SonarQube should be up and running on `http://localhost:9000`. The default username and password is login: `admin`, and password: `admin`.

### Customization

The `docker-compose.yml` file present in the repository can be adjusted to your convenience. But note that with each pull, it will be overwritten.
Best practice is to use the file `docker-compose.override.yml` for that case.
For instance you could mount specific configuration files, override environment variables, or switch off services you don't need.

Please refer to the official [Docker Compose documentation](https://docs.docker.com/compose/extends/) for more details.

### Default Port

By default the port is bound to `0.0.0.0` means access to SonarQube will be public.
See below how to change that.

## Configuration

Environment variables can be added to `docker-compose.yml` under `environment` to change
SonarQube's configuration. Some are already defined and can be changed via the environment.

You can pass those variables directly when starting the stack as follows.

```shell
VARIABLE=value docker-compose up -d
```

You can also put those variables into an `.env` file in your current working
directory, and Docker Compose will pick it up automatically. See `.env.example`
for details.

## PORT

If you want to specify a different port, you can do so with:

```shell
SONAR_WEB_PORT=4000
```

If you don't want SonarQube to bind to `0.0.0.0` you can bind it to localhost only like this:

```shell
SONAR_WEB_HOST=127.0.0.1
SONAR_WEB_PORT=4000
```

## TAG

If you want to specify a custom tag for the SonarQube docker image, you can do so with:

```shell
SONAR_TAG=my-docker-tag
```

## Upgrade

Retrieve any changes from the `sonarqube-deploy` repository:

```shell
git pull origin main
```

Run the upgrade:

```shell
docker compose -f docker-compose.yml pull
```

Relaunch the containers, ensure you are pulling to use the latest version of the Docker images:

```shell
docker compose up -d --pull always
```

## Uninstall

If you want to stop the containers without removing them directly:

```shell
docker compose stop
```

You can remove the container stack with:

```shell
docker compose down
```

> [!NOTE]
> This will not remove your data which is persisted in named volumes, likely called `compose_sonarqube_data`, `compose_sonarqube_extensions`, `compose_sonarqube_logs` (for the webapp) and `compose_postgres_data` (for the database).
> The exact name depends on the name of the directory where your `docker-compose.yml` and/or you `docker-compose.override.yml` files are stored (`compose` in this case).

If you want to start from scratch and remove the existing data you will have to remove these volumes via
`docker volume rm compose_sonarqube_data compose_sonarqube_extensions compose_sonarqube_logs compose_postgres_data`.

## Troubleshooting

You can look at the logs with:

```shell
docker compose logs -n 1000
```
