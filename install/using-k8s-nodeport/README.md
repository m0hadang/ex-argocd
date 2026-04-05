Install Argo-cd with 2 different modes
- Non-HA mode
- HA mode


# install

$ k create ns argocd
$ kubens argocd
$ k apply -f install.yaml

# components

$ k get pods
NAME                                               READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                    1/1     Running   0          102s
argocd-applicationset-controller-b877ff4d8-xr797   1/1     Running   0          104s
argocd-dex-server-6875748b9c-rd65g                 1/1     Running   0          104s
- for authentication
- for integrating with external authentication like ldap
argocd-notifications-controller-67b66cfb7-qg4wv    1/1     Running   0          104s
argocd-redis-7f45896d6d-mm7pd                      1/1     Running   0          104s
- using for cache
argocd-repo-server-5b5df8bb95-97s6m                1/1     Running   0          103s
- caching git repo
- use redis
argocd-server-7f6c5776d8-zjhhm                     1/1     Running   0          103s
- authenticate argocd
- only interacte with user

# get password

$ k get secret
NAME                          TYPE     DATA   AGE
argocd-initial-admin-secret   Opaque   1      18m
argocd-notifications-secret   Opaque   0      19m
argocd-redis                  Opaque   1      18m
argocd-secret                 Opaque   5      19m

$ k get secret argocd-initial-admin-secret
NAME                          TYPE     DATA   AGE
argocd-initial-admin-secret   Opaque   1      18m

$ k get secret argocd-initial-admin-secret -oyaml
apiVersion: v1
data:
  password: alFEQlpsS0NXUkw5d21ZNg==
kind: Secret
metadata:
  creationTimestamp: "2026-04-04T10:51:16Z"
  name: argocd-initial-admin-secret
  namespace: argocd
  resourceVersion: "10815"
  uid: d3e3ab1a-2d02-4f65-b7e5-c49de132ad49
type: Opaque

$ echo  alFEQlpsS0NXUkw5d21ZNg== | base64 --decode
jQDBZlKCWRL9wmY6

# check insecure setting

- "server.insecure: ture" is not default

$ k get cm
NAME                        DATA   AGE
argocd-cm                   9      8m41s
argocd-cmd-params-cm        1      8m41s
argocd-gpg-keys-cm          0      8m41s
argocd-notifications-cm     0      8m41s
argocd-rbac-cm              0      8m41s
argocd-ssh-known-hosts-cm   1      8m41s
argocd-tls-certs-cm         0      8m41s
kube-root-ca.crt            1      42m

$ k get cm argocd-cmd-params-cm -oyaml
apiVersion: v1
data:
  server.insecure: "true"
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"server.insecure":"true"},"kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-cmd-params-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-cmd-params-cm","namespace":"argocd"}}
  creationTimestamp: "2026-04-04T11:22:18Z"
  labels:
    app.kubernetes.io/name: argocd-cmd-params-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cmd-params-cm
  namespace: argocd
  resourceVersion: "14988"
  uid: 04002759-41aa-41a8-bc49-58c5f808d049
