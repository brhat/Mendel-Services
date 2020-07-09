# buildbot-buildmaster
This directory contains the configuration of the buildbot buildmaster.

The buildmaster listens to changes on the following repositories:
- https://github.com/CenterForSecureEnergyInformatics/data-compressor (pull requests and branch master)
- https://github.com/brhat/checkBitSize.git (branch master)
- https://github.com/brhat/data-compressor-tests.git (branch master)

When changes are detected (or the force button in the web ui is pressed), the project is built and tested.
## Usage
This project is dockerized and uses docker-compose.
The file docker-compose.yml tells docker-compose what to do, so you have to change into the directory containing the file, before executing any of these commands!
### Starting
- docker-compose up -d

or to restart
- docker-compose restart

Note: If the Raspberry Pis can't connect, simply go to ../traefik and run docker-compose restart from there, too.
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
## How it works
Buildbot-workers are defined in master.cfg.
The workers are connected via port 9989 to the buildmaster.
Workers on different platforms are implemented in this setup:
- windows 10. Unfortunately, tls seems not to work on windows with buildbot, so the vm running the buildmaster has to share a subnet with the windows-vm (which it does in our setup).
- Raspberri Pi (1, 2b and 3b+), dockerized. They use a tls connection handled by the reverse proxy traefik.
- Linux, dockerized. See multiarch_dockerfile and docker-compose.yml for the definition. Runs on the same host as the buildmaster.

Buildfactories define which steps are to be executed on a build.
For each platform/architecture a scheduler is defined in this configuration.

Builders are assigned to jobs (defined by factories).
Finally, schedulers trigger builds on the actual workers.

For a detailed overview, please refer to http://docs.buildbot.net/latest/manual/introduction.html

## Setup / Configuration
The file master.cfg is the main configuration file of buildbot, docker-compose.yml handles all services involved.
### master.cfg
Everything is pre-configured in this setup and does not require much changes.
Please refer to http://docs.buildbot.net/latest/manual/configuration/ for details, as a detailed explanation is beyond the scope of this README.
#### Credentials for the Web Ui
To force builds, you need to be logged in.
In master.cfg, fill in your e-mail address(es) under util.RolesFromEmails(admins=["you@email.provider"])
Make sure, that:
- your credentials are filled in secrets/htpasswd (only clear text, so keep permissions (600) in mind).
- the e-mail address is from a contributor in the github-project.
#### Workers
DO NOT CHANGE master.cfg IN THIS STEP!

All workers are pre-configured.
Each of them has a name (do not change!) and a password (please change!), specified in .env files:
- ../windows/windows.env
- the .env files in the subfolders of ../rasperry-pi/
- multiarch.env

Make sure to set these passwords on the corresponding workers as well!
On the workers, the files are located in the same folders and files to make this step easier.

#### GitHubPullRequestPoller
Important Note: for the GithubPullrequestPoller to work, the owner and repository name (NOT the URL) has to be provided.
In this configuration, the following is used:
- compressorRepoName = 'data-compressor'
- compressorRepoOwner = 'CenterForSecureEnergyInformatics'

### docker-compose.yml
The following services are specified here:
- buildbot-buildmaster
- db: a database for the buildmaster
- worker: a buildbot worker running linux (used to crosscompile), defined in multiarch_dockerfile

#### Persistent Data Storage
You have to create a directory for persistent storage for the database service.
- mkdir -p /data/buildbot/db

If you are unhappy with this location, you can specify another one in docker-compose.yml.
To do so, modify the volume of the service "db" accordingly.

#### Web URL
If you aren't running this service under mendel.fh-salzburg.ac.at, you have to specify a different url in docker-compose.yml.
You'll find this option in the labels of the service buildbot-buildmaster
### db.env
Specify a database password.
