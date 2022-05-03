# Pre requisites
- container runtime like docker, podman
- Minikube > v1.12.1
- kubectl > v.110.0+

# Create the Cluster

```bash
minikube start --network-plugin=cni
```

Running with podman / cri-o and resources
```bash
minikube start --cpus=8 --memory=16384 --driver='podman' --network-plugin=cni
```

# Deploy Cilium and Hubble
```bash
cilium install
cilium hubble enable --ui
```

# Accessing the Graphical User Interface
Hubble provides a graphical user interface which displays a service map of your service dependencies. To access Hubble UI, you can use the following command to forward the port of the web frontend to your local machine:
```bash
kubectl port-forward -n kube-system svc/hubble-ui 12000:80
```

Open [http://localhost:12000](http://localhost:12000) in your browser to access the Hubble UI.
