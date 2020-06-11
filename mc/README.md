
Please see [kenthua/anthos-cd-pipeline](https://github.com/kenthua/anthos-cd-pipeline) for the latest version.

## DEPRECATED

### Multi-cluster deployment, based on [istio replicated control planes](https://istio.io/docs/setup/install/multicluster/gateways/) - istio 1.4.9 and 1.5.2
- Istio
  - Setup shared istio certificates
  - Install replicated control plane istio applicable to enivronment. The `istioresources` folder has an IstioOperator applicable to their respective provider.  Navigate to istio version folder, `1.4.9 or 1.5.2`
```
istioctl manifest apply -f istiooperator-{aws,gcp}.yaml
```
  - Setup DNS (per instructions)

- Bank of Anthos - This configuration will deploy ledger db services in cluster1 and accounts db services in cluster2
  - Generate RSA key pair in mc/resources
```
openssl genrsa -out jwtRS256.key 4096
openssl rsa -in jwtRS256.key -outform PEM -pubout -out jwtRS256.key.pub
```
  - Modify `overlays/c1-gcp/serviceentry-patch.yaml` with appropriate remote ingress gateway address
```
export REMOTE_ADDRESS=your_remote_address
sed -i "s/REMOTE_ADDRESS/${REMOTE_ADDRESS}/g" overlays/c1-gcp/serviceentry-patch.yaml
```
  - Deploy each folder mc/overlays/c1-gcp & mc/overlays/c2-aws to their respective clusters (We are using load_restrictor so we are able to leave the kubernetes_manifests folder alone
```
kustomize build --load_restrictor LoadRestrictionsNone | kubectl apply -f -
```
