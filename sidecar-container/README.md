# Sidecar container implementation & testing

This directory contains Yaml to implement & test sidecar container inside k3s cluster. Following are the instructions on how to test it:

- As part of this setup, we will deploy `sidecar-pod` inside `sidecar-container` namespace which consist of main Nginx container along with the sidecar container. The sidecar container will retrieve the Nginx access logs every 05 seconds
- Deploy the `sidecar-pod` pod
```
kubectl apply -f sidecar_pod.yaml
```
- Check the status of the pod, it should be in `Running` state
```
kubectl -n sidecar-container get pods
```
- Now, get the IP of node on which this pod is deployed
```
kubectl -n sidecar-container get pods -o jsonpath="{.items[0].status.hostIP}"
```
- Access the Nginx web UI in the browser using this IP over port `30010`
- Now check the logs of the sidecar container which should show Nginx web UI access logs
```
kubectl -n sidecar-container logs sidecar-pod -c sidecar-log-viewer
```
