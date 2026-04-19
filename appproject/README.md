### listing app projects

```
k get appprojects
NAME      AGE
default   14d

k get appprojects default -oyaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  creationTimestamp: "2026-04-04T12:23:09Z"
  generation: 1
  name: default
  namespace: argocd
  resourceVersion: "20092"
  uid: 5d2b1d80-2f8b-4918-8c23-1bf91f18f282
spec:
  clusterResourceWhitelist:
  - group: '*'
    kind: '*'
  destinations:
  - namespace: '*'
    server: '*'
  sourceRepos:
  - '*'
status: {}
```

### add app project

```
k apply -f restrict-project.yaml
appproject.argoproj.io/restrict-project created

k get appprojects
NAME               AGE
default            14d
restrict-project   12

```

### listing apps

```
k get apps
NAME                       SYNC STATUS   HEALTH STATUS
application-directory      Synced        Healthy
application-from-scratch   Synced        Healthy
restrict-app               Unknown       Unknown
root@controller:~/argocd/appproject/restrict-destinations# k describe app restrict-app
Name:         restrict-app
Namespace:    argocd
Labels:       <none>
Annotations:  <none>
API Version:  argoproj.io/v1alpha1
Kind:         Application
Metadata:
  Creation Timestamp:  2026-04-19T04:14:13Z
  Generation:          2
  Resource Version:    133145
  UID:                 f4105f00-e4d4-4043-970f-ec6bef1070c1
Spec:
  Destination:
    Namespace:  dev
    Server:     https://kubernetes.default.svc
  Project:      restrict-project
  Source:
    Path:             helm/nginx
    Repo URL:         https://github.com/m0hadang/test-argocd.git
    Target Revision:  main
Status:
  Conditions:
    Last Transition Time:  2026-04-19T04:14:13Z
    Message:               application destination server 'https://kubernetes.default.svc' and namespace 'dev' do not match any of the allowed destinations in project 'restrict-project'
    Type:                  InvalidSpecError
  Health:
    Last Transition Time:  2026-04-19T04:14:13Z
    Status:                Unknown
  Sync:
    Status:  Unknown
Events:
  Type    Reason           Age   From                           Message
  ----    ------           ----  ----                           -------
  Normal  ResourceUpdated  23s   argocd-application-controller  Updated sync status:  -> Unknown
  Normal  ResourceUpdated  23s   argocd-application-controller  Updated health status:  -> Unknown
```
