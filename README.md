Scaperoth's Fork of Docker for Shiny Server
=======================

This is a for of the project [Docker for Shiny Server](https://github.com/rocker-org/shiny)

## Usage:

After installing Docker, you can build and run your own shiny server from the command line.

To build the docker image for this:

```sh
docker build --rm -t scaperoth/shiny .
```

You can replace `scaperoth/shiny` with whatever you want to call your app

Then, to run the build with default examples:

```sh
docker run --rm -p 3838:3838 scaperoth/shiny
```

Or, to run the build with a custom app:

```sh
docker run --rm -p 3838:3838 -v \
	<absolute path to custom shiny app>:/srv/shiny-server/  \
	scaperoth/shiny
```

you can replace 3838 with any port you'd like. If you do replace 3838, make sure to expose the port you're using in the `Dockerfile` If you're exposing the application on a server, you might want something like 80:3838 or 443:3838

### With docker-compose

This repository includes an example `docker-compose` file, to facilitate using this container within docker networks.

#### To run a container with Shiny Server:

```sh
docker-compose up
```

Then visit `http://localhost` (i.e., `http://localhost:80`) in a web browser. If you have an app in `/srv/shinyapps/appdir`, you can run the app by visiting http://localhost/appdir/.

#### To add a Shiny app:

1. Uncomment the last line of `docker-compose.yml`.
1. Place the app in `mountpoints/apps/the-name-of-the-app`, replacing `the-name-of-the-app` with your app's name.

If you have an app in `mountpoints/apps/appdir`, you can run the app by visiting http://localhost/appdir/. (If using boot2docker, visit http://192.168.59.103:3838/appdir/)

### To add a new R library install

1. Open the Dockerfile in the root of this project.
1. Find the line that looks like this `install.packages(c('shiny', 'rmarkdown', 'tidyr', 'plyr', 'readr', 'ggvis')`
1. Add the name of your package after `'ggvis'`

Example: `install.packages(c('shiny', 'rmarkdown', 'tidyr', 'plyr', 'readr', 'ggvis', 'ggplot')`

### Connecting app and log directories to host

To expose a directory on the host to the container, use `-v <host_dir>:<container_dir>`. The following command will use one `/srv/shinyapps` as the Shiny app directory and `/srv/shinylog` as the directory for Shiny app logs. Note that if the directories on the host don't already exist, they will be created automatically.:

```sh
docker run --rm -p 3838:3838 \
    -v /srv/shinyapps/:/srv/shiny-server/ \
    -v /srv/shinylog/:/var/log/shiny-server/ \
    rocker/shiny
```

If you have an app in `/srv/shinyapps/appdir`, you can run the app by visiting http://localhost:3838/appdir/. (If using boot2docker, visit http://192.168.59.103:3838/appdir/)

In a real deployment scenario, you will probably want to run the container in detached mode (`-d`) and listening on the host's port 80 (`-p 80:3838`):

```sh
docker run -d -p 80:3838 \
    -v /srv/shinyapps/:/srv/shiny-server/ \
    -v /srv/shinylog/:/var/log/shiny-server/ \
    rocker/shiny
```

### Warnings

In the logs, you may see a note that shiny is running as root.  To run as a regular user, simply set the user in your Docker run command, e.g.

```sh
docker run --user shiny -p 3838:3838 --rm rocker/shiny
```

### Logs

The Shiny Server log and all application logs are written to `stdout` and can be viewed using `docker logs`.

The logs for individual apps are still kept in the `/var/log/shiny-server` directory, as described in the [Shiny Server Administrator's Guide]( http://docs.rstudio.com/shiny-server/#application-error-logs). If you want to avoid printing the logs to STDOUT, set up the environment variable `APPLICATION_LOGS_TO_STDOUT` to `false` (`-e APPLICATION_LOGS_TO_STDOUT=false`).

#### Logs

The example `docker-compose` file will create a persistent volume for server logs, so that log data will persist across instances where the container is running. To access these logs, while the container is running, run `docker exec -it shiny bash` and then `ls /var/log/shiny-server` to see the available logs. To copy these logs to the host system for inspection, while the container is running, you can use, for example, `docker cp shiny:/var/log/shiny-server ./logs_for_inspection`.

#### Detached mode

In a real deployment scenario, you will probably want to run the container in detached mode (`-d`):

```sh
docker-compose up -d
```

### Custom configuration

To add a custom configuration file, assuming the custom file is called `shiny-customized.config`, uncomment the line

```
COPY shiny-customized.config /etc/shiny-server/shiny-server.conf
```

in the `Dockerfile`, and then run `docker-compose build shiny` to rebuild the container. Inline comments above that line in the `Dockerfile` provide additional documentation.

### Futher Reading

For more developer notes and details, view the original repo at [Docker for Shiny Server](https://github.com/rocker-org/shiny)

## Trademarks

Shiny and Shiny Server are registered trademarks of RStudio, Inc. The use of the trademarked terms Shiny and Shiny Server and the distribution of the Shiny Server through the images hosted on hub.docker.com has been granted by explicit permission of RStudio. Please review RStudio's trademark use policy and address inquiries about further distribution or other questions to permissions@rstudio.com.
