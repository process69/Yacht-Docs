# Docker Compose

In order to use Yacht with docker-compose projects you'll need to use a bind mount to a directory on your host machine. By default in the container the directory you want to mount to is `/config` and your compospe projects should go in `/config/compose`.

The following is an example docker run command to use this setup:

*Replace* `/path/to/data` *with wherever you would like the Yacht config folder to be on your host.*

!!! code "Docker-Compose Example"

    === "docker-cli"
        ``` 
        docker run -d \
            --name=yacht \
            -p 8000:8000 \
            -v /var/run/docker.sock:/var/run/docker.sock \
            -v /path/to/data:/config \
            --restart always \
            selfhostedpro/yacht
        ```
    === "docker-compose"
        ```yaml 
        ---
        version: 3.6
        services:
          yacht:
            image: selfhostedpro/yacht
            container_name: yacht
            volumes:
              - /path/to/data:/config
            ports:
              - 8000:8000
            restart: always
        ```
Once it's running you can add your docker-compose folders to /path/to/data/compose/


# Docker Compose Alternate Installation

If you've already got `/config` setup as a bind mount you could instead bind your compose directory to `/compose` inside the container and then set `COMPOSE_DIR=/compose/` in your environment variables.

Here is an example docker run command to use this setup:


!!! code "Docker-Compose Alternate Example"

    === "docker-cli"
        ``` 
        docker volume create yacht
        docker run -d \
            --name=yacht \
            -p 8000:8000 \
            -v /var/run/docker.sock:/var/run/docker.sock \
            -v yacht_data:/config \
            -v <path-to-compose-dir>:/compose \
            -e COMPOSE_DIR=/compose/ \
            selfhostedpro/yacht
        ```
    === "docker-compose"
        ```yaml 
        ---
        version: 3.6
        services:
          yacht:
            image: selfhostedpro/yacht
            container_name: yacht
            environment:
              - COMPOSE_DIR=/compose/
            volumes:
              - yacht_data:/config
              - <path-to-compose-dir>:/compose
            ports:
              - 8000:8000
            restart: always
        volumes:
          yacht_data:
        ```

# Docker Compose Folder Structure:
The following is an example folder structure:

!!! code "Folder Structure"

    ``` bash
    compose
    ├── docker-pulls                ### Project Folder###
    │   ├── Docker_Hub_Stats.json
    │   ├── README.md
    │   ├── config.monitoring
    │   ├── docker-compose.yml      ### docker-compose.yml ###
    │   ├── prometheus
    │   │   ├── alert.rules
    │   │   └── prometheus.yml
    │   └── prometheus.yml
    ├── opendistro                  ### Project Folder
        └── docker-compose.yml      ### docker-compose.yml ###
    ```
The way this works is the Project will be named the same as the project folder. Inside of the project folder you can put the docker-compose.yml file as well as any other files you need to pass through.

These will be seen by Yacht and you'll be able to control the projects, as well as the services within them. When you view a project, the docker-compose file is parsed and you'll see all of the details of the project.