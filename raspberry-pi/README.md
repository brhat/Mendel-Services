This folder contains the credentials of the buildbot-workers (running on Raspberry Pis) on the buildbot buildmaster.

The credentials (builder name and password) have to be placed in the .env files located in the subfolders.
Each subfolder represents an architecture, e.g. armv6.

The .env files are requried by the buildbot-buildmaster (see master.cfg).
