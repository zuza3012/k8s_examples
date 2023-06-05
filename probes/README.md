# Intro
Liveness probe will restart container on failure specified by command etc.
Readiness probe will make conatiner not available to users, e.g. will remove endpoint if port is not accessable but will not restart conatiner.

probes handlers:
- exec
- tcpSocket
- httpGet
# exec-liveness.yaml


## test 
```
k apply -f exec-liveness.yaml
```

Check pods with:

```
k get pods
```

and

```
k describe pod name_of_pod
```

You should see something like this:

```
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  9s    default-scheduler  Successfully assigned tut03/livenesspod to minikube
  Normal  Pulled     7s    kubelet            Container image "busybox:latest" already present on machine
  Normal  Created    7s    kubelet            Created container liveness
  Normal  Started    7s    kubelet            Started container liveness
```
## delete /tmp/healthy
```
k exec livenesspod -- rm /tmp/healthy
```

```
k get pods
```

```
k descibe pod name_of_pod
```

You should see:
```
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  4m18s              default-scheduler  Successfully assigned tut03/livenesspod to minikube
  Normal   Pulled     4m16s              kubelet            Container image "busybox:latest" already present on machine
  Normal   Created    4m16s              kubelet            Created container liveness
  Normal   Started    4m16s              kubelet            Started container liveness
  Warning  Unhealthy  12s (x3 over 22s)  kubelet            Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory
  Normal   Killing    12s                kubelet            Container liveness failed liveness probe, will be restarted
```

and after some time:

```
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  4m43s               default-scheduler  Successfully assigned tut03/livenesspod to minikube
  Warning  Unhealthy  37s (x3 over 47s)   kubelet            Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory
  Normal   Killing    37s                 kubelet            Container liveness failed liveness probe, will be restarted
  Normal   Pulled     7s (x2 over 4m41s)  kubelet            Container image "busybox:latest" already present on machine
  Normal   Created    7s (x2 over 4m41s)  kubelet            Created container liveness
  Normal   Started    7s (x2 over 4m41s)  kubelet            Started container liveness
```

# exec-readiness.yaml
```
k apply -f exec-readiness.yaml
```
Check if everything is okey with pod. Next we will test if readiness probe works, by removing healty file . 
(change port to check by probe inside deployent and apply again if tcpSocket used) 
You should see something like this:

```
Events:
  Type     Reason     Age   From               Message
  ----     ------     ----  ----               -------
  Normal   Scheduled  19s   default-scheduler  Successfully assigned tut03/readinesspod to minikube
  Normal   Pulled     18s   kubelet            Container image "busybox:latest" already present on machine
  Normal   Created    18s   kubelet            Created container readiness
  Normal   Started    18s   kubelet            Started container readiness
  Warning  Unhealthy  4s    kubelet            Readiness probe failed: cat: can't open '/tmp/healthy': No such file or directory
```

You can see that 0 readiness conatiners are ready (no restart happened)


