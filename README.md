# Graph Studio Operator (anzo-operator)

## By Altair Engineering Inc.

## About [Graph Studio](https://altair.com/altair-graph-studio)

Graph Studio is a modern data discovery and integration platform that lets anyone find, connect and blend any enterprise data into analytics-ready datasets.
Graph Studio’s unique use of semantics and graph data models makes it practical for the first time for virtually anyone in your organization – from skilled data scientists to novice business users – to drive the data discovery and integration process and build their own analytics-ready datasets.

The Graph Studio Agent works in concert with the Microservice Leader and Graph Studio Unstructured to perform ingestion of unstructured data into RDF suitable for use in an Graph Studio Data Fabric. The Graph Studio Agent allows the processing of semantic service-based workloads to be distributed away from the primary Graph Studio Server, while allowing those services full access to the primary system datasource. Semantic Services that support distribution onto an agent include: Distributed Unstructured, Microservice Client, and ElasticSearch. Use of the Graph Studio Agent requires a licensed Graph Studio Server installation.

**Project Status** stable
**Operator Version** v2
## Supported tags

| Release | Tags                                       |
| :---:   | :---                                       |
|  3.1.0   |  3.1.0, 3.1.0-{{ build }}  |
|  2.0.7   |  2.0.7, 2.0.7-{{ build }}  |
|  2.0.6   |  2.0.6, 2.0.6-{{ build }}  |
|  2.0.5   |  2.0.5, 2.0.5-{{ build }}  |
|  2.0.4   |  2.0.4, 2.0.4-{{ build }}  |
|  2.0.3   |  2.0.3, 2.0.3-{{ build }}  |
|  2.0.2   |  2.0.2, 2.0.2-{{ build }}  |
|  2.0.1   |  2.0.1, 2.0.1-{{ build }}  |
|  2.0.0   |  2.0.0, 2.0.0-{{ build }}  |
|  1.1.0   |  1.1.0, 1.1.0-{{ build }}  |

## Prerequisites

* Kubernetes cluster, versions {1.31-1.25}
* Kubectl, versions {1.31-1.25}

## Setting up prerequisites

## Steps to deploy anzo-operator

**NOTE**: You must get following docker images for deployment

### Graph Studio Operator

* Path of Docker Hub: ```https://hub.docker.com/r/cambridgesemantics/anzo-operator```

* Command to download the latest release, please use: ```docker pull cambridgesemantics/anzo-operator```

### Graph Studio

* Path of Docker Hub: ```https://hub.docker.com/r/cambridgesemantics/anzo```

* Command to download the latest release, please use: ```docker pull cambridgesemantics/anzo```

## Connect to Kubernetes cluster

You can connect to Kubernetes cluster hosted in public cloud service providers such as AWS, Google, Microsoft Azure, after adding/updating ```${HOME}/.kube/config``` file on workstation where ```kubectl``` is installed.

* For AWS, install ```aws-cli``` and use:

 ```aws eks --region <region-name> update-kubeconfig --name <EKS-cluster-name>```

* For Google, use:

 ```gcloud container clusters get-credentials <GKE-cluster-name> --zone <zone-name> --project <project-name> --region <region-name>```

   To install gcloud, please refer to ```https://cloud.google.com/sdk/install```

* For Microsoft Azure, use:

 ```az aks get-credentials --resource-group <resource-group-name> --name <AKS-cluster-name>```

   To install azure cli, please refer to ```https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest```

## RBAC Prerequisites

For deploying Graph Studio operator and CRs managed by it, user needs to enable RBAC and configure kubernetes objects mentioned below.

```sh
# Create Namespace, mention namespace in metadata.name
$ kubectl create -f deploy/v1_namespace_default.yaml
# Setup Service Account
$ kubectl create -f deploy/default_v1_serviceaccount_anzo-operator.yaml --namespace <namespace>
# Setup RBAC
$ kubectl create -f deploy/default_rbac.authorization.k8s.io_v1_role_anzo-operator.yaml  --namespace <namespace>
$ kubectl create -f deploy/default_rbac.authorization.k8s.io_v1_rolebinding_anzo-operator.yaml  --namespace <namespace>
$ kubectl create -f deploy/rbac.authorization.k8s.io_v1_clusterrolebinding_anzo-operator.yaml
$ kubectl create -f deploy/rbac.authorization.k8s.io_v1_clusterrole_anzo-operator.yaml

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
kubectl delete -f deploy/rbac.authorization.k8s.io_v1_clusterrolebinding_anzo-operator.yaml
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
| `metadata.name` | Name of CR | a01 |
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
