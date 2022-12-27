# initContainers implementation & testing

This directory contains Yamls to implement & test initcontainers inside k3s cluster. Following are the instructions on how to test it:

- As part of this setup, we will deploy `init-container-pod` inside `init-container-ns` namespace which contains two initContainers inside itself. These initcontainers will check the `init-app-svc` & `init-db-svc` services in the same namespace before deploying the main container
- Deploy the `init-container-pod` pod
```
kubectl apply -f init_container.yaml
```
- Now check the status of the pod, it should be in `Init:0/2` state
```
kubectl -n init-container-ns get pods
```
- Also, check the logs of the `init-app-svc` container using below command. You should see `waiting for init-app-svc` logs:
```
kubectl -n init-container-ns logs init-container-pod -c init-app-svc
```
- Now, deploy the `init-app-svc` service
```
kubectl apply -f init_app_svc.yaml
```
- Check the logs of `init-app-svc` container again, this time, you should be able to see the DNS for `init-app-svc` service and the `kubectl -n init-container-ns get pods` should show the status as `Init:1/2`
- Now, deploy the `init-db-svc` service
```
kubectl apply -f init_db_svc.yaml
```
- Check the status of the pod as well as the log of the pod itself. It should show the log as `The app is running!`
```
kubectl -n init-container-ns get pods
kubectl -n init-container-ns logs init-container-pod 
```
