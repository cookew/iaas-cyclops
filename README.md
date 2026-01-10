# About

This is a mirror of my of git repository I use with [ArgoCD](https://argoproj.github.io/cd/) for deploying my Kubernetes cluster cluster that was originally deployed with [ansible-kubernetes](https://github.com/cyclops-k8s/ansible-kubernetes).

# Deploy

1. kustomize build --enable-helm --helm-command ~/bin/helm3 deploy/ | kubectl apply --force-conflicts --server-side -f -
2. Re-encrypt the sealed secrets, update the git repo

# Sealed Secrets

Sealed secrets are used for the cert-manager cluster CA cert and key, and for the Argo SSH key. The sealed secrets should be placed in those app's sealed secret directory.

* /apps/argocd/sealedsecrets
* /apps/cert-manager-certs/sealedsecrets
