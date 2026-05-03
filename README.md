# ex-argocd

### what dose ArgoCD do ?
- manage group of manifest of application

### state
- target state : git repo
- live state : deployed resources
- overwrite state : overwrite value
  - not same git repo status but not OutOfSync. it is Sync status.

### change application parameter(overwrite state)
- if i change param value in argocd ui, then "parameters" will be added to application yaml
- can overwrite value without OutOfSync
  - **argocd on cluster and git-repo can be different even if Sync status**
  - **through this, git-repo is not a perfect source of truth**

### ignore OutOfSync
- user input parameter
  - user input parameter is not sync tracking target
  - undetermined value in a git repo
  - will determin while on system operation
  - ex) syncPolicy
```
  syncPolicy:
      automated: {}             # not determined. can change gui.
                                # ==> added some user input value
```
- job
  - the jos is not sync tracking target

### argocd namespace
- must deploy application onto argocd namespace
  - if not, you can't see created application on gui

### test git repo

- https://github.com/m0hadang/test-argocd.git

### argocd login

```
argocd login 192.168.189.77:8080 --insecure --grpc-web
```

### tracking strategies

- HELM
  - specific chart version: ex) 1.16.1, ...
  - range: ex) 1.2.*, ...
  - latest version: *

```
spec:
  project: default
  source:
    chart: sealed-secrets
    targetRevision: 1.16.1 # chart version
    helm:
      releaseName: sealed-secrets
    repoURL: https://bitnami-labs.github.io/sealed-secrets
```

- GIT
  - branch: ex) HEAD, main, ...
  - tag: ex) v1, ...
  - commit: ex) 920fb41c0a747c127f41f12aa61d9715b57fa522, ...


```
spec:
  project: default
  source:
    path: directory-of-manifests
    repoURL: https://github.com/m0hadang/test-argocd.git
    targetRevision: main # branch
```

