# Deployment

## Suppose you have deployed your application using a deployment controller. Assume the initial number of replicas is one. Write the steps needed to update a container's image using deployment, making sure that there is zero downtime.

## Steps

### 1. Create the deployment
```shell
[root@master]$ kubectl create -f kubia-deployment-v1.yaml
deployment.apps/kubia created
```

### 2. Set minReadySeconds (if not already set in deployment yaml) so that there's no downtime
```shell
[root@master]$ kubectl patch deployment kubia -p '{"spec": {"minReadySeconds": 10}}'
deployment.apps/kubia patched
```

### 3. Set new image (assuming that the new image is already available in repo)
```shell
[root@master]$ kubectl set image deployment kubia nodejs=luksa/kubia:v2
deployment.apps/kubia image updated
```

## Rolling update

### Initial deployment status
```shell
[root@master]$ kubectl -n ani get deployment -o wide
NAME    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES           SELECTOR
kubia   1/1     1            1           19s   nodejs       luksa/kubia:v1   app=kubia

[root@master]$kubectl -n ani get pods
NAME                     READY   STATUS    RESTARTS   AGE
kubia-59d857b444-2sgww   1/1     Running   0          19s
```

### Transition deployment status
```shell
# container with new version is created
[root@master]$ kubectl -n ani get deployment -o wide
NAME    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES           SELECTOR
kubia   1/1     1            1           69s   nodejs       luksa/kubia:v2   app=kubia

[root@master]$ kubectl -n ani get pods
kubia-59d857b444-2sgww   1/1     Running             0          69s
kubia-7d5c456ffc-4kd7l   0/1     ContainerCreating   0          1s


# old container runs for 10 seconds after new container is ready
[root@master]$ kubectl -n ani get deployment -o wide
NAME    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES           SELECTOR
kubia   2/1     1            1           76s   nodejs       luksa/kubia:v2   app=kubia

[root@master]$ kubectl -n ani get pods
NAME                     READY   STATUS    RESTARTS   AGE
kubia-59d857b444-2sgww   1/1     Running   0          76s
kubia-7d5c456ffc-4kd7l   1/1     Running   0          8s


# old container starts terminating 10s after new container is ready
[root@master]$ kubectl -n ani get deployment -o wide
NAME    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES           SELECTOR
kubia   1/1     1            1           91s   nodejs       luksa/kubia:v2   app=kubia

[root@master]$ kubectl -n ani get pods
NAME                     READY   STATUS        RESTARTS   AGE
kubia-59d857b444-2sgww   1/1     Terminating   0          91s
kubia-7d5c456ffc-4kd7l   1/1     Running       0          16s
```

### Final deployment status
```shell
[root@master]$ kubectl -n ani get deployment -o wide
NAME    READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES           SELECTOR
kubia   1/1     1            1           2m2s   nodejs       luksa/kubia:v2   app=kubia

[root@master]$ kubectl -n ani get pods
NAME                     READY   STATUS    RESTARTS   AGE
kubia-7d5c456ffc-4kd7l   1/1     Running   0          47s
```
