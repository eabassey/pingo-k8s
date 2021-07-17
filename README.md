## Requirements
- Make sure you have a kubernetes cluster running
- Make sure you are pointing to the right config context if you have multiple kubernetes contexts as your target
- Install `helm` on your machine. On Mac just do `brew install helm`
- Lastly make sure you have `kubectl` installed to interact with the kube-api-server.

## Installing Istio
Istio is a service mesh that provides traffic management, Network Security, Observability, and Network Policy management in a microservices environment. To start, install by running:
-  `curl -L https://istio.io/downloadIstio | sh -`
- `cd istio-1.10.2` (or the version you download. Just run `ls` to view the folder name)
- `export PATH=$PWD/bin:$PATH`
You should now have an executable command called `istioctl` in your terminal. So verify by typing `istioctl` and press enter to see if you get any result.
- Install by running `istioctl install --set profile=demo -y`
- Now enable istio-injection on the relevant namespaces to allow istio to inject the sidecar proxy into each pod deployed in those namespaces by running `kubectl label namespace default istio-injection=enabled`, replacing the namespace with the one you want.
- Now go ahead and deploy your workloads onto the cluster.

## Installing API Gateway
Istio comes with an API Gateway out of the box which handles ingress traffic into the cluster and can handle very complex rules. Apply the gateways provided in the `gateways` folder of this project by running `kubectl apply -f ./gateways`. This will setup a gateway for keycloak in the `keycloak` namespace and another for all services in the `pingo-dev` namepace to use.
## Installing Keycloak
Once you are in this directory, just use helm to install keycloak by running `helm install keycloak keycloak -n keycloak --create-namespace` to install keycloak in a separate namespace with all its components. You will have a namespace called `keycloak` afterwards.

## Installing Frontend App
In this same directory, run `helm install pingo-dev-frontend frontend -n pingo-dev --create-namespace` to install the frontend application into the kubernetes cluster under the `pingo-dev` namespace. You can then access the frontend app at `http://pingo.dev.frontend.127.0.0.1.nip.io/`.

## Verify Installations
Just run `kubectl get all --all-namespaces` to verify that all the components are in their right namespaces and are up and running. Also run `istioctl analyze` to make sure there are no issues.

## Accessing Keycloak
If you are using the defaults in this configuration, just go to your browser and reach `http://pingo.dev.keycloak.127.0.0.1.nip.io/`. Your keycloak instance should be running.  The admin credentials are hardcoded in the `keycloak-deployment.yaml` file as `username: admin | password: admin` just for testing. In prod, we can use `Secrets` for them. 

## Installing Telemetry
Istio integrates nicely with several telemetry applications such as `Kiali`, `Prometheus`, `Grafana` and `Jaeger`. Inside the `istio-1.10.2` directory where you run the `istioctl`, run these:
- `kubectl apply -f samples/addons`
- `kubectl rollout status deployment/kiali -n istio-system`
`Note that there might be some race conditions so if there are errors, try run the addon command again`
Once done, you can access the dashboard using `istioctl dsahboard kiali`.
To Run prometheus run `istioctl dashboard grafana` and access here ` http://localhost:3000/dashboard/db/istio-mesh-dashboard`. View individual service traffic going to `http://localhost:3000/dashboard/db/istio-service-dashboard`. View the workload dashboard going to `http://localhost:3000/dashboard/db/istio-workload-dashboard`

## Installing ArgoCD for CI/CD


## Installing `fortio` for load testing


## 