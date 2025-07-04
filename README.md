# Docker support for default env

## Build docker image

This custom image installs:
 
- ROS Melodic (no longer supporting Kinetic)
- all dependencies installed via apt-get
- virtualenv and creates python3.7 env (env name=py3.7) (optional, enable it yourself)
- sublime text
- terminator

Build command for nvidia gpu:

    docker build -t u18_gpu_ros_melodic:latest \
        --build-arg UID="$(id -u)" \
        --build-arg GID="$(id -g)" \
        -f docker/Dockerfile-default-gpu_melodic . 

Build command for integrated graphics:

    docker build -t u18_ros_melodic:latest \
        -f docker/Dockerfile-default-melodic .

Note: 

* Ensure all config_${ros_distro}/ros_entrypoint_${ros_distro}.sh, bashrc, and install_dependencies.sh are executable files. 

## Make docker container 

Nvidia Gpu (supports both Melodic and Kinetic):

	docker run -it --privileged --net=host --ipc=host \
         --name=u18_ros_melodic \
         --env="DISPLAY=$DISPLAY" \
         --env="QT_X11_NO_MITSHM=1" \
         --runtime=nvidia \
         --gpus all \
         u18_gpu_ros_melodic:latest \
         terminator

Integrated graphics (supports ROS Kinetic):

    docker run -it --privileged --net=host --ipc=host \
         --name=u18_ros_melodic \
         --env="DISPLAY=$DISPLAY" \
         --env="QT_X11_NO_MITSHM=1" \
         u18_ros_melodic:latest \
         terminator

Note: if do not want to use terminator, replace `terminator` with `bash`

## Running container

Run,

    ./docker/run.sh u18_ros_melodic

Alternatively,
```
xhost +si:localuser:$USER
xhost +local:docker
docker start u18_ros_melodic
```

## Install pip dependencies

Enter the container to install the remaining pip dependencies within the virtualenv.

Run,

    . /root/install.sh
    
## Remove container

Run,

	docker rm -f u18_ros_melodic
