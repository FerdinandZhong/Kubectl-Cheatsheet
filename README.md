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

kubectl list pod in all namespace
```
kubectl get pods --all-namespaces
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
kubectl get deployment -n foo-ns --no-headers=true | awk '/pattern/{print $1}'
```

do deletion with **regex**
```
kubectl get deployment -n foo-ns --no-headers=true | awk '/pattern/{print $1}' | xargs kubectl delete -n foo-ns deployment
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

## Pod Logs and Processes

view pod logs
```
kubectl logs pod-name -n foo-ns
```

view logs from the previous container instance (useful for crashed pods)
```
kubectl logs pod-name -n foo-ns --previous
```

stream logs in real-time
```
kubectl logs -f pod-name -n foo-ns
```

view logs from a specific container in a multi-container pod
```
kubectl logs pod-name -c container-name -n foo-ns
```

show logs from the last hour
```
kubectl logs pod-name --since=1h -n foo-ns
```

show logs since a specific time
```
kubectl logs pod-name --since-time=2024-01-21T10:00:00Z -n foo-ns
```

execute a command in a pod and get interactive shell
```
kubectl exec -it pod-name -n foo-ns -- /bin/bash
```

execute a command in a specific container (multi-container pod)
```
kubectl exec -it pod-name -c container-name -n foo-ns -- /bin/bash
```

get running processes inside a pod
```
kubectl exec pod-name -n foo-ns -- ps aux
```

get environment variables in a pod
```
kubectl exec pod-name -n foo-ns -- env
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

# Authentication

cmds below can be used to verify if a service account have the premission to do operations in a specific namespaces

check if a service account can create deployment in a ns
```
kubectl auth can-i create deployment -n foo-ns --as system:serviceaccount:foo-ns:foo-sa
```

check if a service account can create service in a ns
```
kubectl auth can-i create service -n foo-ns --as system:serviceaccount:foo-ns:foo-sa
```

check if a service account can delete deployment in a ns
```
kubectl auth can-i delete deployment -n foo-ns --as system:serviceaccount:foo-ns:foo-sa
```

check if a service account can get deployments in a ns
```
kubectl auth can-i get deployment -n foo-ns --as system:serviceaccount:foo-ns:foo-sa
```

# Describing and Inspecting Resources

get detailed information about a resource
```
kubectl describe pod pod-name -n foo-ns
```

get detailed information about a deployment
```
kubectl describe deployment deployment-name -n foo-ns
```

get detailed information about a node
```
kubectl describe node node-name
```

get resource in YAML format
```
kubectl get pod pod-name -n foo-ns -o yaml
```

get resource in JSON format
```
kubectl get pod pod-name -n foo-ns -o json
```

# Troubleshooting

check pod status and events
```
kubectl get pod pod-name -n foo-ns -o wide
```

get events in a namespace to understand what's happening
```
kubectl get events -n foo-ns --sort-by='.lastTimestamp'
```

debug a pod by creating a debug container
```
kubectl debug pod-name -n foo-ns -it --image=busybox
```

port-forward to access a service locally
```
kubectl port-forward pod-name 8080:8080 -n foo-ns
```

port-forward to a service
```
kubectl port-forward svc/service-name 8080:8080 -n foo-ns
```

# Scaling and Managing Deployments

scale a deployment
```
kubectl scale deployment deployment-name --replicas=3 -n foo-ns
```

update deployment image
```
kubectl set image deployment/deployment-name container-name=new-image:tag -n foo-ns
```

# Label and Annotation Management

update or add a label to a resource
```
kubectl label pod pod-name key=value -n foo-ns
```

remove a label from a resource
```
kubectl label pod pod-name key- -n foo-ns
```

update or add an annotation to a resource
```
kubectl annotate pod pod-name key=value -n foo-ns
```