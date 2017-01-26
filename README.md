# konfetti-serversetup
Utility Files and Documentation to get the Server Running

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

Here is an example to update the admin app on the server:
```shell
docker pull konfettiapp/admin && docker-compose up -d --no-deps konfettiAdmin
```