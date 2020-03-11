There is a server side component to KUDO. This server side component, is itself a Kubernetes Operator as is manages all KUDO based operatorxs.

`kubectl kudo init --wait`{{execute}}

The `init` instruction will:

- Deploy KUDO CRDs
- Create kudo-system namespace
- Deploy the KUDO controller

Verify the KUDO controller is initialized when the `kudo-controller-manager-0` Pod has a **Running** status.

`kubectl get -n kudo-system pods`{{execute}}

Also, new custom resource definitions associated with the KUDO controller are also available.

`kubectl get crds`{{execute}}