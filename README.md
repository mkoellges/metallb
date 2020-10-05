# Install MetalLBload Loadbalancer service

change default settings in kube-proxy to ```strictARP: ture```

```sh
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl diff -f - -n kube-system

kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl apply -f - -n kube-system
```

Now deploy the Loadbalancer

```ssh
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml

# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

At least, configure the IP addresses that can match to the cluster in this config file and save it as ```config.yml```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 172.16.16.240-172.16.16.250
```

The addresses is the range, where MetalLBLoad Loadbalancer will provide an IP address from.

Deploy this ConfigMap now in the cluster

```sh
kubectl apply -f config.yaml
```
# metallbload
