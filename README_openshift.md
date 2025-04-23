# Graph Studio operator

## By Altair Engineering Inc.

## About [Graph Studio](https://altair.com/altair-graph-studio)

## Supported tags

* [latest](registry.connect.redhat.com/cambridgesemantics/anzo-operator:latest)

**Project Status:** stable
**Operator Version:** v2

## Prerequisites

* Red Hat Openshift Container Platform on Kubernetes, version >= 4.9
* Kubectl, versions {1.31-1.25}

## Graph Studio Docker Images Used for Deployment
When you deploy Graph Studio using operator, following are the set of images used for actual deployments. We have given reference docker commands to download the latest releases for each of them below.

### Graph Studio Operator

* To download latest release, please use:

   ```sh
   docker pull registry.connect.redhat.com/cambridgesemantics/anzo-operator
   ```

### Graph Studio

* To download latest release, please use:

   ```sh
   docker pull registry.connect.redhat.com/cambridgesemantics/anzo
   ```

## Steps to deploy Graph Studio (anzo-operator)
For deploying Graph Studio operator and CRs managed by it, user needs to enable RBAC and configure kubernetes objects mentioned below.

```sh
# Create Namespace, mention namespace in metadata.name
$ kubectl create -f deploy/v1_namespace_default.yaml
# Setup Service Account
$ kubectl create -f deploy/default_v1_serviceaccount_anzo-operator.yaml --namespace <namespace>
# Setup RBAC
$ kubectl create -f deploy/default_rbac.authorization.k8s.io_v1_role_anzo-operator.yaml  --namespace <namespace>
$ kubectl create -f deploy/default_rbac.authorization.k8s.io_v1_rolebinding_anzo-operator.yaml  --namespace <namespace>
$ kubectl create -f deploy/rbac.authorization.k8s.io_v1_clusterrole_anzo-operator.yaml
$ kubectl create -f deploy/default_rbac.authorization.k8s.io_v1_rolebinding_anzo-operator-clusterrole.yaml  --namespace <namespace>
# Setup the CRD
$ kubectl create -f deploy/crds/apiextensions.k8s.io_v1_customresourcedefinition_anzos.anzo.cambridgesemantics.com.yaml
$ kubectl create -f deploy/crds/apiextensions.k8s.io_v1_customresourcedefinition_anzobackups.anzobackup.cambridgesemantics.com.yaml
# Deploy anzo-operator
$ kubectl create -f deploy/default_apps_v1_deployment_anzo-operator.yaml --namespace <namespace>
# Deploy Graph Studio Custom Resource(CR), i.e. Graph Studio deployment
$ kubectl apply -f deploy/default_anzo.cambridgesemantics.com_v1_anzo_agent01.yaml --namespace <namespace>
```

**NOTE** One needs to edit operator deployment, CR deployment with right docker image details.

## Steps to delete Graph Studio CR and operator

```sh
# Delete Graph Studio CR
kubectl delete -f deploy/default_anzo.cambridgesemantics.com_v1_anzo_agent01.yaml --namespace <namespace>
# Delete anzo-operator
kubectl delete -f deploy/default_apps_v1_deployment_anzo-operator.yaml --namespace <namespace>
# Delete RBAC
kubectl delete -f deploy/default_rbac.authorization.k8s.io_v1_role_anzo-operator.yaml --namespace <namespace>
kubectl delete -f deploy/default_rbac.authorization.k8s.io_v1_rolebinding_anzo-operator.yaml --namespace <namespace>
kubectl delete -f deploy/rbac.authorization.k8s.io_v1_clusterrole_anzo-operator.yaml --namespace <namespace>
kubectl delete -f deploy/default_rbac.authorization.k8s.io_v1_rolebinding_anzo-operator-clusterrole.yaml  --namespace <namespace>
# Delete Service Account
kubectl delete -f deploy/default_v1_serviceaccount_anzo-operator.yaml --namespace <namespace>
# Delete CRD
kubectl delete -f deploy/crds/apiextensions.k8s.io_v1_customresourcedefinition_anzos.anzo.cambridgesemantics.com.yaml
kubectl delete -f deploy/crds/apiextensions.k8s.io_v1_customresourcedefinition_anzobackups.anzobackup.cambridgesemantics.com.yaml
```

## Graph Studio CustomResource(CR) Specification

The following table lists the configurable parameters for Graph Studio and their default values.(CR API Version: v2)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `metadata.name` | Name of CR | |
| `metadata.namespace` | Namespace of CR | |
| `metadata.labels` | Dictionary of (key: val) as labels of CR | |
| `spec.nodeConfig.spec` | Configuration specification for Graph Studio | |
| `spec.nodeConfig.spec.replicas` | Number of pods for Graph Studio Agent or Server | 1 |
| `spec.nodeConfig.spec.serviceName` | Name of headless service for Graph Studio | anzo-<metadata.name> |
| `spec.nodeConfig.spec.template.spec.serviceAccountName` | Service account name for pods | anzo-operator |
| `spec.nodeConfig.spec.template.spec.containers.x.Name` | Name of Graph Studio container | anzo |
| `spec.service` | Graph Studio loadbalancer service attributes, of type v1.Service | commented, please uncomment to add value |
| `spec.volumes` | List of persistent volumes for Graph Studio | commented, please uncomment to add value |
| `spec.volumes.[i].name` | Name for persistent volume | |
| `spec.volumes.[i].mountPath` | Path where persistent volume should be mounted inside container | |
| `spec.volumes.[i].pv` | Attributes to configure persistent volume, of type v1.PersistentVolume | |
| `spec.volumes.[i].pvc` | Attributes to configure persistent volume claim, of type v1.PersistentVolumeClaim | |
| `spec.volumes.[i].deletePVC` | Set this to true if you want to delete PVC after CR deletion | false |
| `spec.role` | Role of Graph Studio, AnzoAgent or AnzoServer | |
| `spec.credentials` | Existing credentials for AnzoAgent or AnzoServer | commented, please uncomment to add value |
| `spec.jsonActivation` | Set this to true if Graph Studio should be activated using JSON data file | |
| `spec.bootProperties` | Dictinary having <filename: filecontent> combination to configure boot properties at Graph Studio installation | |
| `spec.jvmMemory` | Amount of memory that should be given for JVM processing | (Pod Memory - 1204M) |
| `spec.licensing` | User provided licensing information | commented, please uncomment to add value |
| `spec.licensing.licenseType` | license type to use, AltairOne or CSI | commented, please uncomment to add value |
| `spec.licensing.altairOne` | AltairOne licensing information | commented, please uncomment to add value |
| `spec.licensing.altairOne.authCode` | AltairOne authentication code | commented, please uncomment to add value |
| `spec.licensing.altairOne.credentials` | AltairOne credentials | commented, please uncomment to add value |
| `spec.licensing.altairOne.credentials.username` | AltairOne username | commented, please uncomment to add value |
| `spec.licensing.altairOne.credentials.password` | AltairOne password | commented, please uncomment to add value |
| `spec.licensing.altairOne.licenseFile` | AltairOne license file | commented, please uncomment to add value |
| `spec.licensing.altairOne.licenseServer` | AltairOne internal license server | commented, please uncomment to add value |
| `spec.licensing.altairOne.proxy` | AltairOne internal license server | commented, please uncomment to add value |
| `spec.licensing.altairOne.proxy.credentials` | AltairOne license proxy credentials | commented, please uncomment to add value |
| `spec.licensing.altairOne.proxy.credentials.username` | AltairOne license proxy username | commented, please uncomment to add value |
| `spec.licensing.altairOne.proxy.credentials.password` | AltairOne license proxy password | commented, please uncomment to add value |
| `spec.licensing.altairOne.proxy.host` | AltairOne proxy server host | commented, please uncomment to add value |
| `spec.licensing.altairOne.proxy.port` | AltairOne proxy server port | commented, please uncomment to add value |

## References

```https://docs.cambridgesemantics.com/```
