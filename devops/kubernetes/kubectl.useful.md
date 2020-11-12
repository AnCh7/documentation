##### Get all latest resources with versions:
```bash
for kind in `kubectl api-resources | tail +2 | awk '{ print $1 }'`; do kubectl explain $kind; done | grep -e "KIND:" -e "VERSION:"
```

##### Copy files from a pod:
```bash
kubectl cp default/XXXXXXXXXX:/app ~/Downloads/app
```

##### SSH to pod:
```bash
kubectl exec -it XXXXXXXXXX -- /bin/bash
```

##### SSH to container in pod:
```bash
kubectl exec -it XXXXXXXXXX --container YYYYYYYYY -- /bin/bash
```