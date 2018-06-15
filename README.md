# Docker + Nginx + Let's Encrypt

Compose config for fully automated nginx proxy with letsencrypt support (uses docker-gen)

## Running proxy
### Prerequisites
* [docker](https://docs.docker.com/engine/installation/) (>= 1.10)
* [docker-compose](https://github.com/docker/compose/releases) (>= 1.8.1)
* access to (sub)domain(s) pointing to a publicly accessible server (required for TLS)

### Setting up things
Clone repository to location on your choice: 
```bash
git clone https://github.com/aDorofeev/docker-nginx-proxy-letsencrypt nginx-proxy
```
Create bridge network, that would allow nginx container to communicate with your projects:
```bash
docker network create -d bridge nginx-proxy
```

### Running
Run a http-only proxy:
```bash
docker-compose up --force-recreate --build -d
```

Or a https-enabled, with automated Let's Encrypt certificate issue:
```bash
docker-compose -f docker-compose.yml -f docker-compose-ssl.yml up --force-recreate --build -d
```

### Usage
Web server of your project needs to be connected to `nginx-proxy` network, and is required to expose web server port.
Environment variables are used to specify required parameters.

Example: a section of docker-compose.yml from a project behind proxy.
```yml
    nginx:
        build: ./docker/nginx
        expose:
            - 80
        environment:
            - VIRTUAL_HOST=yourdomain.com
            - VIRTUAL_NETWORK=nginx-proxy
            - VIRTUAL_PORT=80
            - LETSENCRYPT_HOST=yourdomain.com
            - LETSENCRYPT_EMAIL=youremail@gmail.com
        networks:
            # to interact with your app backend, if required
            - default
            # to interact with nginx proxy
            - proxy-tier
```
