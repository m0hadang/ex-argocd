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
