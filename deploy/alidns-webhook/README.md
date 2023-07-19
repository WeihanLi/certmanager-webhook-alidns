# ACME webhook Ali DNS

The ACME issuer type supports an optional 'webhook' solver, which can be used
to implement custom DNS01 challenge solving logic.

This is useful if you need to use cert-manager with Ali yun DNS provider.

## Installation

Before use this, your should make sure you had cert-manager installed, if not please install cert-manager firstly.

You can install with helm using the following commands:

``` bash
helm repo add jetstack https://charts.jetstack.io

helm repo update

helm install cert-manager jetstack/cert-manager -n cert-manager --create-namespace --set installCRDs=true --version v1.12.2
```

For more ways to install, see the docs from cert-manager <https://cert-manager.io/docs/installation/>

Install Ali DNS webhook for cert-manager

```bash
# render template
helm template certmanager-alidns-webhook . --set groupName=weihanli.top --set issuer.create=true --set issuer.host=weihanli.top --set issuer.email=weihanli@outlook.com --set issuer.secret.accessKeyId=AliAccessKeyId --set issuer.secret.accessKeySecret=AliAccessKeySecret -n cert-manager

# install without creating ClusterIssuer
helm install certmanager-alidns-webhook . --set groupName=weihanli.top -n cert-manager

# install with creating ClusterIssuer and certificate
helm install certmanager-alidns-webhook . --set groupName=weihanli.top --set issuer.create=true --set issuer.host=weihanli.top --set issuer.email=weihanli@outlook.com --set issuer.secret.accessKeyId=AliAccessKeyId --set issuer.secret.accessKeySecret=AliAccessKeySecret -n cert-manager

# install with creating ClusterIssuer without certificate
helm install certmanager-alidns-webhook . --set groupName=weihanli.top --set issuer.create=true --set issuer.host=weihanli.top --set issuer.email=weihanli@outlook.com --set issuer.secret.accessKeyId=AliAccessKeyId --set issuer.secret.accessKeySecret=AliAccessKeySecret --set issuer.createCert=false -n cert-manager
```

Create lets-encrypt certificate separately

sample yaml for cert(`cert.yaml`)

``` yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: "weihanli-top-tls-cert"
  namespace: "cert-manager"
  labels:
    app: "weihanli-top-tls-cert"
spec:
  commonName: "weihanli.top"
  dnsNames:
  - "weihanli.top"
  - "*.weihanli.top"
  issuerRef:
    kind: ClusterIssuer
    name: letsencrypt
  secretName: "weihanli-top-tls-cert"
```

``` sh
kubectl apply -f cert.yaml -n cert-manager
```

## Ingress configure

Configure nginx-ingress default cert tls secret

``` yaml
controller:
  extraArgs:
    default-ssl-certificate: cert-manager/example-com-tls-cert
```

Install with helm

``` sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx --set controller.extraArgs.default-ssl-certificate=cert-manager/example-com-tls-cert --create-namespace

# for aks, append --set controller.service.externalTrafficPolicy=Local
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx --set controller.extraArgs.default-ssl-certificate=cert-manager/weihanli-top-tls-cert --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz --create-namespace
```

## More

The docker image based on <https://github.com/DEVmachine-fr/cert-manager-alidns-webhook>, you can build your own Dockerfile from source repo, and install with your own image with `--set image.repository=<your-image>`

Troubleshooting guide: <https://cert-manager.io/docs/faq/acme/>

## References

- <https://github.com/helm/charts/issues/1495#issuecomment-342275665>
- <https://kubernetes.github.io/ingress-nginx/user-guide/cli-arguments/>
- <https://kubernetes.github.io/ingress-nginx/troubleshooting/>
- <https://cert-manager.io/docs/faq/acme/>
- <https://cert-manager.io/docs/installation/>
- <https://cert-manager.io/docs/concepts/>
- <https://artifacthub.io/packages/helm/ingress-nginx/ingress-nginx>
- <https://github.com/DEVmachine-fr/cert-manager-alidns-webhook>
