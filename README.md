# gpu-operator-rke2

Install GPU-operator docs from Nvidia: https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/getting-started.html

## Guide:
### Step 1: 
Install K8s Cluster with RKE2 using install-rke-guide.docx or Rancher, or follow [official guide](https://ranchermanager.docs.rancher.com/v2.5/how-to-guides/new-user-guides/kubernetes-cluster-setup/rke2-for-rancher)
### Step 2: 
Create containerd GPU config file `/var/lib/rancher/rke2/agent/etc/containerd/config.toml.tmpl` on each node
```
version = 2
[plugins]
  [plugins."io.containerd.grpc.v1.cri"]
    enable_selinux = false 
    sandbox_image = "index.docker.io/rancher/pause:3.6" 
    stream_server_address = "127.0.0.1" 
    stream_server_port = "10010"

    [plugins."io.containerd.grpc.v1.cri".containerd] 
      disable_snapshot_annotations = true 
      snapshotter = "overlayfs" 
      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes] 
        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc] 
          runtime_type = "io.containerd.runc.v2
  [plugins."io.containerd.internal.v1.opt"] 
    path = "/data/rancher/rke2/agent/containerd"
```
Then restart rke2 on all nodes 
  + master node: `systemctl restart rke2-server.service`
  + worker node: `systemctl restart rke2-agent.service`
### Step 3: 
Run this command to install GPU Operator:
```
helm install -n gpu-operator --create-namespace \
  nvidia/gpu-operator $HELM_OPTIONS \
    --set toolkit.env[0].name=CONTAINERD_CONFIG \
    --set toolkit.env[0].value=/var/lib/rancher/k3s/agent/etc/containerd/config.toml.tmpl \
    --set toolkit.env[1].name=CONTAINERD_SOCKET \
    --set toolkit.env[1].value=/run/k3s/containerd/containerd.sock \
    --set toolkit.env[2].name=CONTAINERD_RUNTIME_CLASS \
    --set toolkit.env[2].value=nvidia \
    --set toolkit.env[3].name=CONTAINERD_SET_AS_DEFAULT \
    --set-string toolkit.env[3].value=true
```
