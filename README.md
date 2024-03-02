# backstage-helm-chart

## How to deploy

### Deploy CNPG

- Deploy cnpg

```bash
helm repo add cnpg https://cloudnative-pg.github.io/charts;
helm repo update;
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
      postInitSQL:
        - ALTER ROLE app CREATEDB
EOF
```


