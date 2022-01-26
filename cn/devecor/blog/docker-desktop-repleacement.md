# Alternatives to docker desktop

try to repleace docker desktop on mac with `ubuntu and docker engine` via `multipass`

The purpose is:

1. use `docker cli` on Mac OS as before
2. run ApiTest as usual

## Initialise ubuntu server with `multipass`

```
brew install multipass
multipass launch --disk 10G --mem 4G --cpus 2 --name primary
```
__Note that__ vm disk space must have more than 6GB.(test container need at least 2GB free disk space and os itself will take 4GB)

## Install Docker Engine on Ubuntu

As long as it launched, we can jump to ubuntu termial with `multipass shell`, then please just follow [the docker offical doc](https://docs.docker.com/engine/install/ubuntu/)

## Expose docker deamon to network via TCP

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
    
    And type systemd service configration as follows
    
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

* Install docker cli on mac with `brew install docker`

After restart your intellij idea, ApiTests should just work. Every thing with docker should be fine. However, Keep in mind that your VPN won't be shared with vm.

