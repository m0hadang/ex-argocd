# install

```
Install Argocd Helm Chart as following:

kubectl create ns argocd
helm repo add argo https://argoproj.github.io/argo-helm
helm repo list
helm upgrade --install argocd argo/argo-cd -n argocd
```

# install - using nodeport


```
helm upgrade --install argocd argo/argo-cd -n argocd -f nodeport-value.yaml
```


# install - using ingress(SSL-Passthrough)

- argocd ingress configuration
  - https://argo-cd.readthedocs.io/en/latest/operator-manual/ingress/#kubernetesingress-nginx
- update value

```
...
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:                                              # <-- put this
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"  # 
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"     #
...
```

```
# Note that the nginx.ingress.kubernetes.io/ssl-passthrough annotation requires that the --enable-ssl-passthrough flag be added to the command line arguments to nginx-ingress-controller

k edit deploy ingress-nginx-controller -n ingress-nginx
...
--enable-ssl-passthrough
...

helm upgrade --install argocd argo/argo-cd -n argocd -f ingress-value.yaml

k get ingress
NAME            CLASS   HOSTS                 ADDRESS    PORTS   AGE
argocd-server   nginx   mohadang.argocd.com   10.0.1.5   80      19s
```

# get secret

```
k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

# uninstall

```
helm uninstall argocd -n argocd
```

# argocd helm chart

- https://github.com/argoproj/argo-helm/blob/main/charts/argo-cd/README.md

# get argocd helm values

```
helm show values argo/argo-cd > values.yaml
```
