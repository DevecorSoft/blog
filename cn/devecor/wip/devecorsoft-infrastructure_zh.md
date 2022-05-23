# Devecor website基本建筑

[Devecor website](https://devecor.cn)网站在弹性计算云服务平台上的基本建筑概述

### 持续集成/部署视角

```mermaid
flowchart LR
  subgraph Dev
    vue(vue dev server)
    docker(docker-compose)
  end

  subgraph Staging
    stag_nginx(nginx gateway)
    stag_ECS(aliyun ECS)
  end

  subgraph Live
    live_nginx(nginx gateway)
    live_EC2(aws EC2)
  end

  ghcr(github container registry)

  dockerhub(Dockerhub)

  repository(Repository)

  pipeline(Github actions)

  Dev --git push--> repository --trigger--> pipeline --push image--> ghcr --pull image --> Live
  pipeline --push image--> dockerhub --pull image--> Live

  dockerhub --pull image--> Staging
  ghcr --pull image--> Staging
```

### 微服务架构视角

```mermaid
flowchart LR
  gateway((gateway))
  gateway -. /blog/list .-> blog-service((blog-service))
  gateway -. /blog/blog-id .-> blog-service((blog-service))
  gateway -. / .-> web-service((web-service))
  gateway -. /image .-> image-service((image-service))

  internet((Internet)) -.-> gateway
```

### 网络结构视角

```mermaid
flowchart LR
  subgraph VPC["VPC(172.16.0.0/12)"]

    subgraph switch1[vSwitch: 172.26.224.0/20]
      subgraph ins_1[instance]
        blogor((blog-service))
      end
    end

    subgraph switch2[vSwitch: 172.29.192.0/20]
        subgraph ins_2[instance]
          upimage((image-service))
        end
      
        subgraph ins_3[instance]
          webservice((web service))
          gateway((gateway))
        end
    end
  end
```


上图是一张网络结构图，VPC下分配了两个子网，`172.29.192.0/20`下挂载着两个实例，`172.26.224.0/20`下挂载一个实例。

### 访问控制视角

公网连接

```mermaid
flowchart LR
  subgraph VPC["VPC(172.16.0.0/12)"]

    subgraph switch1[vSwitch: 172.26.224.0/20]
      subgraph ins_1[instance]
        blogor((blog-service))
      end
    end

    subgraph switch2[vSwitch: 172.29.192.0/20]
        subgraph ins_2[instance]
          upimage((image-service))
        end
      
        subgraph ins_3[instance]
          webservice((web service))
          gateway((gateway))
        end
    end
  end

  internet((Internet))
  internet -- "http://ip:80" --> ins_3
  internet -- "no access" --x ins_2
  internet -- "no access" --x ins_1
  
```

内网连接

```mermaid
flowchart LR

subgraph VPC["VPC(172.16.0.0/12)"]

    subgraph switch1[vSwitch: 172.26.224.0/20]
      subgraph ins_1[instance]
        blogor((blog-service))
      end
    end

    subgraph switch2[vSwitch: 172.29.192.0/20]
        subgraph ins_2[instance]
          upimage((image-service))
        end
      
        subgraph ins_3[instance]
          webservice((web service))
          gateway((gateway))
        end
    end
  end
  ins_1 <-- "pass" --> ins_2
  ins_1 <-- "pass" --> ins_3
  ins_2 <-- "pass" --> ins_3
```

子网层级的出入网访问全部放通，实例层级通过安全组配置关闭所有端口的入网访问（22端口除外）。保留22号端口是为了留给流水线平台通过ssh密钥对访问，方便实践持续集成和部署。对于出网请求不做任何限制。

为了向公网暴露website服务，仅放通一台实例的80端口入网请求。

### api内网路由视角

```mermaid
flowchart LR
  subgraph router[VPC router]
    direction LR
    172.29.192.0/20
    172.26.224.0/20
  end

  subgraph "172.29.201.167"
    blog-service((blog-service))
  end

  subgraph entry["172.29.201.168"]
    gateway((gateway))
    web-service((web-service))
  end

  subgraph "172.26.226.148"
    image-service((image-service))
  end

  172.29.192.0/20 ---> 172.29.201.167
  172.26.224.0/20 ---> 172.26.226.148

  entry --> router
  internet((Internet)) --> entry
```
