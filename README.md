# Docker container images with "headless" VNC session

This repository contains a Docker image with headless VNC environments.

The Docker image is installed with the following components:

* Desktop environment [**Xfce4**](http://www.xfce.org)
* VNC-Server (default VNC port `5901`)
* [**noVNC**](https://github.com/novnc/noVNC) - HTML5 VNC client (default http port `6901`)
* Browsers:
  * Mozilla Firefox
  * Chromium

![Docker VNC Desktop access via HTML page](.pics/vnc_container_view.png)

## Installed
* Rocky Linux 8
* Nvidia Driver
* Nvidia Cuda 11.7.1 (You can modify this in Dockerfile. I haven't tested other versions.)
* Xfce4

## Usage
- Build the image:

  `sudo make build`

- Create and run a container:
  
  `sudo make run`

# Connect & Control
If the container is started like mentioned above, connect via one of these options:

* connect via __VNC viewer `localhost:5901`__, default password: `vncpassword`
* connect via __noVNC HTML5 full client__: [`http://localhost:6901/vnc.html`](http://localhost:6901/vnc.html), default password: `vncpassword`
* connect via __noVNC HTML5 lite client__: [`http://localhost:6901/?password=vncpassword`](http://localhost:6901/?password=vncpassword)


## Hints

### 1) Change User of running VNC Container

Per default, since version `1.3.0` all container processes will be executed with user id `1000`. You can change the user id as follows:

#### 1.1) Using root (user id `0`)
Add the `--user` flag to your docker run command:

    docker run -it --user 0 -p 6911:6901 consol/rocky-xfce-vnc

#### 1.2) Using user and group id of host system
Add the `--user` flag to your docker run command:

    docker run -it -p 6911:6901 --user $(id -u):$(id -g) consol/rocky-xfce-vnc

### 2) Override VNC environment variables
The following VNC environment variables can be overwritten at the `docker run` phase to customize your desktop environment inside the container:
* `VNC_COL_DEPTH`, default: `24`
* `VNC_RESOLUTION`, default: `1280x1024`
* `VNC_PW`, default: `my-pw`
* `VNC_PASSWORDLESS`, default: `<not set>`

#### 2.1) Example: Override the VNC password
Simply overwrite the value of the environment variable `VNC_PW`. For example in
the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_PW=my-pw consol/rocky-xfce-vnc

#### 2.2) Example: Override the VNC resolution
Simply overwrite the value of the environment variable `VNC_RESOLUTION`. For example in
the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_RESOLUTION=800x600 consol/rocky-xfce-vnc

#### 2.3) Example: Start passwordless
Set `VNC_PASSWORDLESS` to `true` to disable the VNC password.
It is highly recommended that you put some kind of authorization mechanism
before this. For example in the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_PASSWORDLESS=true consol/rocky-xfce-vnc

### 3) View only VNC
Since version `1.2.0` it's possible to prevent unwanted control via VNC. Therefore you can set the environment variable `VNC_VIEW_ONLY=true`. If set, the startup script will create a random password for the control connection and use the value of `VNC_PW` for view only connection over the VNC connection.

     docker run -it -p 5901:5901 -p 6901:6901 -e VNC_VIEW_ONLY=true consol/rocky-xfce-vnc

### 4) Known Issues

#### 4.1) Chromium crashes with high VNC_RESOLUTION ([#53](https://github.com/ConSol/docker-headless-vnc-container/issues/53))
If you open some graphic/work intensive websites in the Docker container (especially with high resolutions e.g. `1920x1080`) it can happen that Chromium crashes without any specific reason. The problem there is the too small `/dev/shm` size in the container. Currently there is no other way, as define this size on startup via `--shm-size` option, see [#53 - Solution](https://github.com/ConSol/docker-headless-vnc-container/issues/53#issuecomment-347265977):

    docker run --shm-size=256m -it -p 6901:6901 -e VNC_RESOLUTION=1920x1080 consol/rocky-xfce-vnc chromium-browser http://map.norsecorp.com/

Thx @raghavkarol for the hint!

## How to release
See **[how-to-release.md](./how-to-release.md)**

## Contributors

At this point we want to thank all contributors, which helped to move this great project by submitting code, writing documentation, or adapting other tools to play well together with the docker headless container.

* [Sven Nierlein](https://github.com/sni)
* [Tobias Schneck](https://github.com/toschneck)
* [Robert Bohne](https://github.com/rbo) - IceWM images
* [hsiaoyi0504](https://github.com/hsiaoyi0504) - PR [#66](https://github.com/ConSol/docker-headless-vnc-container/pull/66)
* [dmhumph](https://github.com/dmhumph) - PR [#44](https://github.com/ConSol/docker-headless-vnc-container/issue/44)
* [Simon Hofmann](https://github.com/s1hofmann)

## Changelog

The current changelog is provided here: **[changelog.md](./changelog.md)**
