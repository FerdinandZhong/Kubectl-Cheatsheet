# Self summarized kubectl commands cheatsheet (keep appending new cmds)

## Kubectl Config

check all subcommands
```
kubectl config --help
```

view config
```
kubectl config view
```

kubectl check current context
```
kubectl config current-context
```

kubectl swtich context
```
kubectl use-context foo-context
```

## Kubectl Inline Commands

### Create Objects

in-line initialize a pod (not recommended)
```
kubectl run pod-name --image=foo
kubectl run pod-name --iamge=foo --port=8080
```

use the --dry-run=client flag to preview the object that would be sent to your cluster, without really submitting it
```
kubectl run pod-name --image=foo --dry-run=client
```

kubectl initialize objects from definition yaml file (recommended way)
```
kubectl apply -f pod-definition.yml
kubectl apply -f deployment-definition.yml
```

kubectl create config map thru `from-literal`
```
kubectl create configmap config-name --from-literal=key1=value1 \
--from-literal=key2=value2
```

kubectl create secret thru `from-file`
```
kubectl create configmap config-name --from-file=path-to-file
```

kubectl create secret thru `from-literal`
```
kubectl create secret generic secret-name --from-literal=key1=value1 \
--from-literal=key2=value2
```

kubectl create secret thru `from-file`
```
kubectl create secret generic secret-name --from-file=path-to-file
```

kubectl add taint to node
```
kubectl taint nodes node-name key=value:<taint-effect>
```
taint-effect including `NoSchedule` |  `PreferNoSchedule` | `NoExecute`

### Check Objects

kubectl list all objects
```
kubectl get all -n foo-ns
```

kubectl list pod
```
kubectl get pod -n foo-ns
```

kubectl list pod with selector
```
kubectl get pod --selector key=value
kubectl get pod --selector key1=value1,key2=value2
```

kubectl list service
```
kubectl get svc -n foo-ns
```

kubectl list element with **regex**

```
ubectl get deployment -n foo-ns --no-headers=true | awk '/pattern/{print $1}'
```

do deletion with **regex**
```
ubectl get deployment -n foo-ns --no-headers=true | awk '/pattern/{print $1}' | xargs kubectl delete -n foo-ns deployment
```

kubectl get elements with **grep**
```
kubectl get pod -n foo-ns | grep Evicted
```

do deletion with **grep**
```
kubectl get pod -n foo-ns | grep Evicted | awk '{print $1}' | xargs kubectl delete -n foo-ns pod
```

kubectl inline cmd
```
kubectl exec --namespace=foo-ns foo-pod -- sh -c 'cmd';
```

## Rollout Commands

kubectl rollout status of a deployment
```
kubectl rollout status deployment foo-deployment
```

kubectl rollout history of a deployment
```
kubectl rollout history deployment foo-deployment
```

kubectl check the status of each revision individually by using the --revision flag
```
kubectl rollout history deployment nginx --revision=1
```

Use the `--record` flag to save the command used to create/update a deployment against the revision number. Then when we check the history of rollout, the `change-cause` is recorded
```
kubectl apply -f deployment.yml --record
kubectl rollout history deployment nginx --revision=1
```

kubectl roll back
```
kubectl rollout undo deployment foo-deployment
```

# Monitoring

1. install metrics-server

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

2. kubectl get pod memory and cpu usage
```
kubectl top pod foo-pod -n foo-ns
```
