# Install MetalLBload Loadbalancer service

Configure the IP addresses that can match to the cluster in this config file and save it as ```config.yml```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: metallb-config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 172.16.16.240-172.16.16.250
```

The addresses is the range, where MetalLBLoad Loadbalancer will provide an IP address from.

Deploy this ConfigMap now in the cluster using helm:

```sh
helm install metallb stable/metallb --namespace metallb-system --create-namespace 

kubectl apply -f ./config.yaml
```
