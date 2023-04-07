# gpu-operator-rke2

Install GPU-operator docs from Nvidia: https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/getting-started.html

## Guide:
- Step 1: Install K8s Cluster with RKE2 using install-rke-guide.docx or using Rancher.
- Step 2: Create containerd GPU config.toml.tmpl file on each nodes and restart rke2 (follow install-rke-guide.docx)
- Step 3: run this command to install GPU Operator: 
```helm install -n gpu-operator --create-namespace \
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
