## Requirements
- Make sure you have a kubernetes cluster running
- Make sure you are pointing to the right config context if you have multiple kubernetes contexts as your target
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

## Creating Namespaces
Once you are in this directory, run `kubectl apply -f namespaces.yaml` to create the isolated namespaces we need. This will create `keycloak` and `pingo-dev` namespaces. Each namespace is very isolated and is like a mini cluster on its own.
## Installing Microservices
After creating the namespaces, you can run `kubectl apply -f ./microservices` to get the microservices unto the cluster. You can check that all pods are running by cheking with `kubectl get pods -n pingo-dev` and `kubectl get pods -n keycloak`. If pods are all running, you can access the pwa app at `http://pingo.dev.pwa.127.0.0.1.nip.io/` and keycloak at `http://pingo.keycloak.127.0.0.1.nip.io/`

- You won't be able to access the `name-gen` and `name-processor` service directly outside the cluster since there is no ingress gateway setup for it. But other microservices within the cluster can access them. 

- To `manually` apply linkerd sidercar injection run `linkerd inject ./microservices | kubectl apply -f -`

## Accessing Keycloak
If you are using the defaults in this configuration, just go to your browser and reach `http://pingo.keycloak.127.0.0.1.nip.io/`. Your keycloak instance should be running.  The admin credentials are hardcoded in the `keycloak.yaml` file as `username: admin | password: admin` just for testing. In prod, we can use `Secrets` for them. 


## Installing Telemetry


## Installing ArgoCD for CI/CD


## Installing `fortio` for load testing


## 