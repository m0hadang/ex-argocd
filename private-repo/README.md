
### private repo error

```
k apply -f application-in-private.yaml

[error log]
Failed to load target state: failed to generate manifest for source 1 of 1: rpc error: code = Unknown desc = failed to list refs: authentication required: Repository not found.
```

### argocd repo status

```
argocd repo list
TYPE  NAME  REPO                                     INSECURE  OCI    LFS    CREDS  STATUS  MESSAGE                                                                                                                                                                                                                                                                                    PROJECT
git         git@github.com:m0hadang/test-argocd.git  false     false  false  false  Failed  Unable to connect to repository: rpc error: code = Unknown desc = error testing repository connectivity: unable to ls-remote HEAD on repository: failed to list refs: ssh: handshake failed: ssh: unable to authenticate, attempted methods [none publickey], no supported methods remain
```

### manual add
```
1. Generate an SSH key pair (if you don't have one):
ssh-keygen -t ed25519 -C "argocd" -f ~/.ssh/argocd_id_ed25519 -N ""

2. Add the public key to GitHub → repo Settings → Deploy keys (read-only is enough).

3. Register the private key with ArgoCD:
argocd repo add git@github.com:m0hadang/test-argocd.git \
	--ssh-private-key-path ~/.ssh/argocd_id_ed25519
```
