Docker Usage
You can use this tool from a docker container by either cloning this repo and building the image or using the automatically generated image on GitHub

Pull the image from GitHub (supports both x86 and ARM)
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock -v $(pwd)/logs:/app/logs ghcr.io/red5d/docker-autocompose <container-name-or-id> <additional-names-or-ids>...docker run --rm -v /var/run/docker.sock:/var/run/docker.sock -v $(pwd)/logs:/app/logs ghcr.io/red5d/docker-autocompose <container-name-or-id> <additional-names-or-ids>... 2>&1 | tee logs/docker-autocompose.log
docker pull ghcr.io/red5d/docker-autocompose:latest
Use the new image to generate a docker-compose file from a running container or a list of space-separated container names or ids:

docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose <container-name-or-id> <additional-names-or-ids>...
To print out all containers in a docker-compose format:

docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose $(docker ps -aq)
Contributing