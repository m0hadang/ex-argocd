### restrict namespace

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
InvalidSpecError application destination server 'https://kubernetes.default.svc' and namespace 'default' do not match any of the allowed destinations in project 'restrict-project'kk
```
