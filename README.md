# ex-argocd

### what dose ArgoCD do ?
- manage group of manifest of application

### state
- target state : git repo
- live state : deployed resources
- overwrite state : overwrite value
  - not same git repo status but not OutOfSync. it is Sync status.

### test git repo

- https://github.com/m0hadang/test-argocd.git

### argocd login

```
argocd login 192.168.189.77:8080 --insecure --grpc-web
```
