### sync policy

```
  automated:
    prune: true       # auto sync
    selfHeal: true    # prine resources
    enabled: true     # self heal
```

### ENABLE AUTO-SYNC

- when git repo changed then sync
  - git repo changed -> sync
  - argocd live state changed -> not sync

### SELF HEAL

- alwasy sync with git repo
  - git repo changed -> sync
  - argocd live state changed -> sync
- must use with auto-sync option
  - auto-sync must be true

```
# if change scale then rollback replica count
k scale --replicas=5 deployment nginx -n directory
```

### PRUNE RESOURCES

- when sync, delete untracking resource

### application level option
- write on argocd application 
- createx namespace
  - create namespace automatically
- namespace metadata
  - using with create namespace
  - add labels when create namespae
- selective sync
  - select sync target
- prune last
- fail on shared resource
  - if resource is already applied by another application then sync fail
    - regradless of early apply time, sync fail
  - if namespace is different, sync ok
```
SharedResourceWarning    Deployment/nginx is part of applications argocd/shared-app-2 and shared-app-1
...

```

- replace
  - replace all related resource
  - when resource is too big to save kubectl apply configuration into argocd local git, and won't fit into last apply configuration annotation
  - if true, no last-applied-configuration
```
# last-applied-configuration:
kubectl.kubernetes.io/last-applied-configuration: >
  {"apiVersion":"v1","kind":"ServiceAccount","metadata":{"annotations":{"argocd.argoproj.io/sync-options":"Prune=false","argocd.argoproj.io/tracking-id":"auto-sync-app:/ServiceAccount:directory/nginx"},"labels":{"app.kubernetes.io/instance":"nginx","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"nginx","app.kubernetes.io/version":"1.16.0","helm.sh/chart":"nginx-0.1.0"},"name":"nginx","namespace":"directory"}}
```



### resource level option
- write on target resource
- no prune
  - when prune sync, ignore prune
- disable-kubectl-validation
  - skips client-side schema validation by kubectl when applying manifests.
    - Equivalent to running kubectl apply --validate=false
  - When to use it
    - When using CRDs (Custom Resource Definitions) that aren't yet known to the cluster's OpenAPI schema
    - When manifests use fields that the local kubectl doesn't recognize (e.g., newer API fields)
    - When validation produces false positives that block legitimate syncs
  - for dev
- replace
  - replace resource
- prune last

```
metadata:
  annotations:
    argocd.argoproj.io/sync-options: Prune=false      # no prune
    argocd.argoproj.io/sync-options: Validate=false   # disable kubectl valication
    argocd.argoproj.io/sync-options: Replace=true     # replace
    argocd.argoproj.io/sync-options: PruneLast=true   # prune last
```
