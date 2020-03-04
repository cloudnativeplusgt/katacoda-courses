You will be creating a new Kubernetes resource `kind` that defines a Thermometer. Let's begin by creating a YAML file that declares a simple Thermometer.

```cat <<EOF > thermometer-crd.yaml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: thermometers.d2iq.com
spec:
  group: d2iq.com
  version: v1
  names:
    kind: Thermometer
    plural: thermometers
    shortNames:
      - therm
  scope: Namespaced
EOF```{{execute}}

In the above statement the `cat <<EOF >` command created a new YAML file in your current directory.

`cat thermometer-crd.yaml`{{execute}}

In this CRD definition the Kind is CustomResourceDefinition and the CRD is scoped to namespaces. We also provide the plural and short alias names for the same resource. Later we will get to defining other attributes like units.

Before we give this declaration to your cluster, let's see what is currently on the cluster. There should be no pre-existing CRDs.

`kubectl get crds`{{execute}}

And of course, Kubernetes has no knowledge of a Thermometer and a small error ensues.

`kubectl get thermometers`{{execute}}

Now, give the thermometer declaration to Kubernetes.

`kubectl apply -f thermometer-crd.yaml`{{execute}}

Notice the `apply` command was used instead of the `create` because we will be applying additional upgrades to the CRD in the subsequence steps. Kubernetes is now aware of this new resource.

`kubectl get crds`{{execute}}

Thermometer behaves like any other resource.

`kubectl explain thermometer`{{execute}}

The resource is also available through the Kubernetes API.

`kubectl get -v=9 --raw /api/v1/thermometers | jq .`{{execute}}

Notice in the last command we added a [verbosity request](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) `-v=9`. With it set to level 9 (highest) we get a bit more insight into how the `kubectl` command is obtaining the resource information.

The CRD definition just defines the resource type and while Kubernetes recognizes the type, there are no instances of the Thermometer resource type.

`kubectl get therm`{{execute}}

In the next step, let's add a Thermometer based on this type.