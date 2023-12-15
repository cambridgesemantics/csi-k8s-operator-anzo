# anzo-operator

## By Cambridge Semantics Inc.

## Supported tags

* [latest](registry.connect.redhat.com/cambridgesemantics/anzo-operator:latest)

**Project Status:** stable
**Operator Version:** v1

## Prerequisites

* Red Hat Openshift Container Platform on Kubernetes, version >= 4.3
* Kubectl, versions {1.28-1.16}

## Anzo Docker Images Used for Deployment
When you deploy Anzo using operator, following are the set of images used for actual deployments. We have given reference docker commands to download the latest releases for each of them below.

### Anzo Operator

* To download latest release, please use:

   ```sh
   docker pull registry.connect.redhat.com/cambridgesemantics/anzo-operator
   ```

### Anzo

* To download latest release, please use:

   ```sh
   docker pull registry.connect.redhat.com/cambridgesemantics/anzo
   ```

## Steps to deploy anzo-operator
For deploying Anzo operator and CRs managed by it, user needs to enable RBAC and configure kubernetes objects mentioned below.

```sh
# Create Namespace, mention namespace in metadata.name
$ kubectl create -f deploy/v1_namespace_default.yaml
# Setup Service Account
$ kubectl create -f deploy/default_v1_serviceaccount_anzo-operator.yaml --namespace <namespace>
# Setup RBAC
$ kubectl create -f deploy/default_rbac.authorization.k8s.io_v1_role_anzo-operator.yaml  --namespace <namespace>
$ kubectl create -f deploy/default_rbac.authorization.k8s.io_v1_rolebinding_anzo-operator.yaml  --namespace <namespace>
$ kubectl create -f deploy/rbac.authorization.k8s.io_v1_clusterrole_anzo-operator.yaml

$ kubectl create -f deploy/rbac.authorization.k8s.io_v1_clusterrolebinding_anzo-operator.yaml
$ kubectl create -f deploy/policy_v1beta1_podsecuritypolicy_anzo-privileged.yaml
# Setup the CRD
$ kubectl create -f deploy/crds/apiextensions.k8s.io_v1_customresourcedefinition_anzos.anzo.cambridgesemantics.com.yaml
# Deploy anzo-operator
$ kubectl create -f deploy/default_apps_v1_deployment_anzo-operator.yaml --namespace <namespace>
# Deploy Anzo Custom Resource(CR), i.e. Anzo deployment
$ kubectl apply -f deploy/default_anzo.cambridgesemantics.com_v1_anzo_agent01.yaml --namespace <namespace>
```

**NOTE** One needs to edit operator deployment, CR deployment with right docker image details.

## Steps to delete Anzo CR and anzo-operator

```sh
# Delete Anzo CR
kubectl delete -f deploy/default_anzo.cambridgesemantics.com_v1_anzo_agent01.yaml --namespace <namespace>
# Delete anzo-operator
kubectl delete -f deploy/default_apps_v1_deployment_anzo-operator.yaml --namespace <namespace>
# Delete RBAC
kubectl delete -f deploy/default_rbac.authorization.k8s.io_v1_role_anzo-operator.yaml --namespace <namespace>
kubectl delete -f deploy/default_rbac.authorization.k8s.io_v1_rolebinding_anzo-operator.yaml --namespace <namespace>
kubectl delete -f deploy/rbac.authorization.k8s.io_v1_clusterrole_anzo-operator.yaml --namespace <namespace>
kubectl delete -f deploy/rbac.authorization.k8s.io_v1_clusterrolebinding_anzo-operator.yaml
kubectl delete -f deploy/policy_v1beta1_podsecuritypolicy_anzo-privileged.yaml
# Delete Service Account
kubectl delete -f deploy/default_v1_serviceaccount_anzo-operator.yaml --namespace <namespace>
# Delete CRD
kubectl delete -f deploy/crds/apiextensions.k8s.io_v1_customresourcedefinition_anzos.anzo.cambridgesemantics.com.yaml
```

## Anzo CustomResource(CR) Specification

The following table lists the configurable parameters for Anzo and their default values.(CR API Version: v1)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `metadata.name` | Name of CR | a01 |
| `metadata.namespace` | Namespace of CR | |
| `metadata.labels` | Dictionary of (key: val) as labels of CR | |
| `spec.nodeConfig.spec` | Configuration specification for Anzo | |
| `spec.nodeConfig.spec.replicas` | Number of pods for Anzo Agent or Server | 1 |
| `spec.nodeConfig.spec.serviceName` | Name of headless service for Anzo | anzo-<metadata.name> |
| `spec.nodeConfig.spec.template.spec.serviceAccountName` | Service account name for pods | anzo-operator |
| `spec.nodeConfig.spec.template.spec.containers.x.Name` | Name of Anzo container | anzo |
| `spec.service` | Anzo loadbalancer service attributes, of type v1.Service | commented, please uncomment to add value |
| `spec.volumes` | List of persistent volumes for Anzo | commented, please uncomment to add value |
| `spec.volumes.[i].name` | Name for persistent volume | |
| `spec.volumes.[i].mountPath` | Path where persistent volume should be mounted inside container | |
| `spec.volumes.[i].pv` | Attributes to configure persistent volume, of type v1.PersistentVolume | |
| `spec.volumes.[i].pvc` | Attributes to configure persistent volume claim, of type v1.PersistentVolumeClaim | |
| `spec.volumes.[i].deletePVC` | Set this to true if you want to delete PVC after CR deletion | false |
| `spec.role` | Role of Anzo, AnzoAgent or AnzoServer | |
| `spec.license` | User provided license string | "" |
| `spec.credentials` | Existing credentials for AnzoAgent or AnzoServer | commented, please uncomment to add value |
| `spec.macID` | Anzo Mac ID associated with licese | |
| `spec.jsonActivation` | Set this to true if Anzo should be activated using JSON data file | |
| `spec.bootProperties` | Dictinary having <filename: filecontent> combination to configure boot properties at Anzo installation | |
| `spec.jvmMemory` | Amount of memory that should be given for JVM processing | (Pod Memory - 1204M) |
| `spec.skipLbCheck` | Set this to true if you want to disable the check of loadbalancer | false |

## References

```https://docs.cambridgesemantics.com/```
