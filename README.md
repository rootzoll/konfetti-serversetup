# konfetti-serversetup
Utility Files and Documentation to get the Server Running

# Setup

Docker needs to be installed on your machine/server.
Clone this git repo and change into `konfetti-serversetup` directory.
Run `docker-compose up` and docker will pull the latest images from dockerhub - and then start up the backend locally.


# Server Configuration

Install docker: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04

Install docker-compose: https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-16-04

Install letsencrypt using `apt-get update && apt-get install letsencrypt`

This repository is enough to bring up the services.

Run service with docker-compose in console
`docker-compose up`

# Application routes
NGINX is configured to bundle the various administration interfaces to be accessible via https, when deployed, the services are not reachable individually. Instead use these routes to access them ([:port/outside_route]->[docker-internal_address]):

`:80,:443/ -> http://konfettiHomepage:80/`

`:80,:443/app/ -> http://konfettiApp:80/`

`:80,:443/admin/ -> http://konfettiAdmin:80/`

`:80,:443/coupongenerator/ -> http://konfettiCouponGenerator:2342/`

`:80,:443/bootadmin/ -> http://konfettiBootAdmin:8180/`

`:80,:443,:8280/konfetti/api/ -> http://konfettiApi:8280/konfetti/api/`

# SSL
Note: this setup will not start without SSL-configuration. For a more accessible development setup without ssl checkout this repository: https://github.com/rootzoll/konfetti-api

SSL is done via letsencrypt.org and certbot (ubuntu package https://certbot.eff.org/#ubuntuxenial-nginx).
Inital certificate aquisition: Adapt and run the following command on the host system (everything is mounted into docker - the ngninx container will not start, if defined mounts or cert-files are not available. Uncomment if necessary)
`$ letsencrypt certonly --webroot -w {pathToProjectFolder}/nginx/letsencrypt -d konfettiapp.de -d www.konfettiapp.de -d test.konfettiapp.de`
This should generate the certificates.
Afterwards run `letsencrypt renew --dry-run --agree-tos` to prepare automatic renewal.
Then place this into root's cron (renews the certificates every 2 month and makes nginx reload the files):
`* * * */2 * letsencrypt renew && pkill -HUP nginx`


# How to

# Update a container
If you want to update a container on the server you have to first switch to the `konfetti` user
with `su konfetti`. After that is done browse to the `~/konfetti-serversetup` folder which is already
checked out on the server and pull the current version from github via `git pull --no-ff`.
Now you can update a container with the following command.
```shell
docker pull <name-of-the-repo-you-want-to-update> && docker-compose up -d --no-deps <name-of-the-corresponding-service>
```
That command acutally pulls the latest changes from docker hup into the local docker machine
and after that it instructs docker-compose to update just the given service in deamon mode (`-d`) and
with no further dependencies (`--no-deps`).

Here is an example to update the admin app and other cointainers on the server (just one line at the time):
```shell
docker pull konfettiapp/admin && docker-compose up -d --no-deps konfettiAdmin
docker pull konfettiapp/api && docker-compose up -d --no-deps konfettiApi
docker pull konfettiapp/app && docker-compose up -d --no-deps konfettiApp
docker pull konfettiapp/homepage && docker-compose up -d --no-deps konfettiHomepage
docker pull konfettiapp/couponservice && docker-compose up -d --no-deps konfettiCouponGenerator
```
