# k3s-multi-node-setup
This setup requires (but not limited) to have the following nodes:

- k3s master node (No. of nodes: 1)
- k3s worker nodes (No. of nodes: 2)

### Pre-requisites to prepare the nodes:
This setup assumes the underlying infrastructure deployed on AWS. If that so, there are following steps that needs to be carried out prior to k3s installation on nodes. Following are those steps outlined:

- Deploy 3 instances (1 master & 2 worker nodes)
- Keep one separate security group for master node and another one shared amongst the worker nodes
- On master node security group, allow following inbound rules:
  - ICMP  [From Public IP address of worker instance (This is optional, just to test node reachability)]
  - SSH   [From public IP where you're trying to connect to instance]
  - 6443  [From Private IP of worker nodes. This is required to be able to reach Kube-API server]
  - 10250 [From Private IP of worker nodes. This is required for Kubelet]
- On worker nodes security group, allow following inbound rules:
  - ICMP [From Public IP address of worker instance (This is optional, just to test node reachability)]
  - SSH  [From public IP where you're trying to connect to instance]
  - Application ports [In case if there are any specific application on the cluster which demands external access. For network policy & sidecar testing, it needs port `30007` & `30010` to be opened to reach Nginx web UI to verify]
- Modify hostnames of all 3 nodes (master & worker) and make the permanent by adding them in `/etc/hostname` file
- Also, add host entries for each hosts in `/etc/hosts` file to be able to resolve the node from other nodes

### k3s installation
- Install k3s on master node using the following command:
```
curl -sfL https://get.k3s.io | sh -
```
- Once its successfully installed, verify the `k3s` service status using following:
```
sudo systemctl status k3s.service
```
- On the master node, go to `/var/lib/rancher/k3s/server/` and retrieve the token from `agent-token` file which is needed to configure worker nodes
- On the worker nodes, run following command to add them in the cluster:
```
curl -sfL https://get.k3s.io | K3S_URL=https://<hostname_of_master_node>:6443 K3S_TOKEN=<retrieved_token> sh -
```
- Once completed, verify `k3s-agent` service on the worker nodes:
```
sudo systemctl status k3s-agent.service
```
- Once this is completed and all the services are running, verify the cluster nodes by running following on master node:
```
kubectl get nodes
kubectl get pods -A
```

### Troubleshoot
- In the event the installation on worker nodes remain stuck at `[INFO]  systemd: Starting k3s-agent`, please make sure all the necessary ports in security groups are allowed
- Also, verify if you're able to reach master node from worker nodes over port `6443` using utilities such as `telnet` or `dig`
