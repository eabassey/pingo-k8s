## Requirements
- Make sure you have a kubernetes cluster running
- Make sure you are pointing to the right config context if you have multiple kubernetes contexts as your target
- Install `helm` on your machine. On Mac just do `brew install helm`
- Lastly make sure you have `kubectl` installed to interact with the kube-api-server.

## Installing Linkerd
Linkerd is a service mesh that provides traffic management, Network Security, and Observability in a microservices environment. To start, install by running:
-  `curl -sL https://run.linkerd.io/install | sh` or `brew install linkerd` # to install the linkerd command on your machine
- then `linkerd check --pre` to validate the installation
- then `linkerd install | kubectl apply -f -` to install the control plane
- then `linkerd check` to validate the install again
- then `linkerd viz install | kubectl apply -f -` to enable the dashboard features
- then `linkerd check` to validate again
- then run `linkerd viz dashboard &` to open the dashboard.


## Installing Contour Ingress
Contour is an ingress controller for handling traffic into the k8s cluster. To install contour ton integrate well with linkerd, run the following:
```bash
# install Contour
kubectl apply -f https://projectcontour.io/quickstart/contour.yaml

# create a service account (optional)
kubectl apply -f - << EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: envoy
  namespace: projectcontour
EOF

# add service account to envoy (optional)
kubectl patch daemonset envoy -n projectcontour --type json -p='[{"op": "add", "path": "/spec/template/spec/serviceAccount", "value": "envoy"}]'

# auto mount the service account token (required)
kubectl patch daemonset envoy -n projectcontour --type json -p='[{"op": "replace", "path": "/spec/template/spec/automountServiceAccountToken", "value": true}]'

# inject linkerd first into the DaemonSet
kubectl -n projectcontour get daemonset -oyaml | linkerd inject - | kubectl apply -f -

# inject linkerd into the Deployment
kubectl -n projectcontour get deployment -oyaml | linkerd inject - | kubectl apply -f -
```
## Installing Keycloak
Once you are in this directory, just use helm to install keycloak by running `helm install keycloak keycloak -n keycloak --create-namespace` to install keycloak in a separate namespace with all its components. You will have a namespace called `keycloak` afterwards.

## Installing PWA App
In this same directory, run `helm install pwa pwa -n pingo-dev --create-namespace` to install the frontend application into the kubernetes cluster under the `pingo-dev` namespace. You can then access the frontend app at `http://pingo.dev.pwa.127.0.0.1.nip.io/`.

## Verify Installations
Just run `kubectl get all --all-namespaces` to verify that all the components are in their right namespaces and are up and running. Also run `istioctl analyze` to make sure there are no issues.

## Accessing Keycloak
If you are using the defaults in this configuration, just go to your browser and reach `http://pingo.dev.keycloak.127.0.0.1.nip.io/`. Your keycloak instance should be running.  The admin credentials are hardcoded in the `keycloak-deployment.yaml` file as `username: admin | password: admin` just for testing. In prod, we can use `Secrets` for them. 

## Installing Name Generator Microservice
In this same directory, run `helm install name-gen name-gen -n pingo-dev --create-namespace` to install the name generator microservice into the kubernetes cluster under the `pingo-dev` namespace. You won't be able to access the service directly outside the cluster since there is no ingress gateway setup for it. But other microservices within the cluster can access it. 

## Installing Name Processor Microservice
In this same directory, run `helm install name-processor name-processor -n pingo-dev --create-namespace` to install the name generator microservice into the kubernetes cluster under the `pingo-dev` namespace. You won't be able to access the service directly outside the cluster since there is no ingress gateway setup for it. But other microservices within the cluster can access it. 
## Installing Telemetry


## Installing ArgoCD for CI/CD


## Installing `fortio` for load testing


## 