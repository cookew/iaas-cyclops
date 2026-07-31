# Kubernetes Deployment

This is a mirror of my of git repository I use with [ArgoCD](https://argoproj.github.io/cd/) for deploying my Kubernetes cluster cluster that was originally deployed with [ansible-kubernetes](https://github.com/cyclops-k8s/ansible-kubernetes).

## Deploy

1. Deploy manifests.

    ```bash
    kustomize build --enable-helm deploy/ | kubectl apply --server-side -f -
    ```

1. Re-encrypt the sealed secrets, update the git repo.
1. Re-deploy manifests.

    ```bash
    kustomize build --enable-helm deploy/ | kubectl apply --server-side -f -
    ```

1. Update ceph minimum placement groups, if needed.

    ```bash
    # Determine if there is a problem
    kubectl -n rook-ceph-cluster describe cephobjectstore
    # Fix pg_num_min
    kubectl -n rook-ceph-cluster exec deploy/rook-ceph-tools -- ceph osd pool set .rgw.root pg_num_min 8
    ```

1. Delete the super-user group to auto-add admin roles from various apps to the group.

    ```bash
    kubectl delete roles.group.keycloak.crossplane.io super-users
    ```

## Sealed Secrets

Sealed secrets are used for the cert-manager cluster CA cert and key, and for the Argo SSH key. The sealed secrets should be placed in those app's sealed-secrets directory.

* /apps/argocd/sealed-secrets
* /apps/cert-manager-certs/sealed-secrets

## Login info

ArgoCD

* Username: admin
* Password:

    ```bash
    kubectl -n argocd get secrets argocd-secret -o go-template='{{index .data "admin.password" | base64decode | printf "%s\n"}}'
    ```

Ceph

* Username: admin
* Password:

    ```bash
    kubectl -n rook-ceph-cluster get secrets rook-ceph-dashboard-password -o go-template='{{index .data "password" | base64decode | printf "%s\n"}}'
    ```

GitLab

* Username: root
* Password:

    ```bash
    kubectl -n gitlab get secrets gitlab-gitlab-initial-root-password -o go-template='{{index .data "password" | base64decode | printf "%s\n"}}'
    ```

* Use this URL to login as root to assign your user admin priviledges. <https://gitlab.apps.iaas.wcooke.me/users/sign_in?auto_sign_in=false>

Guacamole

The username and password need to be changed after deployment. These are hard-coded and not set in a secret.

* Username: guacadmin
* Password: guacadmin

Steps for setup:

* Set the ```EXTENSION_PRIORITY``` environment variable to ```"*, openid"``` in the values.yaml file.
* Redeploy the app.
* Log in as guacadmin
* Create a different admin user account.
* Assign the new admin account a password.
* Assign all the permissions to the admin user.
* Add the guacamole-admin group.
* Assign all the permissions to the guacamole-admin group.
* Set the ```EXTENSION_PRIORITY``` environment variable to ```"openid"``` in the values.yaml file.
* Redeploy the app.
* Log in with a user that has the guacamole-admin role in Keycloak.

Keycloak

* Username:

    ```bash
    kubectl -n keycloak get secrets keycloak-user -o go-template='{{index .data "username" | base64decode | printf "%s\n"}}'
    ```

* Password:

    ```bash
    kubectl -n keycloak get secrets keycloak-user -o go-template='{{index .data "password" | base64decode | printf "%s\n"}}'
    ```
