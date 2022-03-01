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

  ci --docker image--> staging -.-> _gloo
  ci --docker image--> prod -.-> _gloo
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
