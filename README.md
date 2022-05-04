# Pre requisites
- container runtime like [docker](https://docs.docker.com/get-docker/), [podman](https://podman.io/getting-started/installation)
- [minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)

# Create the Cluster
Start minikube with default settings:
```bash
minikube start --network-plugin=cni
```

Start minikube with specified resource limits and driver:
```bash
minikube start --cpus=8 --memory=16384 --driver='podman' --network-plugin=cni
```

# Install Cilium
## Cilium CLI
Install the latest version of the Cilium CLI. The Cilium CLI can be used to install Cilium, inspect the state of a Cilium installation, and enable/disable various features.

## Linux
```bash
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-amd64.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
rm cilium-linux-amd64.tar.gz{,.sha256sum}
```
## macOS
```bash
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/latest/download/cilium-darwin-amd64.tar.gz{,.sha256sum}
shasum -a 256 -c cilium-darwin-amd64.tar.gz.sha256sum
sudo tar xzvfC cilium-darwin-amd64.tar.gz /usr/local/bin
rm cilium-darwin-amd64.tar.gz{,.sha256sum}
```
## Other
Download the binary for your platform from [https://github.com/cilium/cilium-cli/releases/latest](https://github.com/cilium/cilium-cli/releases/latest)

# Deploy Cilium and Hubble
Install Cilium and verify it has been properly installed.
```bash
❯ cilium install

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
Cluster Pods:     0/1 managed by Cilium
Image versions    cilium             quay.io/cilium/cilium:v1.11.3@sha256:cb6aac121e348abd61a692c435a90a6e2ad3f25baa9915346be7b333de8a767f: 1
                  cilium-operator    quay.io/cilium/operator-generic:v1.11.3@sha256:5b81db7a32cb7e2d00bb3cf332277ec2b3be239d9e94a8d979915f4e6648c787: 1
```

Install Hubble and verify it has been properly installed.
```bash
❯ cilium hubble enable --ui

❯ cilium status --wait                                                                                                       ﴱ default
    /¯¯\
 /¯¯\__/¯¯\    Cilium:         OK
 \__/¯¯\__/    Operator:       OK
 /¯¯\__/¯¯\    Hubble:         OK
 \__/¯¯\__/    ClusterMesh:    disabled
    \__/

DaemonSet         cilium             Desired: 1, Ready: 1/1, Available: 1/1
Deployment        cilium-operator    Desired: 1, Ready: 1/1, Available: 1/1
Deployment        hubble-relay       Desired: 1, Ready: 1/1, Available: 1/1
Deployment        hubble-ui          Desired: 1, Ready: 1/1, Available: 1/1
Containers:       cilium             Running: 1
                  cilium-operator    Running: 1
                  hubble-relay       Running: 1
                  hubble-ui          Running: 1
Cluster Pods:     3/3 managed by Cilium
Image versions    cilium             quay.io/cilium/cilium:v1.11.3@sha256:cb6aac121e348abd61a692c435a90a6e2ad3f25baa9915346be7b333de8a767f: 1
                  cilium-operator    quay.io/cilium/operator-generic:v1.11.3@sha256:5b81db7a32cb7e2d00bb3cf332277ec2b3be239d9e94a8d979915f4e6648c787: 1
                  hubble-relay       quay.io/cilium/hubble-relay:v1.11.3@sha256:7256ec111259a79b4f0e0f80ba4256ea23bd472e1fc3f0865975c2ed113ccb97: 1
                  hubble-ui          quay.io/cilium/hubble-ui:v0.8.5@sha256:4eaca1ec1741043cfba6066a165b3bf251590cf4ac66371c4f63fbed2224ebb4: 1
                  hubble-ui          quay.io/cilium/hubble-ui-backend:v0.8.5@sha256:2bce50cf6c32719d072706f7ceccad654bfa907b2745a496da99610776fe31ed: 1
                  hubble-ui          docker.io/envoyproxy/envoy:v1.18.4@sha256:e5c2bb2870d0e59ce917a5100311813b4ede96ce4eb0c6bfa879e3fbe3e83935: 1
```

# Weather App
We will deploy a Weather App to get familiar with Cilium and Hubble. Deploy the Weather App and forward the frontend port.

```bash
kubectl apply -f weather-app/
kubectl port-forward -n weather svc/weather-app-service 3000:80
```

Open [http://localhost:3000/](http://localhost:3000/) in your browser to access the Weather App.

# Hubble UI
Hubble provides a graphical user interface which displays a service map of your service dependencies. To access Hubble UI, you can use the following command to forward the port of the web frontend to your local machine:
```bash
kubectl port-forward -n kube-system svc/hubble-ui 12000:80
```

Open [http://localhost:12000](http://localhost:12000) in your browser to access the Hubble UI and select the weather namespace.

In the Weather App search for a few locations and observe the requests in the Hubble UI.
![](./images/ui.gif)

# References
- https://cilium.io/
- https://github.com/amreshh/weather