# backstage-helm-chart

![Intro](./img/Backstage-Helm-Chart-intro.png)

This is a Helm chart repo to deploy backsage.
Deploy Backstage in 5 minutes.

<br>
<a href="https://www.platformers.community/">
Powered by <img src="./img/platformers-logo.png" width=100 height=100>
The Platformers Community
 </a>

## Screenshot

![screenshot](./img/platformers-backstage-screenshot.png)

## How to deploy

1. Deploy PG using CNPG
2. Deploy Backstage

### Deploy CNPG

- Deploy cnpg

```bash
helm repo add cnpg https://cloudnative-pg.github.io/charts
helm repo update
helm upgrade --install cnpg cnpg/cloudnative-pg --namespace cnpg-system --create-namespace 
```

- Create the first DB

```bash
kubectl apply -f - << EOF
---
apiVersion: v1
kind: Namespace
metadata:
  name: backstage
---
apiVersion: v1
kind: Secret
type: kubernetes.io/basic-auth
metadata:
  name: app-secret
  namespace: backstage
data:
  username: YXBw
  password: cGFzc3dvcmQ=
---
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: backstage
  namespace: backstage
spec:
  instances: 1
  primaryUpdateStrategy: unsupervised
  storage:
    size: 1Gi
  bootstrap:
    initdb:
      secret:
        name: app-secret
      postInitSQL:
        - ALTER ROLE app CREATEDB
EOF
```

## Deploy Backstage

- [Generate GH access token here](https://github.com/settings/tokens)

The Required Permissions:

```bash
Reading software components:
- repo

Reading organization data:
- read:org
- read:user
- user:email

Publishing software templates:
- repo
- workflow (if templates include GitHub workflows)
```

- Export your token to be used by the chart

```bash
export GH_TOKEN=YOUR-TOKEN
```

- Install the chart using your personal access token

```bash
helm repo add platformers-community http://helm.platformers.community/
helm repo update
helm upgrade --install backstage platformers-community/backstage -n backstage --set github.accessToken=${GH_TOKEN}
```

If you have to enforce HTTPS, use this Helm repo:

``` bash
helm repo add platformers-community https://platformerscommunity.github.io/backstage-helm-chart/
```

## Go to Backstage UI

```bash
open http://127.0.0.1:7007
kubectl port-forward svc/backstage -n backstage 7007:7007
```
