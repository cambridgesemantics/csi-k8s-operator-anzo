# anzo-operator

## By Cambridge Semantics Inc.

## Supported tags

* [1.1.0](https://hub.docker.com/layers/cambridgesemantics/anzo-operator/1.1.0-202012150734/images/sha256-95303484429a060637782dae1a2a48e58d968d2abbcefba9d16d33604147b050?context=explore)

## About [Anzo](https://www.cambridgesemantics.com/product/)

Anzo is a modern data discovery and integration platform that lets anyone find, connect and blend any enterprise data into analytics-ready datasets.
Anzo’s unique use of semantics and graph data models makes it practical for the first time for virtually anyone in your organization – from skilled data scientists to novice business users – to drive the data discovery and integration process and build their own analytics-ready datasets.

The Anzo Agent works in concert with the Microservice Leader and Anzo Unstructured to perform ingestion of unstructured data into RDF suitable for use in an Anzo Data Fabric. The Anzo Agent allows the processing of semantic service-based workloads to be distributed away from the primary Anzo Server, while allowing those services full access to the primary system datasource. Semantic Services that support distribution onto an agent include: Distributed Unstructured, Microservice Client, and ElasticSearch. Use of the Anzo Agent requires a licensed Anzo Server installation.

**Project Status** beta
**Operator Version** v1beta1

## Prerequisites

* Kubernetes cluster, versions {1.18-1.14}
* Kubectl, versions {1.18-1.14}

## Setting up prerequisites

## Steps to deploy anzo-operator

**NOTE**: You must get following docker images for deployment

### Anzo Operator

* Path of Docker Hub: ```https://hub.docker.com/r/cambridgesemantics/anzo-operator```

* Command to download the latest release, please use:

    ```sh
   docker pull cambridgesemantics/anzo-operator
   ```

### Anzo

* Path of Docker Hub: ```https://hub.docker.com/r/cambridgesemantics/anzo```

* Command to download the latest release, please use:

    ```sh
   docker pull cambridgesemantics/anzo
   ```

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

For deploying Anzo operator and CRs managed by it, user needs to enable RBAC and configure kubernetes objects mentioned below.

```sh
# Create Namespace
$ kubectl create -f deploy/namespace.yaml
# Setup Service Account
$ kubectl create -f deploy/service_account.yaml
# Setup RBAC
$ kubectl create -f deploy/role.yaml
$ kubectl create -f deploy/role_binding.yaml
$ kubectl create -f deploy/cluster_role.yaml
$ kubectl create -f deploy/cluster_role_binding.yaml
$ kubectl create -f deploy/psp.yaml
# Setup the CRD
$ kubectl create -f deploy/crds/anzo.cambridgesemantics.com_anzos_crd.yaml
# Deploy anzo-operator
$ kubectl create -f deploy/operator.yaml
# Deploy Anzo Custom Resource(CR), i.e. Anzo deployment
$ kubectl apply -f deploy/crds/anzo.cambridgesemantics.com_v1beta1_anzo_cr.yaml
```

**NOTE** One needs to edit deploy/operator.yaml, deploy/crds/anzo.cambridgesemantics.com_v1beta1_anzo_cr.yaml with right docker image details. Also, namespace needs to be specified in other YAML files.

## Steps to delete Anzo CR and anzo-operator

```sh
# Delete Anzo CR
kubectl delete -f deploy/crds/anzo.cambridgesemantics.com_v1beta1_anzo_cr.yaml
# Delete anzo-operator
kubectl delete -f deploy/operator.yaml
# Delete RBAC
kubectl delete -f deploy/role.yaml
kubectl delete -f deploy/role_binding.yaml
kubectl delete -f deploy/cluster_role.yaml
kubectl delete -f deploy/cluster_role_binding.yaml
kubectl delete -f deploy/psp.yaml
# Delete Service Account
kubectl delete -f deploy/service_account.yaml
# Delete CRD
kubectl delete -f deploy/crds/anzo.cambridgesemantics.com_anzos_crd.yaml
```

## Anzo CustomResource(CR) Specification

The following table lists the configurable parameters for Anzo and their default values.(CR API Version: v1beta1)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `metadata.name` | Name of CR | a01 |
| `metadata.namespace` | Namespace of CR | |
| `metadata.labels` | Dictionary of (key: val) as labels of CR | |
| `spec.serviceAccountName` | Name of service account for deploying CR | anzo-operator |
| `spec.role` | Role of Anzo, AnzoAgent or AnzoServer | |
| `spec.image.registry` | Docker image registry for Anzo All-in-One image | docker.io |
| `spec.image.name` | Docker image name for Anzo All-in-one image | cambridgesemantics/anzo |
| `spec.image.tag` | Docker image tag for Anzo All-in-one image | latest |
| `spec.image.pullPolicy` | Docker image pull policy for Anzo All-in-one image | IfNotPresent |
| `spec.size` | Size of Anzo pod, only single pod allowed | 1 |
| `spec.resources.requests.cpu` | Resource request for Anzo container, number of CPUs | 2000m |
| `spec.resources.requests.memory` | Resource request for Anzo container, memory size in MB | 7340M |
| `spec.resources.limits.cpu` | Resource limit for Anzo container, number of CPUs | 16000m |
| `spec.resources.limits.memory` | Resource limit for Anzo container, memory size in MB | 50331M |
| `spec.tolerations` | Anzo database pod tolerations | |
| `spec.affinity` | Anzo database pod node affinity | |
| `spec.service` | Database loadbalancer service attributes, of type v1.Service | commented, please uncomment to add value |
| `spec.volumes` | List of persistent volumes for Anzo DB | commented, please uncomment to add value |
| `spec.volumes.[i].name` | Name for persistent volume | |
| `spec.volumes.[i].mountPath` | Path where persistent volume should be mounted inside container | |
| `spec.volumes.[i].pv` | Attributes to configure persistent volume, of type v1.PersistentVolume | |
| `spec.volumes.[i].pvc` | Attributes to configure persistent volume claim, of type v1.PersistentVolumeClaim | |
| `spec.license` | User provided license string | "" |
| `spec.credentials` | Existing credentials for AnzoAgent or AnzoServer | commented, please uncomment to add value |
| `spec.macID` | Anzo Mac ID associated with licese | |
| `spec.jsonActivation` | Set this to true if Anzo should be activated using JSON data file | |
| `spec.bootProperties` | Dictinary having <filename: filecontent> combination to configure boot properties at Anzo installation | |
| `spec.jvmMemory` | Amount of memory that should be given for JVM processing | (Pod Memory - 1204M) |

## References

```https://docs.cambridgesemantics.com/```
