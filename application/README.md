### listing connected argocd cluster

```
argocd cluster list --grpc-web
SERVER                          NAME        VERSION  STATUS   MESSAGE                                                  PROJECT
https://kubernetes.default.svc  in-cluster           Unknown  Cluster has no applications and is not being monitored.
```

### status of my application

```
k get applications
NAME                       SYNC STATUS   HEALTH STATUS
application-from-scratch   OutOfSync     Missing
```

### when change release name
- must sync with PRUNE option
  - all resource must be deleted before sync

### when change param
- not OutOfSync with git-repo but Sync status

### add new application
```
k apply -f new-application.yaml
k get applications
NAME                       SYNC STATUS   HEALTH STATUS
application-from-scratch   Synced        Healthy
sealed-secrets             OutOfSync     Missing
```
