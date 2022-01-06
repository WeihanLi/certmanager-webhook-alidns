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

helm install cert-manager jetstack/cert-manager -n cert-manager --create-namespace --set installCRDs=true --version v1.6.1
```

For more ways to install, see the docs from cert-manager <https://cert-manager.io/docs/installation/>

Install Ali DNS webhook for cert-manager

```bash
cd deploy/certmanager-webhook-alidns

# render template
helm template certmanager-webhook-alidns . --set issuer.create=true --set issuer.host=weihanli.top --set issuer.email=weihanli@outlook.com --set issuer.secret.accessKeyId=AliAccessKeyId --set issuer.secret.accessKeySecret=AliAccessKeySecret -n cert-manager

# install without creating ClusterIssuer
helm install certmanager-webhook-alidns . -n cert-manager

# install with creating ClusterIssuer
helm install certmanager-webhook-alidns . --set issuer.create=true --set issuer.host=weihanli.top --set issuer.email=weihanli@outlook.com --set issuer.secret.accessKeyId=AliAccessKeyId --set issuer.secret.accessKeySecret=AliAccessKeySecret -n cert-manager
```

## More

The docker image based on <https://github.com/kevinniu666/cert-manager-webhook-alidns>, you can build your own Dockerfile from source repo, and install with your own image with `--set image.repository=<your-image>`

Troubleshooting guide: <https://cert-manager.io/docs/faq/acme/>
