# Network Policy implementation & testing

This directory contains Yamls to implement & test network policy inside k3s cluster. Following are the instructions on how to test it:

- Deploy Nginx in `nginx-deploy` namespace in the form of deployment and configure the service to expose it over Nodeport
```
kubectl apply -f nginx_deploy.yaml
```
- Deploy another busybox container in `busybox-ns` namespace
```
kubectl apply -f busybox_pod.yaml
```
- Login to the busy box container & test the connectivity to Nginx pods (which ae in different namespace) by using telnet to clusterIP of Nginx service over port `80`. This should work due to default networking behaviour of k8s where connectivity between different namespace is allowed
- Now, implement a network policy which will deny all ingress to the resources in `nginx-deploy` namespace
```
kubectl apply -f default_deny_network_policy.yaml
```
- Login to the busy box container & test the connectivity to Nginx pods. This time, it should not work due to network policy implementation
- Now, customize the policy to allow ingress to resources in `nginx-deploy` namespace only from resources in `busybox-ns` over port `80`
```
kubectl apply -f custom_allow_network_policy.yaml
```
- Again, login to the busy box container & test the connectivity to Nginx pods. This time, it should work
- Spin up another busybox instance in the default namespace and test the connectivity to resources in `nginx-deploy`. This should not work since this ingress is not allowed
```
kubectl apply -f default_ns_busybox.yaml
```
