### deploy priority

1. phases
2. wave
3. resourece type
  - ... sa > sc > dp ...

### phases priority

- pre-sync
  - ex) job, ...
  - ex) health check, db migration, ...
- sync(default)
  - after pre-sync ok
  - ex) svc, sa, deploy, ...
- post-sync
  - after sync ok
  - ex) job, ...
  - ex) notification to chatbot, ...
- sync-fail
  - when sync operation failed
  - ex) send message to user that last sync reulst is failed

### argocd hooks
- write on target resource(ex: Job, ...)

```
metadata:
  name: notify-post-job-failure
  annotations:
    argocd.argoproj.io/hook: PreSync     # pre sync
    # argocd.argoproj.io/hook: PostSync    # post sync
    # argocd.argoproj.io/hook: SyncFail    # sync fai
```

```
# can see completed pod(job)
k get pods -n ns-sync-phases-waves
NAME                            READY   STATUS      RESTARTS   AGE
health-check-pre-job-rct7w      0/1     Completed   0          60s
nginx-5888d7c657-6m2c4          1/1     Running     0          47s
notify-post-job-success-69q46   0/1     Completed   0          44s
j

k logs health-check-pre-job-rct7w -n ns-sync-phases-waves
PreSync ran

k logs notify-post-job-success-69q46 -n ns-sync-phases-waves
MESSAGE FROM ARGOCD: The last sync is:  *** Sync OK ***
```


### hook deletion policies
- write on target resource(ex: Job, ...)
- HookSucceeded
  - hook resource is deleted after the hook succeed
- HookFailed
  - hook resource is deleted after the hook failed
- BeforeHookCreation(default)
  - any existing hooker resource is deleted before the new one is created
  - it will be available until the new resource will be create 

### wave
- lowerest number is high priority
  - ... >> -3 >> -2 >> -1 >> 0 >> +1 >> +2 >> ...
  - 0 is default
