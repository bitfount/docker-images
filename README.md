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

Here's how it can be done with the `docker run` command:
```
docker run -d -v /path/to/config/directory:/mount/config ghcr.io/bitfount/pod:stable
```

Alternative you could copy a config file into a stopped container using [docker cp](https://docs.docker.com/engine/reference/commandline/cp/).

If you're using a CSV data source then you'll also need to mount your data to the container,
this will need to be mounted at the path specified in your config, for simplicity it's easiest
put your config and your CSV in the same directory and then mount it to the container.

## Providing storage for Authentication Tokens
To avoid having to log in repeatedly whenever a Pod or Modeller container is restarted, you can bind a volume on your host machine to the container which will store your access tokens.

The location inside the container to mount to is: `/root/.bitfount/`, so your `docker run` command might look like this:
```
docker run -d -v /path/to/config/directory:/mount/config -v /path/to/directory/for/auth/tokens:/root/.bitfount ghcr.io/bitfount/pod:stable
```

## Starting the Pod
Once your container is running you will need to check the logs and complete the login step,
allowing your container to authenticate with Bitfount.
The process is the same as when running locally (e.g. the tutorials),
except that we can't open the login page automatically for you.

## Running with GPU(s) (NVIDIA CUDA)
To run the pod with a GPU it's recommended to use a Deep Learning Image when provisioning your host machine.
If you are unable to use a cloud provider Deep Learning Image, then see the section below on Manual GPU Setup.

The image you choose needs to have the following:
- NVIDIA GPU Drivers
- NVIDIA CUDA (version 10.2)
- [Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#setting-up-nvidia-container-toolkit)
- [Docker](https://docs.docker.com/engine/install/)

You can check if the Nvidia Container Toolkit is installed by running:
```bash
which nvidia-container-runtime-hook
```
If a path is shown then it is installed.

You can also check that CUDA is installed and set up correctly by running:
```bash
nvidia-smi
```
If it's installed correctly then you will see the details of your GPU.

Docker and the nvidia container toolkit are easy to install if they are missing, just follow the steps in the links above.

Here's an example of a suitable image provided by AWS:
![](assets/images/AWS_Deep_Learning_Image.png)

Once you've provisioned your machine you just need to pull down the pod image:
```bash
docker pull ghcr.io/bitfount/pod:stable
```

Then test that `nvidia-smi` is available _inside_ the container:
```bash
docker run -it --gpus all --entrypoint nvidia-smi ghcr.io/bitfount/pod:stable
```

The result should look something like this:
![](assets/images/nvidia-smi-in-container.png)

If that's all working then you can run the pod with GPU access:
```bash
docker run -d --gpus all -v /path/to/config/directory:/mount/config -v /path/to/directory/for/auth/tokens:/root/.bitfount ghcr.io/bitfount/pod:stable
```
Not forgetting any arguments that you need for mounting volumes etc.

These GPU run arguments are all [documented by Docker](https://docs.docker.com/config/containers/resource_constraints/#access-an-nvidia-gpu), and support is also available in docker-compose.


### Manual GPU Setup
If you are unable to use an image which already has NVIDIA CUDA set u p then [this guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#pre-requisites)
has information on how to install all of the pre-requisites on a wide variety of Operating Systems. 
You should only need the 'Installation Guide', but sections of the 'User Guide' may be useful for debugging or improving your understanding.

## Viewing logs
The logs are streamed to stdout and stderr by the application running inside the docker container.
If you're running the container detached (recommended) then these can be viewed directly using `docker logs` (see [docker's documentation](https://docs.docker.com/engine/reference/commandline/logs/)),
or using the tool that you normally use to view Docker container logs.

If using docker logs, run `docker ps` to find the name of your `ghcr.io/bitfount/pod` container, 
or use the long ID string produced when you ran your container,
 then run `docker logs -f <container name>`.
`-f` will follow the logs as they are generated, you can stop viewing them using `CTRL+C`.