# Install MetalLBload Loadbalancer service

Configure the IP addresses that can match to the cluster in this config file and save it as ```config.yml```

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
      - - 172.16.16.240-172.16.16.250
```

The addresses is the range, where MetalLBLoad Loadbalancer will provide an IP address from.

Prepare the kube-proxy for loadbalancer usage

```sh
# see what changes would be made, returns nonzero returncode if different
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl diff -f - -n kube-system

# actually apply the changes, returns nonzero returncode on errors only
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl apply -f - -n kube-system
```

Install the Metallb by manifest:

```sh
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.6/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.6/manifests/metallb.yaml
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

Otherwise try to install using helm:

```sh
helm install metallb stable/metallb --namespace metallb-system --create-namespace 
```
Last but not least, define the ip address range where to set the LB address:

```sh
kubectl apply -f ./config.yaml
```
