# WSL Ubuntu Tool Installation

Use this guide when students are using Ubuntu inside WSL 2 on Windows.

These steps install:

- kubectl
- Minikube

The labs use Docker as the Minikube driver, so Docker must also be available inside the Ubuntu WSL terminal.

## Goal

Prepare a WSL Ubuntu machine for the Kubernetes 3-day training.


## Prerequisites

- Windows has WSL 2 enabled.
- Ubuntu is installed from the Microsoft Store or with `wsl --install`.
- Docker Desktop is installed on Windows.
- Docker Desktop WSL integration is enabled for the Ubuntu distribution.
- The student can open an Ubuntu terminal.

## 1. Verify WSL Version

Run this command from PowerShell or Windows Terminal:

```powershell
wsl.exe -l -v
```

Expected output:

```text
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

If Ubuntu is using WSL 1, upgrade it from PowerShell:

```powershell
wsl.exe --set-version Ubuntu 2
```

Set WSL 2 as the default for future distributions:

```powershell
wsl.exe --set-default-version 2
```

## 2. Verify Docker from Ubuntu

Start Docker Desktop on Windows.

In Docker Desktop, enable WSL integration:

1. Open Docker Desktop.
2. Go to Settings.
3. Go to Resources.
4. Go to WSL Integration.
5. Enable integration for Ubuntu.
6. Select Apply and restart Docker Desktop if asked.

Open the Ubuntu terminal and run:

```bash
docker version
docker ps
docker info --format '{{.OSType}}'
```

Expected output includes:

```text
Client:
 Version: ...

Server:
 Engine:
  Version: ...
```

Expected output from `docker info --format '{{.OSType}}'`:

```text
linux
```

Docker Desktop must be using Linux containers, not Windows containers.

If `docker ps` works, Minikube can use the Docker driver.

## 3. Update Ubuntu Packages

Run these commands inside Ubuntu:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
sudo mkdir -p -m 755 /etc/apt/keyrings
```

## 4. Install kubectl

Add the Kubernetes apt repository:

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.36/deb/Release.key | sudo gpg --dearmor --yes -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.36/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
```

Install kubectl:

```bash
sudo apt-get update
sudo apt-get install -y kubectl
```

Verify kubectl:

```bash
kubectl version --client
```

Expected output:

```text
Client Version: v1.36...
```

## 5. Install Minikube

Check your CPU architecture:

```bash
uname -m
```

Expected output is usually one of these:

```text
x86_64
```

```text
aarch64
```

For `x86_64` or AMD64, install the latest stable Minikube Debian package:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
rm minikube_latest_amd64.deb
```

For `aarch64` or ARM64, install the latest stable Minikube Debian package:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_arm64.deb
sudo dpkg -i minikube_latest_arm64.deb
rm minikube_latest_arm64.deb
```

Verify Minikube:

```bash
minikube version
```

Expected output:

```text
minikube version: v...
```

## 6. Start Minikube with Docker

Start the local Kubernetes cluster:

```bash
minikube start --driver=docker --cpus=2 --memory=4096 --kubernetes-version=v1.36.1
```

Expected output includes:

```text
Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

If startup fails or pods stay pending, check Docker Desktop resource limits and give Docker at least 2 CPUs and 4 GB RAM.

If you do not need a pinned Kubernetes version, you can use the latest Minikube default version instead:

```bash
minikube start --driver=docker --cpus=2 --memory=4096 
```

## 7. Verify the Cluster

Run:

```bash
kubectl get nodes
kubectl get pods -A
kubectl config current-context
```

Expected output:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   ...   v...
```

```text
minikube
```

## 8. Optional kubectl Completion

Install Bash completion:

```bash
sudo apt-get install -y bash-completion
```

Add kubectl completion and the short `k` alias:

```bash
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'alias k=kubectl' >> ~/.bashrc
echo 'complete -o default -F __start_kubectl k 2>/dev/null || true' >> ~/.bashrc
source ~/.bashrc
```

Test:

```bash
k version --client
```

## Common Errors and Fixes

### docker: Cannot connect to the Docker daemon

Make sure Docker Desktop is running on Windows.

Then verify WSL integration:

1. Open Docker Desktop.
2. Go to Settings.
3. Go to Resources.
4. Go to WSL Integration.
5. Enable Ubuntu.
6. Apply changes.

Restart WSL from PowerShell:

```powershell
wsl.exe --shutdown
```

Open Ubuntu again and test:

```bash
docker ps
```

### Exiting due to PROVIDER_DOCKER_NOT_RUNNING

Docker is not available to Minikube.

Fix:

```bash
docker ps
minikube start --driver=docker --cpus=2 --memory=4096
```

If `docker ps` fails, fix Docker Desktop WSL integration first.

### The connection to the server was refused

This usually means Minikube is not running yet.

Check:

```bash
minikube status
```

Start Minikube:

```bash
minikube start --driver=docker --cpus=2 --memory=4096
```

### Ubuntu is WSL 1

Minikube with Docker Desktop should use WSL 2.

Check from PowerShell:

```powershell
wsl.exe -l -v
```

Convert Ubuntu to WSL 2:

```powershell
wsl.exe --set-version Ubuntu 2
```

## Cleanup Commands

Stop Minikube:

```bash
minikube stop
```

Delete the local Minikube cluster:

```bash
minikube delete
```

## References

- Kubernetes kubectl Linux install docs: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
- Minikube start and Linux install docs: https://minikube.sigs.k8s.io/docs/start/
- Docker Desktop WSL 2 docs: https://docs.docker.com/desktop/features/wsl/
