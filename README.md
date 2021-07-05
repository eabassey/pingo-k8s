## Requirements
- Make sure you have a kubernetes cluster running
- Make sure you are pointing to the right config context if you have multiple kubernetes contexts as your target
- Install `helm` on your machine. On Mac just do `brew install helm`
- Lastly make sure you have `kubectl` installed to interact with the kube-api-server.

## Installing Contour Ingress
Contour is an ingress controller for handling traffic into the k8s cluster. To install run `helm repo add bitnami https://charts.bitnami.com/bitnami` to add the bitnami helm repo the run `helm install my-release bitnami/contour -n contour --create-namespace` to do the installation. This will create contour and its components in a `contour` namespace.

## Installing Keycloak
Once you are in this directory, just use helm to install keycloak by running `helm install pingo-dev-keycloak keycloak -n pingo-dev --create-namespace` to install keycloak in a separate namespace with all its components. You will have a namespace called `pingo-dev` to begin with.

## Verify Installations
Just run `kubectl get all --all-namespaces` to verify that all the components are in their right namespaces and are up and running.

## Accessing Keycloak
If you are using the defaults in this configuration, just go to your browser and reach `http://pingo.dev.keycloak.127.0.0.1.nip.io/`. Your keycloak instance should be running.  The admin credentials are hardcoded in the `keycloak-deployment.yaml` file as `username: admin | password: admin` just for testing. In prod, we can use `Secrets` for them. 