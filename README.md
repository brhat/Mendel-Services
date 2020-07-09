# zse-buildbot-buildmaster
This project contains dockerized services, serving a buildbot buildmaster to the public web over tls.
It is intended to be used on a linux server.
Currently, it runs under Debian Buster.
## Setup
- install docker and docker-compose.
- create a dedicated, unpriveliged user for this project
- add the user to the docker group
- clone this repository to your server
  - optinal: create a branch for your secrets (git checkout -b secrets)
- follow the setup instrctions in the subfolders. Start with traefik and then buildbot.

This project comes mostly preconfigured.
What you have to do:
- changing passwords / specifing credentials (see buildbot/README.md)
- providing a certificate for tls connections (traefik/certs)
- adjusting the web url (buildbot/docker-compose.yml)
- createing a persistent data directory
- adusting file permissions

## Usage
- cd traefik
- docker-compose up -d
- cd ../buildbot
- docker-compose up -d
