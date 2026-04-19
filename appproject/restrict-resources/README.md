### restrict resources


find api resources

```
k api-resources | grep -i serviceaccount
serviceaccounts                   sa                 v1                                     true         ServiceAccount
```

```
k apply -f restrict-app.yaml
application.argoproj.io/restrict-app created

k get app
NAME                       SYNC STATUS   HEALTH STATUS
application-directory      Synced        Healthy
application-from-scratch   Synced        Healthy
restrict-app               Unknown       Unknown
sealed-secrets             Synced        Degraded

[argocd error log]
resource :Service is not permitted in project restrict-project
resource apps:Deployment is not permitted in project restrict-project
```

### api version

if api version 'v1' then no need to set group

```
# namespace resource
k api-resources  | grep -i service
serviceaccounts                   sa                 v1                                     true         ServiceAccount


# cluster resource
k api-resources | grep namesp
namespaces                        ns                 v1                                     false        Namespace
```


on the other hand, should set api version to group

```
k api-resources | grep -i deploy
deployments                       deploy             apps/v1                                true         Deployment


cat restrict-project.yaml
...
  - group: 'apps'
    kind: 'Deployment'
...
```
