### restrict source repo

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
InvalidSpecError application repo https://github.com/devopshobbies/argocd-tutorial.git is not permitted in project 'restrict-project'

argocd app list
NAME                             CLUSTER                         NAMESPACE  PROJECT           STATUS   HEALTH    SYNCPOLICY  CONDITIONS        REPO                                                  PATH                                TARGET
argocd/application-directory     https://kubernetes.default.svc  directory  default           Synced   Healthy   Manual      <none>            https://github.com/m0hadang/test-argocd.git           directory-of-manifests              main
argocd/application-from-scratch  https://kubernetes.default.svc  default    default           Synced   Healthy   Manual      <none>            https://github.com/m0hadang/test-argocd.git           helm/nginx                          main
argocd/restrict-app              https://kubernetes.default.svc  default    restrict-project  Unknown  Unknown   Manual      InvalidSpecError  https://github.com/devopshobbies/argocd-tutorial.git  v03-argocd-applications/helm/nginx  main
argocd/sealed-secrets            https://kubernetes.default.svc  default    default           Synced   Degraded  Manual      <none>            https://bitnami-labs.github.io/sealed-secrets                                             1.16.1
```
