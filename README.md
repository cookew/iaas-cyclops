# About

This is a mirror of my of git repository I use with [ArgoCD](https://argoproj.github.io/cd/) for deploying my Kubernetes cluster cluster that was originally deployed with [ansible-kubernetes](https://github.com/cyclops-k8s/ansible-kubernetes).

# Deploy

1. Deploy manifests.
   ```
   kustomize build --enable-helm deploy/ | kubectl apply --server-side -f -
   ```
1. Re-encrypt the sealed secrets, update the git repo.
1. Re-deploy manifests.
   ```
   kustomize build --enable-helm deploy/ | kubectl apply --server-side -f -
   ```
1. Update ceph minimum placement groups, if needed.
   ```
   # Determine if there is a problem
   kubectl -n rook-ceph-cluster describe cephobjectstore
   # Fix pg_num_min
   kubectl -n rook-ceph-cluster exec deploy/rook-ceph-tools -- ceph osd pool set .rgw.root pg_num_min 8
   ```
1. Delete the super-user group to auto-add admin roles from various apps to the group.
   ```
   kubectl delete roles.group.keycloak.crossplane.io super-users
   ```

# Sealed Secrets

Sealed secrets are used for the cert-manager cluster CA cert and key, and for the Argo SSH key. The sealed secrets should be placed in those app's sealed secret directory.

* /apps/argocd/sealedsecrets
* /apps/cert-manager-certs/sealedsecrets

# Login info

ArgoCD
* Username: admin
* Password:
  ```
  kubectl -n argocd get secrets argocd-secret -o go-template='{{index .data "admin.password" | base64decode}}'
  ```

Ceph
* Username: admin
* Password:
  ```
   kubectl -n rook-ceph-cluster get secrets rook-ceph-dashboard-password -o go-template='{{index .data "password" | base64decode}}'
  ```

Keycloak
* Username:
  ```
  kubectl -n keycloak get secrets keycloak-user -o go-template='{{index .data "username" | base64decode}}'
  ```
* Password:
  ```
  kubectl -n keycloak get secrets keycloak-user -o go-template='{{index .data "password" | base64decode}}'
  ```
