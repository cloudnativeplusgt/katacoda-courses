# Preparations #

There are a few things to prepare before installing OpenFaaS.

## Namespaces ##

Create and configure two namespaces, one for the OpenFaaS core services _openfaas_ and a second for the functions _openfaas-fn_.

`kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml`{{execute}}

The created namespaces may be listed.

`kubectl get namespaces`{{execute}}

If you are curious, scan the contents of the namespace creating YAML. It does more than just creating the namespaces.

`curl https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml`{{execute}}.

## OpenFaaS Access Secret ##

Generate and submit a Kubernetes secret for basic authentication for the gateway. The secret is named _basic-auth_ and OpenFaaS will use that key when it prompts you for access.

`PASSWORD=$(head -c 12 /dev/urandom | shasum | cut --delimiter=' ' --fields=1 | head -c 4)`{{execute}}

The random password is shortened to just 4 characters for these demonstration purposes. Normally `head -c 4` would be omitted.

`kubectl -n openfaas create secret generic basic-auth --from-literal=basic-auth-user=user --from-literal=basic-auth-password=$PASSWORD`{{execute}}

## Container Registry ##

OpenFaaS requires a Docker registry during the build, push and deploy phases. It defaults to using Docker Hub, but there is no need to shuttle private images over the internet. Instead we keep all this pushing and pulling in a local registry.

There are many options for standing up a container registry. With Minikube there is a registry addon. However, we prefer a more pure Kubernetes solution and install a registry through the [stable Helm chart](https://github.com/helm/charts/tree/master/stable/docker-registry#docker-registry-helm-chart).

`helm install stable/docker-registry --name registry --namespace kube-system --set service.type=NodePort --set service.nodePort=31500`{{execute}}

The registry is now available as a service. It can be seen in Minikube

`minikube service list --namespace kube-system`{{execute}}

Assign an environment variable to the common registry location.

`export REGISTRY=[[HOST_SUBDOMAIN]]-31500-[[KATACODA_HOST]].environments.katacoda.com`{{execute}}

It will be a few moments before the registry deployments reports its _Available_.

`kubectl get deployments registry-docker-registry --namespace kube-system`{{execute}}

Once the registry is serving, inspect the contents of the empty registry.

`curl $REGISTRY/v2/_catalog`{{execute}}