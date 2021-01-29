# docker-images
Information &amp; Licensing details for our public docker images

## License

The license for this software is available in the `LICENSE.md` file.
This can be found in the Github Repository, as well as inside the Docker image.

## Using the Docker images

There are two docker images, one for running a Pod (`ghcr.io/bitfount/pod:stable`),
and another for running a modelling task (`ghcr.io/bitfount/modeller:stable`).

## Providing the Config & Data
Both of the images require a `config.yaml` file to be provided to them,
by default they will try to load it from `/mount/config/config.yaml` inside the docker container.
You can provide this file easily by mounting/binding a volume to the container,
how you do this may vary depending on your platform/environment (Docker/docker-compose/ECS),
if you have any problems doing this then feel free to reach out to us.

Alternative you could copy a config file into a stopped container using [docker cp](https://docs.docker.com/engine/reference/commandline/cp/).

If you're using a CSV data source then you'll also need to mount your data to the container,
this will need to be mounted at the path specified in your config, for simplicity it's easiest
put your config and your CSV in the same directory and then mount it to the container.

## Providing storage for Authentication Tokens
To avoid having to log in repeatedly whenever a Pod or Modeller container is restarted, you can bind a volume on your host machine to the container which will store your access tokens.

The location inside the container to mount to is: `/root/.bitfount/`.

## Starting the Pod
Once your container is running you will need to check the logs and complete the login step,
allowing your container to authenticate with Bitfount.
The process is the same as when running locally (e.g. the tutorials),
except that we can't open the login page automatically for you.

## Viewing logs
The logs are streamed to stdout and stderr by the application running inside the docker container.
These can be viewed directly using `docker logs` (see [docker's documentation](https://docs.docker.com/engine/reference/commandline/logs/)),
or using the tool that you normally use to view Docker container logs.