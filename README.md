# Pre requisites
- container runtime like docker, podman
- Minikube > v1.12.1
- kubectl > v.110.0+

# Create the Cluster

```bash
minikube start --network-plugin=cni --cni=false
```

Running with podman / cri-o and resources
```bash
minikube start --cpus=8 --memory=16384 --driver='podman' --container-runtime='cri-o' --network-plugin=cni --cni=cilium
```

Mount the eBPF filesystem
```bash
minikube ssh -- sudo mount bpffs -t bpf /sys/fs/bpf
```

# Install Cilium
```bash
kubectl create -f https://raw.githubusercontent.com/cilium/cilium/v1.9/install/kubernetes/quick-install.yaml
```


# Install the Cilium CLI
## Linux
```bash
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-amd64.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
rm cilium-linux-amd64.tar.gz{,.sha256sum}
```

## Linux (openSUSE)
```bash
sudo zypper install cilium-cli
sudo ln -s /usr/bin/cilium-cli /usr/bin/cilium
```

## macOS
```bash
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/latest/download/cilium-darwin-amd64.tar.gz{,.sha256sum}
shasum -a 256 -c cilium-darwin-amd64.tar.gz.sha256sum
sudo tar xzvfC cilium-darwin-amd64.tar.gz /usr/local/bin
rm cilium-darwin-amd64.tar.gz{,.sha256sum}
```

## Other
See [Cilium Releases](https://github.com/cilium/cilium-cli/releases/latest)

# Validate the Installation
To validate that Cilium has been properly installed, you can run:
```bash
❯ cilium status --wait                                                                                                              
    /¯¯\
 /¯¯\__/¯¯\    Cilium:         OK
 \__/¯¯\__/    Operator:       OK
 /¯¯\__/¯¯\    Hubble:         disabled
 \__/¯¯\__/    ClusterMesh:    disabled
    \__/

DaemonSet         cilium             Desired: 1, Ready: 1/1, Available: 1/1
Deployment        cilium-operator    Desired: 1, Ready: 1/1, Available: 1/1
Containers:       cilium             Running: 1
                  cilium-operator    Running: 1
Cluster Pods:     1/1 managed by Cilium
Image versions    cilium             quay.io/cilium/cilium:v1.9.9@sha256:a85d5cff13f8231c2e267d9fc3c6e43d24be4a75dac9f641c11ec46e7f17624d: 1
                  cilium-operator    quay.io/cilium/operator-generic:v1.9.9@sha256:3726a965cd960295ca3c5e7f2b543c02096c0912c6652eb8bbb9ce54bcaa99d8: 1
```

Run the following command to validate that your cluster has proper network connectivity (this can take ~):
```bash
❯ cilium connectivity test
```