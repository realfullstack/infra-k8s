# RealFullStack Infra

This contains the kubernetes manifests required to run all realfullstack.com environments



## KubeSeal

Fetch the existing kubeseal cert

```bash
kubeseal \
 --controller-name=sealed-secrets \
 --controller-namespace=security \
 --fetch-cert > ./.secrets/kube-seal.pem
```

## Github packages credentials

update `.secrets/docker-config.json` with the desired auths configuration.
Than use kubeseal to generate a new sealed configuration.

```bash
kubectl create secret generic regcred  \
  --from-file=.dockerconfigjson=./.secrets/docker-config.json  \
  --type=kubernetes.io/dockerconfigjson  \
  --dry-run=client -o yaml  | kubeseal \
  --cert .secrets/kube-seal.pem \
  --scope cluster-wide \
  -o yaml > ./registry/sealed-regcred.yaml
```



## Manifests

```bash
# namespaces
k apply -f namespaces/

# docker secrets
k apply -f registry/ -n realfullstack-prod
k apply -f registry/ -n realfullstack-infra

# kustomize resources

k apply -f argocd/

```