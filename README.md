# Cluster Repository

This repository represents a single cluster repository.
The conventions and best practices used here make it work seamlessly with [Syncier Security Tower](https://app.securitytower.io/).

## Directory Layout

``` text
.
├── .securitytower
│   ├── cluster.yaml
│   ├── demo-app.yaml
│   └── ...
├── cluster
│   ├── metadata
|   |   └── sealed-secret-cert.pem
│   |   └── kubeconfig.yaml
│   ├── global
│   │   └── policies
│   │   └── common-specs
│   │   └── namespace-specs
│   |       └── foo
│   |       |   └── namespace.yaml
│   |       └── bar
│   |           └── namespace.yaml
│   ├── namespaces
│   |   ├── kube-system
│   |   │   └── sealed-secrets
|   |   |       └── controller.yaml
│   |   ├── foo
│   |   │   └── deployment.yaml
│   |   └── bar
│   |       └── deployment.yaml
└── README.md
```

### `/.securitytower`

[Syncier Security Tower](https://app.securitytower.io/) reads all configuration files in this directory regardless of their names.
The cluster configuration is mandatory.
In addition, applications and their stages are stored in this directory.

More information about the structure of these files can be found here:

- <https://securitytower.syncier.com/docs/reference/clusters>
- <https://securitytower.syncier.com/docs/reference/applications>

### `/cluster`

This directory contains all manifests that belong to the Kubernetes cluster.

### `/cluster/metadata`

If you want to serve a [kubeconfig file](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) for your cluster via [Syncier Security Tower](https://app.securitytower.io/), a `kubeconfig.yaml` must reside in this directory.
This directory is also meant to store certificates containing the public key used for [sealed secrets](https://github.com/bitnami-labs/sealed-secrets).

### `/cluster/global`

This directory contains all manifests with cluster-scope.

### `/cluster/global/common-specs`

This is a good place to define things like _ClusterRole_, _PriorityClass_ or _StorageClass_ for your Kubernetes cluster.

### `/cluster/global/namespace-specs`

The `namespace.yaml` for each namespace is stored within `<name>/namespace.yaml`

### `/cluster/global/policies`

The directory layout for an example policy looks like:

``` text
└── policies
    ├── constraint-templates
    │   └── enforceimageversion-template.yaml
    ├── constraints
    │   └── enforceimageversion-constraint.yaml
    └── enforceimageversion.policy
 ```

#### Policy manifests

The policies root directory contains a `.policy` file for every policy activated via [Syncier Security Tower](https://app.securitytower.io/).
It contains metadata and references other files belonging to that policy.

#### Constraints and constraint templates

A short introduction to OPA Gatekeeper constraints and constraint templates can be found at <https://open-policy-agent.github.io/gatekeeper/website/docs/howto/>.

For more information about working with policies see <https://securitytower.syncier.com/docs/reference/policies>.

### `/cluster/namespaces`

This directory contains all manifests that reside inside a namespace.
This excludes the `namespace.yaml` itself, which is stored in `/cluster/global/namespace-specs`.

In the following example we place some resources defined in the `controller.yaml` file into the `kube-system` namespace.
Therefore, `metadata.namespace` field value must align with the respective directory name in `namespaces/`.
In our example it is `kube-system`.
Subdirectories, such as `sealed-secrets`, are allowed.

``` text
.
├── cluster
│   └── namespaces
│   |   ├── kube-system
│   |   │   └── sealed-secrets
|   |   |       └── controller.yaml
```
