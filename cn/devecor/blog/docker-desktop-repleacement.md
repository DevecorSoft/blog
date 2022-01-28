# Alternatives to docker desktop

try to replace docker desktop on mac with `ubuntu and docker engine` via `multipass`

The purpose is:

1. use `docker cli` and `docker compose` on Mac OS as before
2. run ApiTest as usual

## Initialize ubuntu server with `multipass`

```
brew install multipass
multipass launch --disk 10G --mem 4G --cpus 2 --name primary
```
__Note that__ vm disk space must have more than 6GB.(test container need at least 2GB free disk space and os itself will take 4GB)

## Install Docker Engine on Ubuntu

As long as it launched, we can jump to ubuntu terminal with `multipass shell`, then please just follow [the docker official doc](https://docs.docker.com/engine/install/ubuntu/)

## Expose docker daemon to network via TCP

* Remote tcp config

    `sudo vim /etc/docker/daemon.json`

    ```json
    {
        "hosts": [
            "tcp://0.0.0.0:2375",
            "unix:///var/run/docker.sock"
        ]
    }
    ```

* Override docker service

    ```
    sudo mkdir -p /etc/systemd/system/docker.service.d
    sudo vim /etc/systemd/system/docker.service.d/override.conf
    ```
    
    And type systemd service configuration as follows
    
    ```
    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd
    ```

    Remember to reload it

    ```
    systemctl daemon-reload
    systemctl restart docker.service
    ```

## Configure docker on MAC

* Add `DOCKER_HOST` env on your `~/.zshrc`
  ```shell
  export DOCKER_HOST="tcp://192.168.64.2:2375"
  ```

  `source ~/.zshrc`

After restarting your intellij idea, All `ApiTests` should just work.

## Install docker cli and compose on MAC

* Install docker cli on mac with `brew install docker`
* Install docker compose 
  ```
  mkdir -p ~/.docker/cli-plugins/
  curl -SL https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-darwin-x86_64 -o ~/.docker/cli-plugins/docker-compose
  chmod +x ~/.docker/cli-plugins/docker-compose
  ```
  __note that__ if you are using `Apple m1 chip`, please replace the command above with `darwin-aarch64`
  ```
  curl -SL https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-darwin-aarch64 -o ~/.docker/cli-plugins/docker-compose
  ```
* Test your installation
  ```
  $ docker compose version
  Docker Compose version v2.2.3
  ```

 Everything with docker should be fine. However, Keep in mind that your VPN won't be shared with vm.
