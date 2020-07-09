# Reverse Proxy (traefik)
This folder contains the configuration for the reverse proxy in front of the buildbot buildmaster.
The proxy handles tls connections and automatically redirects from port 80 to port 443.
Furtermore, it listens on port 9989 and provides a tls connection for buildbot-workers to the buildmaster.

The services behind traefik reside in private networks, only the ports needed are exposed to the internet.

Traefik needs access to the docker-socket: var/run/docker.sock.
Since this could result in vulnerabilities, the socket is made available over a socket-proxy (tecnativa/docker-socket-proxy), which runs as a service next to traefik.

## Usage
This project is dockerized and uses docker-compose.
The file docker-compose.yml tells docker-compose what to do, so you have to change into the directory containing the file, before executing any of these commands!
### Starting
- docker-compose up -d

or to restart
- docker-compose restart

Note: If the Raspberry Pis can't connect to the buildmaster, simply run docker-compose restart.
### Stopping
- docker-compose down
### Updating
Run the following steps in this order:
- docker-compose down
- docker-compose pull
- docker-compose build
- docker image prune
- docker-compose up -d
### Debugging
To view logs in realtime, run
- docker-compose logs -f

Exit with CRTL+C

## Setup / Configuration
Traefik has a static configuration (docker-compose.yml and traefik.yml) and a dynamic configuration (folders certs and dynamic)
Everything is preconfigured, so no action is required.
The only thing to keep in mind is to renew the certificates, located in certs (please refer to the README in this folder).
Traefik could handle renewals by itself when using letsencrypt, but in this setup wie specified a certificate manually.

The services handled by traefik are configured via labels in the corresponding docker-compose.yml files, in our case ../buildbot/docker-compose.yml, so please have a look there

## 
