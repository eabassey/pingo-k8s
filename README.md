## Requirements
- Make sure you have a kubernetes cluster running
- Make sure you are pointing to the right config context if you have multiple kubernetes contexts as your target
- Lastly make sure you have `kubectl` installed to interact with the kube-api-server.

## Installing Contour Ingress
To install contour on AWS with NLB run `kubectl apply -f ./contour`

## Creating Namespaces
Once you are in this directory, run `kubectl apply -f namespaces.yaml` to create the isolated namespaces we need. This will create `keycloak` and `pingo-dev` namespaces. Each namespace is very isolated and is like a mini cluster on its own.
## Installing Microservices
After creating the namespaces, you can run `kubectl apply -f ./microservices` to get the microservices unto the cluster. You can check that all pods are running by cheking with `kubectl get pods -n pingo-dev` and `kubectl get pods -n keycloak`. If pods are all running, you can access the pwa app at `http://pingo.dev.pwa.127.0.0.1.nip.io/` and keycloak at `http://pingo.keycloak.127.0.0.1.nip.io/`

- You won't be able to access the `name-gen` and `name-processor` service directly outside the cluster since there is no ingress gateway setup for it. But other microservices within the cluster can access them. 

## Accessing Keycloak
If you are using the defaults in this configuration, just go to your browser and reach `http://pingo.keycloak.127.0.0.1.nip.io/`. Your keycloak instance should be running.  The admin credentials are hardcoded in the `keycloak.yaml` file as `username: admin | password: admin` just for testing. In prod, we can use `Secrets` for them. 

## Basic Metrics
To deploy and access the Kubernetes Metrics server, run `kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`. Then run `kubectl top nodes` to access cpu utilization. Note: This is not a monitoring tool. Just a basic stuff. 
## Installing Telemetry


## Installing ArgoCD for CI/CD


## Installing `fortio` for load testing


## 