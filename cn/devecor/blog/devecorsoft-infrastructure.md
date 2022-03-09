# Overview of current infrastructure

overview of current infrastructure


```mermaid
flowchart TB
  subgraph gateway
    _gloo[gloo: restful接口暴露和path级路由]
  end

  subgraph staging[staging argoCD]
    direction BT
    subgraph staging_helm[helm]
      staging_env[env]
      staging_secrets[secrets]
    end
    subgraph staging k8s
      direction LR
      staging_pods[pods]
    end
  end

  subgraph prod[production argoCD]
    direction BT
    subgraph prod_helm[helm]
      prod_env[env]
      prod_secrets[secrets]
    end
    subgraph production k8s
        direction LR
        prod_pods[pods]
    end
  end

  subgraph ci[github actions]
    direction TB
    lint
    test
    build
  end

  _cr[container registry]
  ci --docker image--> _cr
  _cr  -.docker image.-> staging
  _cr -.docker image.-> prod
  ci --message--> staging -.-> _gloo
  ci --message--> prod -.-> _gloo
```

典型场景：

* a restful api
* a env
* a secret

1. dev -> test
2. generate secret by ci
3. configure env and secret on charts(helm)
4. merge into main branch(deploy into staging)
5. expose api by gloo on staging
6. trigger release workflow(deploy into prod)
7. expose api by gloo on prod


## Infrastructure in ECS

```mermaid
flowchart LR
  subgraph VPC

    subgraph ins_1[instance]
      blogor((blogor))
    end

    subgraph ins_2[instance]
      upimage((upimage))
    end
    
    subgraph ins_3[instance]
      webservice((web service))
      gateway((gateway))
      public_ip{{"public ip(EIP)"}}
    end

    router([Router])
    subgraph switch1[vSwitch: 172.26.224.0/20]
    
    end
    subgraph switch2[vSwitch: 172.29.192.0/20]
      
    end

    router --> switch1
    router --> switch2

    switch2 --> ins_3
    switch2 --> ins_2
    switch1 --> ins_1

    gateway -. / .-> webservice
    gateway -. /blog .-> webservice
    gateway -. /blog/blogid .-> ins_1
    gateway -. /image .-> ins_2

    gateway -. listen 80 .- public_ip

  end

  public_ip <--> internet((Internet))
```


