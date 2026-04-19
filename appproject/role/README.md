### token

external system can access to argocd application using token

### role

role is permission spec

can generate token using role


### create token

```
cat role-project.yaml
...
  roles:
  - name: read-only
    description: "this role can be used for reading application"
    policies:
    - p, proj:role-project:read-only, applications, get, role-project/*, allow
...

# create project
k apply -f role-project.yaml
appproject.argoproj.io/role-project created

# create project token
argocd proj role create-token role-project read-only
Create token succeeded for proj:role-project:read-only.
  ID: 4bd7d0f7-f321-45d7-982e-a1a885353df3
  Issued At: 2026-04-19T14:30:31+09:00
  Expires At: Never
  Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJwcm9qOnJvbGUtcHJvamVjdDpyZWFkLW9ubHkiLCJuYmYiOjE3NzY1NzY2MzEsImlhdCI6MTc3NjU3NjYzMSwianRpIjoiNGJkN2QwZjctZjMyMS00NWQ3LTk4MmUtYTFhODg1MzUzZGYzIn0.SaoJgsiaVRza5WTJKnxeCsZwcj-Fb-z0IbgMx5M6cHA


k apply -f app-1.yaml
application.argoproj.io/app-1 created

# listing app using token
argocd app list --auth-token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJwcm9qOnJvbGUtcHJvamVjdDpyZWFkLW9ubHkiLCJuYmYiOjE3NzY1NzY2MzEsImlhdCI6MTc3NjU3NjYzMSwianRpIjoiNGJkN2QwZjctZjMyMS00NWQ3LTk4MmUtYTFhODg1MzUzZGYzIn0.SaoJgsiaVRza5WTJKnxeCsZwcj-Fb-z0IbgMx5M6cHA
NAME          CLUSTER                         NAMESPACE  PROJECT       STATUS     HEALTH   SYNCPOLICY  CONDITIONS  REPO                                         PATH        TARGET
argocd/app-1  https://kubernetes.default.svc  default    role-project  OutOfSync  Missing  Manual      <none>      https://github.com/m0hadang/test-argocd.git  helm/nginx  main
```

### sync

with admin => success

```
argocd app sync app-1
TIMESTAMP                  GROUP        KIND       NAMESPACE                  NAME    STATUS    HEALTH        HOOK  MESSAGE
2026-04-19T14:37:14+09:00   apps  Deployment         default           app-1-nginx  OutOfSync  Missing
2026-04-19T14:37:14+09:00            Service         default           app-1-nginx  OutOfSync  Missing
2026-04-19T14:37:14+09:00         ServiceAccount     default           app-1-nginx  OutOfSync  Missing
2026-04-19T14:37:15+09:00         ServiceAccount     default           app-1-nginx    Synced  Missing
2026-04-19T14:37:15+09:00         ServiceAccount     default           app-1-nginx    Synced   Missing              serviceaccount/app-1-nginx created
2026-04-19T14:37:15+09:00            Service         default           app-1-nginx  OutOfSync  Missing              service/app-1-nginx created
2026-04-19T14:37:15+09:00   apps  Deployment         default           app-1-nginx  OutOfSync  Missing              deployment.apps/app-1-nginx created
2026-04-19T14:37:15+09:00            Service     default           app-1-nginx    Synced  Healthy                  service/app-1-nginx created
2026-04-19T14:37:15+09:00   apps  Deployment     default           app-1-nginx    Synced  Progressing              deployment.apps/app-1-nginx created

Name:               argocd/app-1
Project:            role-project
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://argocd.example.com/applications/app-1
Source:
- Repo:             https://github.com/m0hadang/test-argocd.git
  Target:           main
  Path:             helm/nginx
SyncWindow:         Sync Allowed
Sync Policy:        Manual
Sync Status:        Synced to main (46440f2)
Health Status:      Progressing

Operation:          Sync
Sync Revision:      46440f2c51e571d9bc504226d8a638f71ca26137
Phase:              Succeeded
Start:              2026-04-19 14:37:14 +0900 KST
Finished:           2026-04-19 14:37:15 +0900 KST
Duration:           1s
Message:            successfully synced (all tasks run)

GROUP  KIND            NAMESPACE  NAME         STATUS  HEALTH       HOOK  MESSAGE
       ServiceAccount  default    app-1-nginx  Synced                     serviceaccount/app-1-nginx created
       Service         default    app-1-nginx  Synced  Healthy            service/app-1-nginx created
apps   Deployment      default    app-1-nginx  Synced  Progressing        deployment.apps/app-1-nginx created
```

with token(read-only) => fail, because of permision

```
k apply -f app-2.yaml
application.argoproj.io/app-2 created

argocd app list --auth-token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJwcm9qOnJvbGUtcHJvamVjdDpyZWFkLW9ubHkiLCJuYmYiOjE3NzY1NzY2MzEsImlhdCI6MTc3NjU3NjYzMSwianRpIjoiNGJkN2QwZjctZjMyMS00NWQ3LTk4MmUtYTFhODg1MzUzZGYzIn0.SaoJgsiaVRza5WTJKnxeCsZwcj-Fb-z0IbgMx5M6cHA
NAME          CLUSTER                         NAMESPACE  PROJECT       STATUS     HEALTH   SYNCPOLICY  CONDITIONS  REPO                                         PATH        TARGET
argocd/app-1  https://kubernetes.default.svc  default    role-project  Synced     Healthy  Manual      <none>      https://github.com/m0hadang/test-argocd.git  helm/nginx  main
argocd/app-2  https://kubernetes.default.svc  default    role-project  OutOfSync  Missing  Manual      <none>      https://github.com/m0hadang/test-argocd.git  helm/nginx  main

argocd app sync app-2 --auth-token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJwcm9qOnJvbGUtcHJvamVjdDpyZWFkLW9ubHkiLCJuYmYiOjE3NzY1NzY2MzEsImlhdCI6MTc3NjU3NjYzMSwianRpIjoiNGJkN2QwZjctZjMyMS00NWQ3LTk4MmUtYTFhODg1MzUzZGYzIn0.SaoJgsiaVRza5WTJKnxeCsZwcj-Fb-z0IbgMx5M6cHA
{"level":"fatal","msg":"rpc error: code = PermissionDenied desc = permission denied: applications, sync, role-project/app-2, sub: proj:role-project:read-only, iat: 2026-04-19T05:30:31Z","time":"2026-04-19T14:49:37+09:00"}
```
### token update

if update appproject then token is not valid more

should create new token

```
k apply -f role-project.yaml
appproject.argoproj.io/role-project configured

argocd proj role create-token role-project read-sync
Create token succeeded for proj:role-project:read-sync.
  ID: 43ee588f-c49e-4e60-a316-b30c63be3ab4
  Issued At: 2026-04-19T15:02:07+09:00
  Expires At: Never
  Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJwcm9qOnJvbGUtcHJvamVjdDpyZWFkLXN5bmMiLCJuYmYiOjE3NzY1Nzg1MjcsImlhdCI6MTc3NjU3ODUyNywianRpIjoiNDNlZTU4OGYtYzQ5ZS00ZTYwLWEzMTYtYjMwYzYzYmUzYWI0In0.uKNhgUUHOT8xihrvtWG7aX_7BsS18wEISNP4AiI4Fc0
```
